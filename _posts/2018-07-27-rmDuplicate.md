---
layout: post
title: 数组去重方法总结
categories: [blog ]
tags: [ ]
description: remove duplicate
---

### 前言

最近一段时间项目中都没写到Vue和ES6，对ES6的部分内容已经有点生疏，最近项目进测试阶段终于有点时间翻看之前写关于ES6的笔记。ES6中的`Set`可以数组去重，当时便总结了几种去重的方法，现重新整理补充成此文章。

### new Set

第一种当然是`new Set()`，只用一行代码便能实现数组去重

```
var unique = (a) => [...new Set(a)];
```

这里有几点需要注意，先上图：

![new Set()去重](../img/20180727_set.png)

- Set本身是一个构造函数，用来生成Set数据结构（个人觉得可认为一种对象，如`{1, 2, 3}`），因而返回的不是数组，不能直接拿来当数组使用，将伪数组或类似数组的对象转换为数组的方式有多种方式，这里介绍两种，其余的在以后的文章做总结。

    > 前置条件：`var set = new Set([1,1,2,3]);`
    > `[...set]`
    > `Array.from(set)`

- Set函数可以接受一个数组/伪数组/类似数组的对象，作为参数
- Set内部判断是否相同使用的算法类似于`===`，唯一不同的地方是`NaN`(`NaN`不等于自身，但在Set中两个`NaN`是相等的)，所以图中**1和'1'不等**，**{}和{}不等**
- 其他Set实例的属性和方法参见阮一峰老师的[ES6标准入门](http://es6.ruanyifeng.com/)第13章第一节

### indexOf

![indexOf去重](../img/20180727_indexOf.png)

> `indexOf`内部使用使用的`===`进行判断

### 待补充...











### 基本概念

#### DOM tree

浏览器把获取到的html代码解析成一个**DOM tree**，每个标签对应就是一个节点，根节点就是`document`对象。`DOM tree`包含了所有的html标签，包括`display: none;`的标签节点，还有通过js动态加入的节点

#### CSSOM tree

浏览器把所有的样式解析成样式结构体，在解析的过程会去掉浏览器不能识别的样式，比如IE去除-moz开始的样式（firefox），firefox会去掉_开头的样式

#### render tree

- 浏览器将DOM和CSSOM合并成一个`render tree`，网罗网页上所有可见的DOM内容，以及每个节点的所有CSSOM样式信息。
- `render tree`类似于`DOM tree`，但区别很大，`render tree`能识别样式，`render tree`中每个node都有自己style。
- **`render tree`不包含隐藏的节点（比如：`display: none;`和`head`节点）**，因为这些节点不会用于呈现，而且不影响呈现
- **`render tree`会包含`visibility: hidden;`隐藏的节点**，因为这些节点会影响布局layout，会占有空间
- `render tree`每个节点就是一个个的盒元素
- `render tree`以**多叉树**的形式保存了每个节点的css属性、节点本身属性、以及节点的孩子

<figure>
        <img src="https://lo56ve.github.io/img/render-tree-construction.png">
</figure>

### 浏览器完成的步骤

1. 处理 HTML 标记并构建 DOM 树
2. 处理 CSS 标记并构建 CSSOM 树
3. 将 DOM 与 CSSOM 合并成一个渲染树
4. 根据渲染树来布局，以计算每个节点的几何信息
5. 将各个节点绘制到屏幕上

### 重绘回流

- 重绘：`render tree`一些元素的属性发生改变，不会改变到页面布局，只是影响透明度（opacity），背景色（background-color），则只是引发重绘
- 回流：只要是页面布局（大小宽度位置等）出现了改变，就会引发回流和重绘

### 浏览器机制

```
var s = document.body.style;
s.padding = "2px"; // 回流+重绘
s.border = "1px solid red"; // 再一次 回流+重绘
s.color = "blue"; // 再一次重绘
s.backgroundColor = "#ccc"; // 再一次 重绘
s.fontSize = "14px"; // 再一次 回流+重绘
// 添加node，再一次 回流+重绘
document.body.appendChild(document.createTextNode('abc!'));
```

上述代码进行了6次重绘回流，由于回流的开销不小，如果每条js操作都去重绘回流的话浏览器不堪重负。所以很多浏览器会优化这些操作，浏览器会维护1个队列，把所有引起回流、重绘的操作放入这个队列，等队列中的操作**到了一定数量或者一定时间间隔**，就会flush队列，进行一个批处理，把多次回流重绘变成一次回流重绘

**但是**，有些代码会强制提前flush队列，比如`offsetTop/Left/Width/Height, scrollTop/Left/Width/Height, clientTop/Left/Width/Height, width, getComputedStyle(), currentStyle`，（注：getComputedStyle()获取元素样式，currentStyle获取IE元素样式），因为这些代码需要实时获取到最新的元素在页面的位置信息

### 减少回流重绘

1. 使用classname替换之后dom操作 
2. 使用documentFragment进行缓存操作，只引发一个回流
3. 将需要改变的元素先display:none然后再显示，进行两次回流
4. 不要经常返回引起flush队列的属性，确实需要访问，利用缓存

第4点示例：

```
// 别这样写
for(循环) {
el.style.left = el.offsetLeft + 5 + "px";
el.style.top = el.offsetTop + 5 + "px";
}
// 这样写好点
var left = el.offsetLeft,
top = el.offsetTop,
s = el.style; 
for (循环) { 
left += 10; 
top += 10; 
s.left = left + "px"; 
s.top = top + "px"; 
}
```

