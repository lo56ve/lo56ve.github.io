---
layout: post
title: js字符串和数组的操作方法
categories: [blog ]
tags: [js ]
description: 熟记操作方法
---

数组和字符串的操作方法一直自己很容易混淆，所以写一个博客记一下

# String

### 1. charAt()和charAtCodeAt()

用于访问字符串中特定字符，这两个字符串都接收一个参数，即基于0的字符位置，`charAt()`是返回给定位置的字符，`charCodeAt()`是返回该字符的编码。

	var stringValue="hello world";
	alert(stringValue.charAt(1));	//输出e
	alert(stringValue.charCodeAt(1));  //输出101

> 还可以通过加[]来获取字符，和数组相似

	stringValue[1]		//输出e

### 2.concat()

concat()方法可以接受任意多个参数，也就是说可以通过它拼接任意多个字符串

	var stringValue="hello ";
	var result=stringValue.concat("world","!");

	alert(stringValue);		//"hello"
	alert(result);			//"hello world!"

上述例子将`world`和`！`拼接到hello之后，而且不会影响到原来的字符串不变

> 使用加号操作符子啊大多数情况下都比使用concat()方法要简便易行（特别是拼接多个字符串的情况下）

### 3.slice()，substr(),concat()

