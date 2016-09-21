---
layout: post
title: js字符串和数组的操作方法
categories: [blog ]
tags: [ ]
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

> 使用加号操作符在大多数情况下都比使用concat()方法要简便易行（特别是拼接多个字符串的情况下）

### 3.slice()，substr()，substring()

这三种方法均是基于子字符串创建新字符串的方法，这三种方法的第一个参数都是指定子字符串的开始位置。

`slice()`和`substring()`第二个参数指定的是子字符串最后一个字符后面的位置。

`substr()`第二个参数指定的是返回的字符个数。

如果没有给上述三种方法传递第二个参数，则将字符串的长度作为结束位置。和`concat()`方法一样，不会修改字符串本身的值。

	var stringValue="hello world";
	alert(stringValue.slice(3));		//"lo world"
	alert(stringValue.substring(3));	//"lo world"
	alert(stringValue.substr(3));		//"lo world"
	alert(stringValue.slice(3,7));		//"lo w"
	alert(stringValue.substring(3,7));	//"lo w"
	alert(stringValue.substr(3,7));		//"lo worl"

但是，在传递给这些方法的参数是负值的情况下，三种方法就完全不同了，`slice()`方法会将传入的负值与字符串的长度相加，即从结尾开始算起。

`substr()`方法会将第一参数加上字符串长度，从结尾算起，第二参数如果是负数则转换为0。

`substring()`方法将所有的负值参数都转换为0。

	var stringValue="hello world";
	alert(stringValue.slice(-3));		//"rld"
	alert(stringValue.substring(-3));	//"hello world"
	alert(stringValue.substr(-3));		//"rld"
	alert(stringValue.slice(3,-4));		//"lo w"
	alert(stringValue.substring(3,-4));	//"hel"
	alert(stringValue.substr(3,-4));		//""(空字符串)

### 4.indexOf()和lastindexOf()

这两种方法是从字符串中查找字符串的方法，一个从开头开始向后查找，一个从结尾向前查找，`返回子字符串第一次出现的位置（如果没有找到子字符串，则返回-1）`。这里第二个参数可选，表示从字符串中的哪个位置开始搜索。

	var stringValue="hello world";
	alert(stringValue.indexOf("o",6));	//7

从位置6，即字母w（位置6，位置基于0开始）开始向后搜索。

### 5.trim()

这个方法会创建一个字符串的副本，删除前置及后缀的所有空格，然后返回结果。

	var stringValue="   hello world  ";
	var trimedstringValue=stringValue.trim();
	alert(strgingValue);		//"   hello world  "
	alert(trimedstringValue);	//"hello world"

此外，Firefox3.5+、Safari5+、Chrome8+还支持非标准的trimLeft(),trimRight()方法

### 6.toLowerCase()，toUpperCase()

这两个是字符串大小写转换方法

### 7.match()，search()，replace()

定义了用于字符串匹配模式的方法。

match()方法只接受一个参数，要么是一个正则表达式，要么是一个regExp对象。

返回的是一个数组，数组的第一项是与整个模式匹配的字符串，之后每一项保存着与正则表达式中的捕获组匹配的字符串。

	var text="cat,bat,sat,fat";
	var pattern=/.at/;

	//与pattern.exec(text)相同
	var matchs=text.match(pattern);
	alert(matchs.index);		//0
	alert(matchs[0]);			//"cat"
	alert(pattern.lastIndex);	//0

search()方法接收的参数和match()相同，但返回的是第一个匹配项的索引，如果没有就返回-1，此方法始终是从字符串开头向后查找。

	var text="cat,bat,sat,fat";
	var pos=text.search(/at/);
	alert(pos);		//1(at在字符串中第一次出现的位置)

replace()方法接收两个参数，第一个参数可以是一个字符串或者一个RegExp对象，第二个参数可以是一个字符串或者一个函数。先匹配到第一个参数，然后用第二个参数替换，如果第一个参数是一个字符串，则只会替换第一个匹配到的字符串，如果想替换全局，则需要提供正则表达式，指定全局（g）标志。

	var text="cat,bat,sat,fat";
	var result=text.replace("at","ond");	
	alert(result);	//cond,bat,sat,fat

	result=text.replace(/at/g,"ond");
	alert(result);	//"cond,bond,sond,fond"

### 8.localCompare()

比较这两个字符串，

如果字符串在`字母表`中应该排在字符串之前，返回负数（大部分情况是-1）

如果字符串等于字符串参数，则返回0

如果字符串在字母表中应该排在字符串参数之后，返回一个正数（大部分情况是1）

	var stringValue="yellow";
	alert(stringValue.localeCompare("brick"));	//1
	alert(stringValue.localeCompare("yellow"));	//0
	alert(stringValue.localeCompare("zoo"));	//-1


# Array

### 1.toLocalString(),toString(),valueOf()

这三个方法很类似，toString()和toLocalString()方法返回数组中每个值的字符串形式拼接而成的一个以`逗号`分隔的字符串，但是通过alert打印的数组，是隐形通过toString()方法。valueOf()返回的还是数组。

### 2.join()

此方法接收一个参数，即用分隔符的字符串，然后返回包含所有数组项的字符串。

	var colors=["red","green","blue"];
	alert(colors.join(","));	//red,green,blue
	alert(colors.join("||"));	//red||green||blue

### 3.push(),pop(),shift(),unshift()

数组末端进，末端出	（栈）		push(),pop()
数组末端进，前端出	（队列）		push(),shift()
数组前端进，末端出				unshift(),pop()

在数组末端发生的，pop(),push()

在数组前端发生的，shift()，unshift()

### 4.reverse(),sort()

reserve()方法会反转数组项的顺序

1.sort()方法按升序排列数组项--即最小的值位于最前面，最大的值排在最后面，sort()方法会调用每个数组项的tostring()转型方法，然后比较得到的字符串，确定如何排序（`即使数组中每一项都是数值，sort()方法比较的也是字符串`）

	var values=[0,1,5,10,15];
	values.sort();
	alert(values);		//0,1,10,15,5

在进行字符串比较时，“10”位于“5”前面。

2.sort()方法可以接受一个`比较函数`作为参数。

比较函数接受两个参数，如果第一个参数应该位于第二个之前返回一个负数，两个参数相同时返回0，如果第一个参数应该位于第二个参数之后，返回一个正数。

一个简单的比较函数如下：

	function compare(value1,value2){
		if(value1<value2){
			return -1;
		}else if(value1>value2){
			return 1;
		}else{
			return 0;
		}
	}
	
	var values=[0,1,5,10,15];
	values.sort(compare);
	alert(values);		//15,10,5,1,0

### 5.concat()

concat()可以基于当前数组中的所有项创建一个新数组,不会影响原数组

	var colors=["red","green","blue"];
	var colors2=colors.concat("yellow",["black","brown"]);

	alert(colors);		//red,green,blue
	alert(colors2);		//red,green,blue,yellow,black,brown

### 6.slice()

接收一或者两个参数，即要返回项的起始和结束位置。

只有一个参数，slice()返回从该参数指定位置开始到当前数组末尾的所有项。

两个参数情况下，slice()返回起始和结束位置之间的项，但`不包含结束位置`的项。

可以接受负数参数，从结尾数起，如果结束位置小于起始位置，则返回空数组。

	var colors=["red","green","blue","yellow","purple"];
	var colors2=colors.slice(1);
	var colors3=colors.slice(1,4);

	alert(colors2);		//green,blue,yellow,purple
	alert(colors3);		//green,blue,yellow

### 7.splice()

最强大的数组方法，主要用途是向数组中部插入项。

#### 删除

可以删除任意数量的项，指定两个参数：要删除的第一项的位置和要删除的项数。例如,splice(0,2)会删除数组中的前两项。

#### 插入

可以向指定位置插入任意数量的项。指定三个参数：起始位置、0（表示要删除的项数）、要插入的项。

#### 替换

可以向指定位置插入任意数量的项，同时删除任意数量的项。例如：splice(2,1,"red","green")会删除当前数组位置2的项，然后再从位置2开始插入字符串"red"和"green"

> **splice()方法始终都会返回一个数组**

### 8.indexOf()，lastIndexOf()

与字符串的作用相似，在比较第一个参数与数组中的每一项时，会使用全等操作符