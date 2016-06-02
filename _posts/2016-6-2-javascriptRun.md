---
layout: post
title:  "深入理解 Javascript 运行机制及原型"
date:   2016-6-2
keywords: javascript 运行机制 变量预解析 闭包 作用域  Event Loop setimeout call apply bind caller callee arguments javascript原型
categories:
- blog
permalink: javascriptRun
---


#### 事件执行

JavaScript语言的一大特点就是单线程，也就是说，同一个时间只能做一件事。

单线程就意味着，所有任务需要排队，前一个任务结束，才会执行后一个任务。如果前一个任务耗时很长，后一个任务就不得不一直等着。

JavaScript语言的设计者意识到，这时主线程完全可以不管IO设备，挂起处于等待中的任务，先运行排在后面的任务。于是，所有任务可以分成两种，一种是同步任务（synchronous），另一种是异步任务（asynchronous）。

因此Javascript运行机制可以简单理解成一个主线程（执行栈）和一个任务队列，脚本运行时先运行主线程，主线程运行完后，从"任务队列"中读取事件，运行任务队列的任务，这个过程是循环不断的，又称Event Loop（事件循环）。

更多更详细的介绍请参考阮大师的[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)

理解了上面的内容来观察下面代码：

	console.log('a');
	setTimeout(function(){
	    console.log('c');
	},0);
	console.log('b');

上述代码执行结果依次输出 abc。接下来看下面代码：

	var n = 0;
	for(var i=0;i<10;i++){
	    setTimeout(function(){
	        n+=i;
	    },0);
	}

	console.log(n);
	setTimeout(function(){
	    console.log(n);
	},0);

注意：for循环中的setTimeout只开启一个延时任务，并不是开启 i 次 setTimeout。


#### 变量/函数的预解析

总所周知javascript在运行前会对基础类型的变量进行预解析，对函数声明（不包括函数表达式）进行预加载，观察下面代码的运行结果：

	console.log(a);
	b();
	c();
	var a = 'a';
	function b(){
	    console.log('b');
	}
	var c = function(){
	    console.log('c');
	}

代码中变量a、c会预先解析并初始化赋值为undefined，函数b会预加载在内存中，所以，b函数可在函数声明前调用而c函数表达式不行。

那么，对于一些有逻辑判断的情况下JavaScript是如何进行预解析和预加载的呢？观察下面两段代码的执行结果：

代码one：

	console.log(a,b,c);
	var b = 'b';
	if(true){
	    var a = 'a';
	}
	if(false){
	    var c = 'c';
	}
	console.log(a,b,c);

输出结果 ：

	undefined undefined undefined
	a b undefined

代码 two

	faz('faz');
	//foo('foo');
	//bar('bar');
	if(true){
	    function foo(x){
	        console.log(x);
	    }
	}
	if(false){
	    function bar(x){
	        console.log(x);
	    }
	}
	function faz(x){
	    console.log(x);
	}
	foo('foo');
	bar('bar');

输出结果：

	faz
	foo
	Uncaught TypeError: bar is not a function

可见对于变量的定义，无论是否存在逻辑判断，JavaScript都会进行预解析，而对于函数声明，JavaScript并不会对逻辑判断中的进行预加载，只会对函数主体中暴露的进行预加载。

再看一个很容易迷惑人的例子 ：

	var foo = 1;
	function bar() {
	    if (!foo) {
	        var foo = 10;
	    }
	    alert(foo);
	}
	bar();

小心被迷惑，最后alert的结果是10。

另外，当在函数作用域里的return语句后变量声明，依然有效：

	function foo() {
	    if (false) {
	        var x = 1;
	    }
	    console.log(x,y);
	    bar();
	    return;
	    var y = 1;
	    function bar(){
	        console.log('bar');
	    }
	}
	foo();

输出结果为 ：

	undefined,undefined
	bar



#### 闭包/作用域 call apply bind

对应作用域（也可以理解为闭包）可以简单的这样理解，函数被包裹在一个容器（作用域）里面，在这个容器里面存在一些变量或者其他东西，当函数运行，调用这些变量等，就会在当前容器里面找这个东西。这个容器其实外面还包裹了一个更大的容器，如果当前小容器没有的话，函数会到更大的容器里面寻找，依次类推，一直找到最大的容器 window 对象。但是如果函数在当前小容器里面运行的时候，小容器里面有对应变量等，即便是大容器里面也有，函数还是会调用自己容器里面的。call、apply、bind方法就是来打破这个作用域的。

call 和 apply 方法，这两个方法的本质没有区别，只是参数的方式不同，call和apply都会立即执行当前函数，而bind方法并不会立即执行当前函数而是返回其在指定作用域下（第一个参数）的函数，需要重新调用，bind参数与call类似，观察下面代码执行结果：


	var m = {
	    "x" : 1
	};
	function foo(y) {
	    console.log(this.x + y);
	}
	foo(5);
	foo.apply(m, [5]);
	foo.call(m, 5);
	var foo1 = foo.bind(m, 5);
	foo1();

输出结果为：

	NaN
	6
	6
	6


利用call apply bind 可以实现一些简单的算法函数，如下，查找数组中最大/小值 ：

	var numbers = [5, 458 , 120 , -215 ];
	var maxInNumbers = Math.min.apply(null, numbers);
	console.log(maxInNumbers);

#### 函数的arguments及caller与callee

arguments是函数中的一个特殊的类数组对象，自身含有length 属性，本质是一个内置对象。

caller：返回一个对函数的引用，该函数调用了当前函数（调用当前函数函数的函数）。对于函数来说，caller 属性只有在函数执行时才有定义。 如果函数是由 Javascript 程序的顶层调用的，那么 caller 包含的就是 null 。
callee：返回正被执行的 Function 对象（被调用的函数），也就是所指定的 Function 对象的正文。

注意：arguments.length是实参长度，arguments.callee.length是形参长度。

分别观察下面两段代码：

代码 one :

	function myFunc() {
	   if (myFunc.caller == null) {
	      console.log("该函数在全局作用域内被调用!");
	   } else
	      console.log("调用我的是函数是" + myFunc.caller);
	}
	myFunc();
	function test() {
		myFunc()
	}
	test();

代码 two :

	function a(){
	  console.log(arguments);
	  var args = arguments;
	  function c(){
	    console.log(arguments);
	    console.log(args.callee == a);
	    console.log(arguments.callee == c);
	  }
	  c(4,5,6);
	}
	a(1,2,3);


接下来看下下面这段代码的执行结果:

	var length = 10;
	function fn(){
	    console.log(this.length);
	}
	var obj = {
	    length : 5,
	    method : function(fn){
	        fn();
	        arguments[0]();
	    }
	}
	obj.method(fn,1);


#### javascript 原型理解

在JavaScript中有一种说法是，一切皆是对象。众所周知，JavaScript中有5种基本类型（ES5之前），分别为： undefined，null，boolean，string，number。而复杂类型就是我们所说的对象 Object。

这5种基本类型也可以看做一种对象，即所谓的包装对象。请看下面代码：

	var str = 'abcdefg';
	var str1 = new String('test');
	var str2 = String('test');
	str.size = 10;
	str1.size = 10;
	str2.size = 10;
	console.log(str.length); //7
	console.log(str.indexOf('d')); //3
	console.log(str.size); //undefined
	console.log(typeof str1); //object
	console.log(typeof str2); //string
	console.log(str1.charAt(2)); //s
	console.log(str2.charAt(2)); //s
	console.log(str1.size); //10
	console.log(str2.size); //undefined

由以上代码的结果可见，对于基本类型，可以定义属性，程序并不报错，也可以通过new来新建对象，说明像String，Array，Boolean等都可以理解为构造函数，而JavaScript中Function 类型有一个属性 prototype，直接翻译过来就是原型。这个属性就是一个指针，指向一个对象，这个对象包含一些属性和方法，这些属性和方法会被当前函数生成的所有实例（对象）所共享。为了理解原型将JavaScript中的对象分为以下三种。

- 普通对象
- 函数对象
- 原型对象

普通对象有个不规范的“原型”对象，在chrome和Firefox中以 \__proto\__表示，本文暂时称为隐式原型。
每个函数对象都有一个 prototype 对象，即为原型。原型也有隐式原型指向Object的原型即Object.prototype。而函数对象本身的隐式原型则都指向Function.prototype。具体请看下图：

![prototype](http://coolnuanfeng.github.io/assets/images/prototype.png)

有图在脑，原型不愁。 来看下面的代码 ：

	function Person(name, age){
	    this.name = name;
	    this.age = age;
	}
	Person.prototype.getInfo = function(){
	    console.log(this.name + " is " + this.age + " years old");
	};
	Person.prototype.sex = 'man';
	Person.__proto__.height = '178';
	var will = new Person("Will", 28);
	console.log(will.prototype);
	console.log(will.__proto__);
	console.log(will.name);
	console.log(will.constructor);
	console.log(Person.prototype.__proto__);
	console.log(Person.prototype.constructor);
	console.log(Person.prototype.sex === will.sex);
	console.log(Function.prototype.height,will.height);
	console.log(Person.prototype.__proto__ === Object.prototype);
	console.log(typeof Object);
	console.log(Object);
	console.log(Object.prototype);
	console.log(Object.prototype.__proto__);
	console.log(Object.prototype.constructor);
	console.log(Function.__proto__ === Function.prototype);
	console.log(Person.__proto__ === Person.prototype);
	console.log(Object.__proto__ === Function.prototype);


更多原型的更详细讲解可以查看这两篇文章：

- [彻底理解JavaScript原型](http://www.imooc.com/article/2088)
- [JavaScript 原型理解与创建对象应用](http://yujiangshui.com/javascript-prototype-and-create-object/)


以上是我关于JavaScript的底层一些运行原理等的理解，可能还有错误，请指正。
