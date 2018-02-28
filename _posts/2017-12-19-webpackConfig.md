---
layout: post
title: webpack打包配置相关
categories: [blog ]
tags: [ ]
description: webpack
---

`webpack`很强大，但也很多复杂，很多功能通过配置及插件来实现，在之前已经有一篇webpack的文章[webpack多页应用踩坑](http://www.oppovivo.xyz/blog/webpack.html)，现在对webpack也只能说是入门了，但还是很多东西不懂，还是需要继续踩坑，边踩边填边做笔记。

本文最近在`webpack`关于打包配置方面遇到的问题。

在vue-cli搭建的项目中，/config/index.js是关于webpack开发环境和生产环境的一些配置，主要看生产环境：

<figure>
        <img src="https://lo56ve.github.io/img/build.png">
</figure>

#### build.index

这个配置必须是文件系统上的绝对路径，是存放index.html的路径，图中为脚手架默认`dist`文件夹下

#### build.assetsRoot

这个也是文件系统上面的绝对路径，是存放应用程序**所有**静态资源如img,js,css,font文件夹的根路径，上图同样配置在dist

#### build.SubDirectory

这个其实是被编译的静态资源的二级目录，如图中配置为`static`，那么静态资源如img，js，css文件夹会输出到`/dist/static/`下面（即`assetsRoot` + `SubDirectory`），如果此项设置为空，则直接将静态资源输出到`build.assetsRoot`，如图中`/dist`，即和html同级目录

#### build.assetsPublicPath

设置静态资源引入的方式，**通过http服务器运行的url路径**，在大多数情况下设置为 `'/'`，即网站的根目录，如果后端有需要没办法在根目录中引入静态资源，可以设置为 `'./'`，以相对路径引入资源文件，如果打包输出的`html`和`css`文件夹是同级目录，即可以按照`./css/reset.css`这样来获取到css文件

因为打包输出的是项目的js和css，所以可以按照上面配置来绝对最后打包出来的html通过何种途径引用资源，但是图片不编译，直接复制到打包文件中（通过loader处理，小于限定值则为base64）

- 图片是以`/static/images/1.png`这样的根路径方式写进去，则输出在html中也是以这种形式
- 图片是以`../../static/images/1.png`在开发的src目录跳到static目录中，则打包之后还是以相对路径引入，但是路径会跟图片的路径相应的修改。还有一个点：~~**通过相对路径引入的图片会直接复制到打包后的图片文件夹中**，有可能出现文件夹中图片**重复**~~
- 图片是写在通过组件的data()方法等形式插入进去，则图片的路径不会改变，相当于后端返回的图片路径

> 2017/12/20 修改

- 通过**相对路径引**入的图片会通过`loader`打包，加上hash（如果有配置的话），然后复制到打包文件中
- 通过**根路径**索引引入的图片（如`/static/images/1.png`），是不会通过`loader`打包的
- 但是我们打包之后还是可以拿到图片资源的，只是没有加hash值，这是vue-cli脚手架打包生产用了**copy-webpack-plugin**插件，直接将静态资源复制到打包文件中，这就是之前页面图片通过相对路径到`static`文件夹中引用图片资源时，打包出来的图片会出现重复的原因。不清楚vue-cli使用了`copy-webpack-plugin`为何还要用`loader`打包图片。

在`/build/webpack.prod.conf.js`中的**copy-webpack-plugin**插件

<figure>
        <img src="https://lo56ve.github.io/img/copy-webpack-plugin.png">
</figure>

**copy-webpack-plugin**的相关配置

<figure>
        <img src="https://lo56ve.github.io/img/copy-webpack-lplugin-config.png">
</figure>

> 修改结束

所以，`webpack`打包资源路径最好是按照根目录写绝对路径，尽量避免使用相对路径来引入资源，不然在引入图片路径时候很麻烦

#### build.prodectionSourceMap

在构建生产环境版本时是否开启source map

#### dev.proxyTable

定义开发服务器的代理规则

#### 其余的问题

最近项目中，出于其他原因考虑，前端的代码不能放在根路径下面，只能放在根目录的index文件夹，这样如果在开发和打包中还是按照根目录来引入资源会找不到资源，所以为了保持开发和生产环境代码一致，所以将开发和生产中的静态资源路径（`build.SubDirectory`）改为`index`，将脚手架目录中的`static`的改为`index`

#### 关于webpack

今天在知乎上面看到一位大牛发表对webpack的看法[webpack为什么这么难用?](https://zhuanlan.zhihu.com/p/32148338?utm_source=wechat_session&utm_medium=social)

大佬果然是大佬，学习了

#### parcel

今天也在掘金上面看到`parcel`的介绍，在`github`上面开源到现在才十几天的打包功能，但是star已经高达1w+，据悉是不用想`webpack`一样需要配置，直接使用打包，学习成本比`webpack`低得多。之后再去细细研究一下。传送门：[从 webpack 到全面拥抱 Parcel #1 探索 Parcel](https://juejin.im/post/5a38e100f265da4324809297)