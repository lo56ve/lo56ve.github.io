---
layout: post
title: + - !function(){...}(),function前面符号意思
categories: [blog ]
tags: [ ]
description: IIFE
---

### 声明函数

声明函数的两个方法：

- function foo(){...}	（函数声明）
- var bar = function(){...}		（函数表达式，等号后面是一个匿名函数）

### 除此之外方法

1. var foo = function bar(){...}
2. (function(){...})()
3. +function(){...}()
4. -function(){...}()	
5. !function(){...}()
6. ~function(){...}()
7. void function(){...}()

以上这几种方法都是转换为函数表达式

第1种：转换为`var foo = function(){...}`，函数外无法通过bar访问到函数，在函数内部可以访问到bar，即对这个函数的引用

第2种：`(function(){...})`将函数声明转换为函数表达式，函数表达式后面可以接`()`，即立即执行

第3-7种：也是转换为函数表达式，同时对**返回值**进行处理。

```
+function(){
	console.log(111);
}()

// 111
// NaN
```

因为函数没有返回值，所以返回`undefined`，对`undefined`进行`+`操作，即隐式转换为`Number`类型，结果为`NaN`。`-`与`+`类似。

`!`是对返回值取反，看下面代码：

```
!function(){console.log(111);}		// false
!function(){console.log(111);}()	// true
```

第一个，是对`函数`取反，结果为`false`，第二个是对返回值`undefined`，结果为`true`

`~`是对返回值按位取反

```
~function(){console.log(111);}()		// 111 -1
~function(){console.log(111);return 3}	// 111 -4
```

第一个，对`undefined`按位取反，即`0`取反为`-1`，`3`取反为`-4`
