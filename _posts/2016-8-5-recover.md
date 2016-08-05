---
layout: post
title:  "JS 垃圾回收机制"
date:   2016-8-5
keywords: JS 垃圾回收机制 内存泄漏及解决方案
categories:
- blog
permalink: recover
---




JS中的垃圾回收机制，有两种策略来实现垃圾回收：标记清除 和 引用计数。

#### 标记清除

标记清除：垃圾收集器在运行的时候会给存储在内存中的所有变量都加上标记，然后，它会去掉环境中的变量的标记和被环境中的变量引用的变量的标记，此后，如果变量再被标记则表示此变量准备被删除。 2008年为止，IE，Firefox，opera，chrome，Safari的javascript都用使用了该方式；


#### 引用计数

引用计数：跟踪记录每个值被引用的次数，当声明一个变量并将一个引用类型的值赋给该变量时，这个值的引用次数就是1，如果这个值再被赋值给另一个变量，则引用次数加1。相反，如果一个变量脱离了该值的引用，则该值引用次数减1，当次数为0时，就会等待垃圾收集器的回收。


这个方式存在一个比较大的问题就是循环引用，就是说A对象包含一个指向B的指针，对象B也包含一个指向A的引用。 这就可能造成大量内存得不到回收（内存泄露），因为它们的引用次数永远不可能是 0 。早期的IE版本里（ie4-ie6）采用是计数的垃圾回收机制，闭包导致内存泄露的一个原因就是这个算法的一个缺陷。

我们知道，IE中有一部分对象并不是原生额javascript对象，例如，BOM和DOM中的对象就是以COM对象的形式实现的，而COM对象的垃圾回收机制采用的就是引用计数。因此，虽然IE的javascript引擎采用的是标记清除策略，但是访问COM对象依然是基于引用计数的，因此只要在IE中设计COM对象就会存在循环引用的问题！

举个例子

	window.onload = function(){
	    var el = document.getElementById("id");
	    el.onclick = function(){
	        alert(el.id);
	    }
	}

这段代码为什么会造成内存泄露？

	el.onclick= function () {
	    alert(el.id);
	};

执行这段代码的时候，将匿名函数对象赋值给el的onclick属性；然后匿名函数内部又引用了el对象，存在循环引用，所以不能被回收；

解决方法：

	window.onload = function(){
	    var el = document.getElementById("id");
	    var id = el.id; //解除循环引用
	    el.onclick = function(){
	        alert(id);
	    }
	    el = null; // 将闭包引用的外部函数中活动对象清除
	}
