---
title: 原型、原型链
date: 2018-07-12 15:10:06
tags: [js,原型,new的过程]
categories: js
toc: true # 是否启用内容索引
---

![原型链](/img/prototype.jpg)


### 函数声明
```javascript
var f1 = function(){}; // 1. 字面量
function f2 = function(){}; // 2. 关键词  
var f3 = new Function(); // 3. 构造函数
```

归根结底都是通过new Function 的方式来创建的，js中一切皆对象，对象又分函数对象和普通对象，简单来说通过new Function创建的就称之为函数对象，其他的都是普通对象

### 原型对象

在js中每一个对象（普通对象、函数对象）都有一个\_\_proto__ 属性，函数对象都有一个prototype属性，指向函数的**原型对象**

```javascript
var obj = {};
var fn = function(){};
console.log(obj.__proto__); // {constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, …}
console.log(obj.prototype); // undefined 
console.log(fn.__proto__); // ƒ () { [native code] }
console.log(fn.prototype); // {constructor: ƒ}

// 其中 prototype 就是原型对象，当然原型对象也是一个普通对象，它的作用主要是用来实现继承的，举个栗子：
    
var Animal = function(){
    this.name = 'Tom';
}

Animal.prototype.say = function(){
    console.log('I am ' + this.name)
}

var cat = new Animal();
var dog = new Animal();
cat.say();  // I am Tom
dog.say();  // I am Tom
```

这里原型继承的问题，暂且按下不表（皮这一下，很开心~~）

### 原型链

上文说到，每一个对象都有一个\_\_proto__  属性，那么这个属性究竟有什么用呢？先来看几个例子

```javascript
var Animal = function(){
    this.name = 'Tom';
}

var cat = new Animal();

console.log(cat.__proto__ === Animal.prototype); // true
console.log(Animal.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__ === null); // true
```

从这个例子中可以看出 \_\_proto__ 属性指向构造函数的原型对象prototype，而prototype也是一个普通对象，也有\_\_proto__属性，继续指向其构造函数，直到指向null，这样就形成了一个原型指向的链条，专业术语称之为原型链。
至于为什么最终是null，如果非要解释的话，引用道德经中的一句话：有，是万物之所始；无，是万物之所母。
此外，Function、Object作为js的内置函数对象，需要清楚一点，他们都是 new Function创建的

    console.log(Function.constructor === Object.constructor); // true

### new 的实现过程

通过new 一个构造函数得到一个对象，看一下内部实现过程，有助于帮助理解原型链指向

    1. 声明一个对象 var obj = {};
    2. obj.\_\_proto__ = 构造函数.prototype;
    3. 改变this指针，通过call、apply让this指向第一步创建的obj；
    4. 返回对象obj;

```javascript
       function Animal(name){
          this.name = name;
       }

       Animal.prototype.say = function(){
           console.log('I am ' + this.name)
       }

       function myNew() {
           var obj = {}; // 是指还是new，不要纠结，只是模拟new的过程
           var Constructor = [].shift.call(arguments); // 构造函数 Animal
           //constructor属性是在Animal.prototype对象中用来记录它指向的 构造函数 的属性
           console.log(Constructor.prototype); // {say: ƒ, constructor: ƒ} say:ƒ() constructor:ƒ Animal(name) 
           obj.__proto__ = Constructor.prototype; // 让obj.__proto__ 指向 Animal.prototype
           Constructor.apply(obj,arguments); // 让this指向obj
           return obj;
       }

       var cat= myNew(Animal,'Tom');
       console.log(cat.name); // Tom
       cat.say(); // I am Tom
       console.log(cat.constructor); // ƒ Animal(name){ this.name = name; }
```

### 大招

如果以上内容还是不太明白，我自己做了张注解图（线条有点多而杂，见谅~~），以及代码案例帮助理解
![图1.jpg](/img/prototype2.jpg)

```javascript
    // 几个概念： 
    // 普通对象是new Object而来的，所以 它的构造函数就是Object
    // 函数对象是new Function而来的，所以 它的构造函数就是Function，Function、Object是new Function而来的
    // 对象的__proto__ 指向构造函数的prototype
    function Animal(){}
    var cat = new Animal();
    
    cat.constructor == Animal; // true
    cat.__proto__ === Animal.prototype; // true
    
    Animal.constructor === Function; // true
    Animal.prototype.__proto__ === Object.prototype; // true 注：prototype是对象
    Animal.__proto__ === Function.prototype; // true

    Object.constructor  === Function; // true
    Object.__proto__ === Function.prototype; // true
    Object.prototype.__proto__ === null; // true

    Function.constructor === Function; // true
    Function.__proto__ === Function.prototype; // true
    Function.prototype.__proto__ === Object.prototype; // true

```

关于面向对象、继承推荐一篇超赞的文章：[javascript面向对象编程，带你认识封装、继承和多态](http://cherryblog.site/javascript-oop.html)，深度好文，值得一读！