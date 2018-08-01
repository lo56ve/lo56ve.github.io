---
layout: post
title: 判断数组方法总结
categories: [blog ]
tags: [ ]
description: judge Array
---

### 前言

今天是8月1号，日历又往前翻了一页，毕业已经有一年一个月，距离全栈开发的目标还是还差多少？扯远了，今天主要来总结一些判断数组的方法，若有纰漏，望在issues中指出。

数组作为对象的一种，如果单纯靠`typeof`来判断肯定是不行的，`typeof`只能区分基本类型和引用类型（即对象），不过`typeof`判断`null`也是为`object`，这点需要特别注意，算是JavaScript的一个bug。那判断一个对象是否为数组的方法，主要有哪些呢？

### 1.instanceof

MDN上对`instanceof`的理解: `instanceof` 运算符用来测试一个对象在其**原型链**中是否存在一个构造函数的 `prototype` 属性。

```
object instanceof constructor
```

> object：要检测的对象
> constructor：某个构造函数

即检测object的原型链上有没有constructor的prototype

```
function C(){}
function D(){}

var o = new C();

o instanceof C;         // true
o instanceof D;         // false
o instanceof Object;    // true ，所有引用类型默认都继承了Object，所有函数的默认原型都是Object的实例，因此默认原型都会包含一个内部指针，指向`Object prototype`（js高程设计164页）

C.prototype = {}

var i = new C();

o instanceof C;         // false，重写了prototype，指向一个空对象的地址，不存在o的原型链上
i instanceof C;         // true

D.prototype = new C();
var x = new D();
x instanceof D;         // true
x instanceof C;         // true
```

按照上述对于instanceof的理解，判断数组即可用

```
var arr = [1, 2, 3];
arr instanceof Array;   // true 在arr的原型链上有Array的prototype属性
```

### 2.Array.isArray()


### 3.Object.prototype.toString


### 4.arr.constructor

instanceof和constructor实现原理是一样的

### 文章内容待补充...

