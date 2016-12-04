---
layout: post
title: apply,call,callee,caller
categories: [blog ]
tags: [ ]
description: 函数自带的属性和方法
---

刚刚在学习nodeJs，在遇到了call和apply方法，就顺便重温了一下这两个方法，并且祭出红宝石重新研读了一下相关的知识。

这篇文章就先说一个call，apply，还有在红宝石书里面相关的caller和callee。

### call和apply

每个函数都包含两个非继承而来的方法，即call和apply

`用途`：在特定的作用域中**调用函数**，实际上等于设置函数体内this对象的值

	var pet={
		words:'...',
		speak:function(words){
			console.log(this.words);
		}
	}
	
	var dog={
		words:'wangwang'
	}
	
	pet.speak.call(dog,'wang');

在这里，dog对象本身没有speak()函数，在此通过call方法调用了pet对象里面的speak()函数。

`区别`：apply方法和call方法区别在于他们接受参数不同，两者第一个参数都是想在其中运行的作用域，两者第二个参数都是想传给函数的参数。

但是，apply接受的是参数数组，*可以是Array实例，也可以是arguments对象*，如：
 
	sum.apply(this,arguments);
	//或者
	sum.apply(this,[num1,num2]); 

call传递给函数的参数必须逐个列举出来，如：

	sum.call(this,num1,num2);

**其实传递参数并非apply和call真正的用武之地，真正强大的地方是能够扩充函数赖以运行的作用域。

	window.color="red";
	var o ={color:'blue'};

	function saycolor(){
		console.log(this.color);
	}
	
	saycolor();

	saycolor.call(this);	//red
	saycolor.call(window);	//red
	saycolor.call(o);		//blue

saycolor()是作为全局函数定义的，而且在全局作用域调用它，使用this，此处的this指向global，在浏览器中即window（在node里面就不是了），然后第二个是在window作用域下调用，第三个是在o对象作用域下调用（this指向o）

使用apply和call最大的好处就体现在第三种，就是**对象不需要和方法有任何耦合关系**，不像之前要把saycolor()方法放到o对象里面，然后再通过o对象调用。

### bind

这个方法和apply和call类似，但是有不同，这个方法会**创建**一个函数的实例，这个实例的this会绑定给bind()函数的值

	window.color='red';
	var o={color:'blue'};

	function saycolor(){
		console.log(this.color);
	}

	var objsaycolor=saycolor.bind(o);
	objsaycolor();

在这里，saycolor()调用了bind()方法并传入了o对象，**创建了objsaycolor()函数**，objsaycolor()函数的this指向对象o。

### caller和callee

#### callee

callee是arguments对象的一个属性，这个属性是一个指针，指向拥有这个arguments对象的函数，即正在被执行的function函数的对象，**多用于递归和封装**

	function factorial(num){
		if(num<=1){
			return 1;
		}else{
			return num*arguments.callee(num-1);
			//return num*factorial(num-1);
		}
	}

这是一个很经典的阶乘函数，其中需要不断递归，如果像注释那样子写法，这个函数的执行和函数名factorial紧紧耦合在一起，需要消除这种耦合现象，就需要采用arguments.callee()，这个属性指向arguments对象的函数，在这即factorial()函数。

> 虽然在谷歌下直接按注释那样调用factorial显示是正常的

#### caller

caller也是函数对象的属性，保存着调用当前函数的函数的引用（即谁引用了当前函数），如果是在全局作用域调用当前函数，则它的值为null

	function outer(){
		inner();	//在outer函数内调用了inner()
	}
	
	function inner(){
		console.log(inner.caller);	//这里的inner.caller指向outer
	}

因为outer()调用了inner(),所以访问inner.caller就指向outer()，为了实现更松散的耦合，也可以通过arguments.callee.caller来访问相同信息。

> 顺便提一下，这里是inner.caller，是`访问函数`的指针而不执行函数，只是访问outer（）函数而不是在此调用执行outer（）函数。
> 要访问函数的指针而不执行函数的话，必须去掉函数名后面的那对括号。

### 结束语

本文到此差不多结束，主要介绍了call,apply,bind,callee,caller，差不多写完看到红宝石上面一个很好的提示

	window.color='red';
	var o={color:'blue'};

	function saycolor(){
		console.log(this.color);
	}

	saycolor();		//red

	o.saycolor=saycolor;	//记住，函数没括号，是访问，不是调用执行
	o.saycolor();	//blue

**函数的名字仅仅是一个包含指针的变量而已，因此，即使是在不同的环境中执行，全局的saycolor()函数和o.saycolor()指向的仍然是同一个函数**

