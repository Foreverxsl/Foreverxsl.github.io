---
title: 常见ES6属性归档
date: 2018-10-12 16:49:42
categories: front #分类
tags: js
comments: true 
---
1. 箭头函数
可以让this指向固定化，这种特性很有利于封装回调函数
函数的this指向：
箭头函数的this永远指向其上下文的 this，任何方法都改变不了其指向，如call(), bind(), apply()
普通函数的this指向调用它的那个对象
2. Promise
Promise是异步编程的一种解决方案，比传统的解决方案——回调函数和事件监听——更合理和更强大。
所谓Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。Promise 提供统一的 API，各种异步操作都可以用同样的方法进行处理。
Promise对象有以下两个特点:
1) 对象的状态不受外界影响，Promise对象代表一个异步操作，有三种状态：Pending（进行中）、Resolved（已完成，又称 Fulfilled）和Rejected（已失败）
2) 一旦状态改变，就不会再变，任何时候都可以得到这个结果。
<!--more-->
3. var、let、const
var、let、const的区别
var定义的变量，没有块级作用域的概念，可以跨块访问, 不能跨函数访问，声明的变量存在变量提升。
let定义的变量，只能在块作用域里访问，不能跨块访问，也不能跨函数访问，变量不存在变量提升。
const用来定义常量，使用时必须初始化(即必须赋值)，只能在块作用域里访问，而且不能修改，变量不存在变量提升。
变量提升现象：
浏览器在运行代码之前会进行预解析，首先解析函数声明，定义变量，解析完之后再对函数、变量进行运行、赋值等。 
-不论var声明的变量处于当前作用域的第几行，都会提升到作用域的头部。 
-var 声明的变量会被提升到作用域的顶部并初始化为undefined，而let声明的变量在作用域的顶部未被初始化。
函数提升在变量提升之前
4. ES6中继承
Class 可以通过extends关键字实现继承
class Parent { }
class Child1 extends Parent {
constructor(x, y, colors) { 
super(x, y); // 调用父类的constructor(x, y)
this.colors = colors; }
toString() { return this.colors + ' ' + super.toString(); // 调用父类的toString() } }