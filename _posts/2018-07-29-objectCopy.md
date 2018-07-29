---
layout: post
title: 复制对象方法总结
categories: [blog ]
tags: [ ]
description: copy object
---

### 前言

ES6提供了很多实用的api，上一篇博文使用到的扩展运算符`...`，也可用于复制对象的，但是ES6仅用于数组，通过`...`来复制对象在ES7中实现。目前，Babel转码器已经支持这项功能。

### 扩展运算符`...`

先来介绍一下`...`，主要在两个地方：

1. rest参数（形式为"...变量名"）:用于获取**函数**的多余参数，变量是一个**数组**，将函数参数多余的参数放入数组中。

    ```
    function a(b, ...rest) {
        console.log(b);         // 1
        console.log(rest);      // [2, 3, 4]
    }
    a(1, 2, 3, 4);
    ```

    这里有两点需要注意：
    
    - rest参数之后不能有其他参数，即必须是最后一个
    - 上述代码中若通过`a.length`来获取函数参数个数，只能获取rest参数之前参数的个数，即上述代码`a.length`为1

2.  扩展运算符：将一个数组/伪数组/类似数组结构的对象(如Set结构)，转为用逗号分隔的参数序列。文字比较晦涩，看代码就懂了：

    ```
    [...[1, 2, 3], 4]      // [1, 2, 3, 4]，将数组[1, 2, 3]转为`1, 2, 3`，并上后边的4成为新数组[1, 2, 3, 4]
    ```

上述扩展运算符的应用非常广泛，如下：

- Math.max等函数接受的是逐个的数值元素而不是数组

    ```
    Math.max(1, 2, 3)                   // 正常接受的参数
    Math.max.apply(null, [1, 2, 3])     // 接受数组的ES5写法
    Math.max(...[1, 2, 3])              // 接受数组的ES6的写法
    ```

- 合并新数组：

    ```
    [1, ...[2, 3], ...[4, 5]];          // [1, 2, 3, 4, 5]
    ```

- 将字符串转化为数组，类似调用str.split('')

    ```
    [..."hello"]        // [ "h", "e", "l", "l", "o" ]
    ```

- 伪数组转换为数组：

    ```
    [...document.querySelectorAll('div')]       // [<div>, <div>, <div>]
    ```

扯得有点远，开篇介绍了这么多关于ES6扩展运算符的知识（更多知识点参见阮一峰老师的ES6入门，第八章[函数的扩展]第二三节），就是为了引出通过`...`来复制数组/对象的方法，顺便复习一下ES6...

### 复制对象的方法

1. `...`扩展运算符（浅拷贝）

    ```
    const a1 = [1, 2]

    const a2 = [...a1]      // 写法一
    const [...a2] = a1      // 写法二
    ```

    > ES7才支持对象复制，参见ES6入门，第九章[对象的扩展]第九节

2. Object.assign（浅拷贝）

    ```
    const obj1 = {a: {b: 1}};
    const obj2 = Object.assign({}, obj1);

    obj1.a.b = 2;
    obj2.a.b;           // 2 
    ```

    > 浅拷贝，对象中的对象是仅复制引用地址

    > ES6的语法，移动端是解析不了`Object.assign`，需要添加babel插件来解析，参见[https://babeljs.io/docs/en/babel-plugin-transform-object-assign/](https://babeljs.io/docs/en/babel-plugin-transform-object-assign/)

3. 数组slice（浅拷贝）

    ```
    const a1 = [1, 2]
    const a2 = a1.slice();
    ```

    > slice()接受参数可以为空，表示截取从第一个元素到最后一个元素

4. 数组concat（浅拷贝）

    ```
    const a1 = [1, 2];
    const a2 = [].concat(a1);
    ```

5. push结合shift（浅拷贝）

    ```
    const arr = [];
    const obj1 = {a: {b: 1}};
    arr.push(obj1);

    const obj2 = arr.shift();
    ```

6. JSON.stringify结合JSON.parse（深拷贝）

    ```
    const obj1 = {a: {b: 1}};
    const obj2 = JSON.parse(JSON.stringify(obj1));
    ```

    > 如果对象有函数则不能正常复制

7. 递归遍历对象（深拷贝）

    ```

    ```


### 最后

这些方法都是在之前的工作学习中总结下来的，目前总结的深拷贝只有最后两种

 - 优劣
 - 深浅拷贝区别
 - 欢迎在github issue指出错误或者有更好的方法提供