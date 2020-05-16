---
title: 闭包
date: 2018-07-13 14:31:27
tags: [js,闭包]
categories: js
toc: true # 是否启用内容索引
---

### 作用域

js中变量分为全局变量、局部变量

```javascript
    var a = 1;  // 全局变量
    function A(){
      b = 2;  // 全局变量（严格模式下报错）
      var c = 3; // 局部变量
    };
    A();
    console.log(a); // 1
    console.log(b); // 2
    console.log(c); // Error：c is not defined
```

### 预解析

js会把带有var和function关键字的事先声明，并在存放在栈区中

```javascript
    console.log(a); // undefined
    var a = 1;
    console.log(b); // undefined
    var b = function b(){}; // 本质还是通过var声明
    console.log(c); // ƒ c(){}
    function c(){};
```

疑问？

```javascript
    function out(){
      var a = 1;
    };
    out();
    console.log(a); // Error: a is not defined， 为什么这里报错了，没有预解析
```

### 解析执行环境

在js执行之前是会预解析function 和var 没错，但是在本例中 它们的“解析执行环境”不同.

function out是在一个全局的环境中，而a是function  out中定义的一个局部变量，a的“解析执行环境”是在function out这个函数里面(这里可以理解为function out里的局部环境)，所以当我们在全局这个环境中console.log(a) 的时候，并没有定义a这个变量或者函数，所以就报错了

```javascript
    function out(){
       console.log(a); // undefined
       var a = 1;
    };
    out();
    console.log(a); // Error: a is not defined
```

### 垃圾回收机制-GC

原理：垃圾收集器会定期（周期性）找出那些不在继续使用的变量，然后释放其内存。
不再使用的变量：也就是生命周期结束的变量，当然只可能是局部变量，全局变量的生命周期直至浏览器卸载页面才会结束。

### 闭包

作用：局部变量常驻内存，读取函数内部的变量
构成：函数嵌套，内部函数调用外部函数的变量
缺点：造成内存泄漏

```javascript
    function parent() {
        var a = 1;
        function son() {
            return a++;
        }
        return son();
    }
    console.log(parent()); // 1
```

### 内存泄漏

- 1. 意外的全局变量引起的内存泄漏。

原因：全局变量，不会被回收。

解决：使用严格模式避免。

- 2.  **闭包引起的内存泄漏**

原因：闭包可以维持函数内局部变量，使其得不到释放。

__解决：在退出函数之前，将不使用的局部变量全部删除，如在不用parent函数的时候，parent = null__

- 3.    没有清理的DOM元素引用

原因：虽然别的地方删除了，但是对象中还存在对dom的引用

解决：手动删除。

- 4.    被遗忘的定时器或者回调

原因：定时器中有dom的引用，即使dom删除了，但是定时器还在，所以内存中还是有这个dom。

解决：手动删除定时器和dom。
