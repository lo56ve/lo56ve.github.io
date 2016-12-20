---
layout: post
title: 移动端开发页面适配
categories: [blog ]
tags: [ ]
description: 移动端适配问题
---

### 前言

最近做移动端微信端页面开发，发现移动端的坑实在不少，自己踩了自己再填上，爬坑辛苦啊

### 移动端页面适配问题

之前暑假实习的公司，也是做微信公众号开发，当时项目中在移动端布局严格按照UI设计师给的设计稿，在chrome上搞的不亦乐乎，结果拿到真机上面测试，结果有多烂可想而知。

其实在手机端的页面像素因为手机不同而不同，但宽度大都不会超过1080px，如果在微信端还是使用px像素进行页面布局肯定不行。所以要采用其他方法。

<figure>
        <img src="https://lo56ve.github.io/img/6p.jpg">
</figure>

> 这是苹果6 plus的页面像素

**如果是在移动端如微信端开发，最好加上meta**，

	<meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no" />

不然页面中会出现很多意想不到的bug

- width = device-width：宽度等于当前设备的宽度
- initial-scale： 初始的缩放比例（默认设置为1.0）
- minimum-scale：允许用户缩放到的最小比例（默认设置为1.0）
- maximum-scale：允许用户缩放到的最大比例（默认设置为1.0）
- user-scalable：用户是否可以手动缩放（默认设置为no，因为我们不希望用户放大缩小页面）

### 方法一

暑假实习的公司，将页面按苹果4的页面像素比例来做，320*480。字体只有大小和粗细可以改变，其他样式不会改变，微信端的字体以系统为主。

> 在这里建议，在手机页面获取屏幕高度时，尽量使用window.innerHeight，以最大的范围兼容各种浏览器

这种方法虽然能兼容很多移动端页面，但是效果也不是很好，子啊大屏手机上面可能出现缝隙比较大，尽量不使用

### 方法二

可以使用媒体查询，根据不同的手机屏幕宽度，然后划分成几个范围，写不同css样式

	@media screen and (max-width: 960px){      
	body{background:#000;}
	}

这个应该算是一个media的一个标准写法，上面这段CSS代码意思是：当页面小于960px的时候执行它下面的CSS

	@media screen and (min-width:960px) and (max-width:1200px){
	body{background:yellow;}
	}

上面的这段代码的意思是当页面宽度大于960px小于1200px的时候执行下面的CSS

media所有参数汇总

- width:浏览器可视宽度。
- height:浏览器可视高度。
- device-width:设备屏幕的宽度。
- device-height:设备屏幕的高度。
- orientation:检测设备目前处于横向还是纵向状态。
- aspect-ratio:检测浏览器可视宽度和高度的比例。(例如：aspect-ratio:16/9)
- device-aspect-ratio:检测设备的宽度和高度的比例。
- color:检测颜色的位数。（例如：min-color:32就会检测设备是否拥有32位颜色）
- color-index:检查设备颜色索引表中的颜色，他的值不能是负数。
- monochrome:检测单色楨缓冲区域中的每个像素的位数。（这个太高级，估计咱很少会用的到）
- resolution:检测屏幕或打印机的分辨率。(例如：min-resolution:300dpi或min-resolution:118dpcm)。
- grid:检测输出的设备是网格的还是位图设备。

> chrome默认最小的字体为12px，小于12px字体都以12px显示，所以当页面媒体查询执行小于根节点小于12px时候，是不起作用的。可以使用vh和vw来作为单位，vh是view height，vw是view width。vh和vw是将视窗划分为100等分。这是css3中的新特性。

### 方法三

使用rem来做适配

rem是指相对根元素（即html元素）的字体大小的单位，假设根元素的字体大小是10px，则5rem大小为50px

具体做法：根据不同的设备宽度在根元素上设置不同的字体大小，将屏幕的rem定好，这里的rem是1rem为100px（项目中设计稿给的尺寸是1080px），而各个手机屏幕尺寸不一样，所以按比例扩大缩小，所以clientWidth/1080是一个比例，按实际比例计算。

<figure>
        <img src="https://lo56ve.github.io/img/js_rem.png">
</figure>

引入这一个立即执行函数，然后在做页面时候，将设计稿对应的px尺寸改为rem值，1rem为100px，如设计稿500px，在css中设计应为5rem。（注意我这里是以1080px，如果开始的设计稿不是1080px需要改动一下）。

[js_rem](https://github.com/lo56ve/somedemo/tree/master/js_rem)

在移动端开发布局时，通常字体都比较大，在div里面加上一个div时，结果发现外面高度是固定了，里面的div上边不能贴着上边框，，原因应该是默认的字体大小固定了高度，所以在外面的div设置字体大小为0（font-size=0;），去除外面div的字体默认大小撑开的高度

