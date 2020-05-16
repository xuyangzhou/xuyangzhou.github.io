---
title: vue组件通信及vuex原理解析
date: 2020-03-20 23:47:09
tags:
---
### 组件传值
组件是 vue.js强大的功能之一，而组件实例的作用域是相互独立的，这就意味着不同组件之间的数据无法相互引用。一般来说，组件可以有以下几种关系：

#### 父传子

  1. `props`

```javascript
// 父组件：
<HelloWorld msg="Welcome to Your Vue.js + TypeScript App"/>
    
// 子组件
props: { msg: String } 
```
	
   2. `$attrs` 子组件未在props中声明的属性
    
   >  inheritAttrs: false 
	
  ```html
  <!-- 父组件 -->
  <HelloWorld msg="Welcome to Your Vue.js + TypeScript App" foo="foo"/>
    
  <!-- 子组件 -->
  <p>{{$attrs.foo}}</p>
    
  <input v-bind="$attrs"/> // placeholder type ...
  ```
  
  3. `$refs`: 如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件实例
  
```js
    // 子组件
    export default {
      data () {
        return {
          title: 'I am hw componet'
        }
      },
      methods: {
        say () {
          alert('Hello');
        }
      }
    }
    
    // 父组件
    <HelloWorld :msg="msg" foo="foo" @changeMsg="changeMsg" ref="hw" />
    
     mounted () {
          const hw = this.$refs.hw;
          console.log(hw.title);  // I am hw componet
          comA.sayHello();  // 弹窗 Hello
    }
 ```
	
   4. `$parent` / `$children`：访问父 / 子实例
  
   > $children子元素不保证顺序
  
#### 子传父

  `$emit`、`$on`

```javascript
    // 子组件
    this.$emit('changeMsg')
    
    // 父组件
    <HelloWorld :msg="msg" foo="foo" @changeMsg="changeMsg"/>
    
    changeMsg() {
        this.msg = 'hello world!'
    }
```
#### 兄弟传值： 通过共同的祖辈组件搭桥，$parent或$root

  ```js
  // brother1
  this.$parent.$on('foo', handle)
  // brother2
  this.$parent.$emit('foo')
  ```

#### 祖代传值 

  - `provide`: Object | () => Object
  - `inject`: Array<string> | { [key: string]: string | Symbol | Object }


> `provide` 和 `inject` 主要在开发高阶插件/组件库时使用。并不推荐用于普通应用程序代码中。[原理解析](https://blog.csdn.net/liubangbo/article/details/101198344) 

> 【注意】：**provide 和 inject 绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的**

  ```javascript
provide () {
      return {
        bar: 'provide value'
      }
}
  
// inject: ['bar']
inject: {
    bar: {
        type: string,
        default: ''
    }
}
  ```

- 实现provide与inject响应化

  - `不推荐` rovide祖先组件的实例，然后在子孙组件中注入依赖，这样就可以在子孙组件中`直接修改祖先组件的实例的属性`，不过这种方法有个缺点就是这个实例上挂载很多没有必要的东西比如props，methods

  - `推荐` 2.6.0 新增API Vue.observable 优化响应式
  
    ```ts
    // 父组件
    export default class App extends Vue {
      msg: string = "Welcome to Your Vue.js + TypeScript App";
      // 调用Vue.observable声明响应数据appTheme  
      appTheme: Theme = Vue.observable({
        color: "red"
      });
      @Provide() bar: string = "root message"; // 普通字符
      @Provide() theme: object = this.appTheme; // 响应数据
      @Provide() app: object = this; // 当前组件实例
        
      changeMsg() {
        this.msg = "hello world!";
      }
    }
    
    // 子组件
    export default class HelloWorld extends Vue {
      @Inject({ default: "" }) bar!: string;
  
      @Inject({
      default: () => ({})
      })
      theme!: object;
    
      @Inject()
      app!: any
    
      mounted() {
        console.log(this.theme, this.app);
        this.app.msg = 'hw' // 直接在子组件中修改父组件的属性 (父组件不能响应变化)
        this.app.changeMsg(); // 调用父组件的方法(子组件不能响应变化)
      }
    }
    ```
  
  - EventBus 事件总线
  
    ```js
    // 初始化event-bus.js
    import Vue from 'vue'
    export const EventBus = new Vue()
    
    // comA组件: 派发add事件
    import { EventBus } from "./eventBus/event-bus";
    export default {
      data () {
        return {
          count: 0
        }
      },
      methods: {
        eveAdd() {
        	EventBus.$emit('add', ++this.count)
    	}
      }
    }
    
    // comB组件：监听add事件，展示add之后的结果
    import { EventBus } from "./eventBus/event-bus";
    export default {
      data () {
        return {
          num: 0
        }
      },
      mounted() {
        EventBus.$on("add", count => {
          this.num = count;
        });
      }
    }
    ```
  
    

### Vuex核心概念

1. state 

  - 管理vuex的数据（官方称之为单一状态树）

2. getter

  - state 中派生出一些状态

3. mutation 
  - 更改 Vuex 的 store 中的状态的`唯一`方法是提交 mutation
  - `必须`是同步函数
  
4. action
  - Action 提交的是 mutation，而不是直接变更状态
  - Action 可以包含任意异步操作
  
5. module

  - 模块化（当应用变得特别复杂时，所有状态都会集中到一个较大的对象中，就会变得非常臃肿，按照模块精心分割，每个模块都拥有自己的state、mutation、action、getter）

    ```js
    const moduleA = {
      state: { ... },
      mutations: { ... },
      actions: { ... },
      getters: { ... }
    }
    
    const moduleB = {
      state: { ... },
      mutations: { ... },
      actions: { ... }
    }
    
    const store = new Vuex.Store({
      modules: {
        a: moduleA,
        b: moduleB
      }
    })
    
    store.state.a // -> moduleA 的状态
    store.state.b // -> moduleB 的状态
    ```

- 问题：mutation中执行异步操作会怎么样？

  > 事实上在 vuex 里面 actions 只是一个架构性的概念，并不是必须的，说到底只是一个函数，你在里面想干嘛都可以，只要最后触发 mutation 就行
  >
  > --- [尤雨溪知乎回答](https://www.zhihu.com/question/48759748)

### Vuex [源码](https://github.com/vuejs/vuex)解析

- Vue.use(Vuex) 做了什么？为什么axios不用Vue.use()方法？
- new Vue({store}) store 在这里做了什么？ 为什么？

 ```js
  // vuex.js 实现commit dispatch getter方法

  var Vue
// 实现install方法
function install(_Vue) {
    Vue = _Vue
    Vue.mixin({
        beforeCreate() {
            if (this.$options.store) { // vue的原型上挂载store（Store的实例）
                Vue.prototype.$store = this.$options.store
            }
        }
    })
}
// 实现Store类
class Store {
    constructor(options) {
        this.state = new Vue({
            // 实现state响应化
            data: options.state
        })

        this.mutations = options.mutations || {}
        this.actions = options.actions || {}
        options.getters && this.handleGetters(options.getters)
    }

    commit = (type, arg) => {
        this.mutations[type](this.state, arg)
    }

    // dispatch方法接受一个上下文执行环境，注意与commit区分
    dispatch(type, arg) {
        this.actions[type]({
            state: this.state,
            commit: this.commit,
            getters: this.getters
        }, arg)
    }

    handleGetters(getters) {
        this.getters = {}
        Object.keys(getters).forEach(key => {
            // 利用defineProtperty将getters的执行结果映射成对象
            Object.defineProperty(this.getters, key, {
                get: () => {
                    return getters[key](this.state)
                }
            })
        })
    }
 }

 export default {
    Store,
    install
 }
 ```

