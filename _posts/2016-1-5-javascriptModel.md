---
layout: post
title:  "javascript 的几种简单的设计模式"
date:   2016-1-5
keywords: javascript的几种简单的设计模式 设计模式 单体模式 工厂模式 观察者模式 接口模式 适配器模式 装饰者模式
categories:
- blog
permalink: javascriptModel
---


简单整理介绍下javascript中常见的几种设计模式，以node环境测试。

####单体/单例模式
单体模式，在该实例不存在的情况下，可以通过一个方法创建一个类来实现创建类的新实例；如果实例已经存在，它会简单返回该对象的引用
新建单体模式模块 singModel.js

	var _instance = null;

	function Single(name){
		this.name = name;
	}
	
	Single.prototype = {
		constructor : Single,
		show : function(){
			console.log(this.name);
		}
	};
	
	
	module.exports = function(name){
		if(!_instance){
			return _instance = new Single(name);
		}else{
			return _instance;
		}
	};

新建 app.js 进行测试，代码如下：

	var Single = require('./singModel');

	var singleObj1 = new Single('blue');
	singleObj1.show();
	
	var singleObj2 = new Single('leo');
	singleObj2.show();


运行node app.js 结果如下：

	blue
	blue

可以发现并没有生成一个blue一个leo的实例，而只有blue一个。


####工厂模式

一个工厂可以生产同一类的多种物品，具体生产哪种就看客户下什么订单了。工厂模式也是一样，我们创建一个工厂类，它可以创建多种实例，由开发者指定。

新建工厂模式模块factory.js，该模块生成两种实例对象A和B。

	var ProductA = require('./productA');
	var ProductB = require('./productB');
	
	exports.creatProduct = function(type){
		var product = null;
		switch(type){
			case 'A':
				product = new ProductA;
				break;
			case 'B':
				product = new ProductB;
				break;
		}
	
		return product.getProduct();
	};

productA.js和productB.js

	//A
	function ProductA(){
		this.name = 'ProductA';
	}
	ProductA.prototype.getProduct = function(){
		console.log(this.name + ' is coming');
	};
	module.exports = ProductA;

	//B
	function ProductB(){
		this.name = 'ProductB';
	}
	ProductB.prototype.getProduct = function(){
		console.log(this.name + ' is coming');
	};
	module.exports = ProductB;


修改 app.js 的代码如下：

	var factory = require('./factory');
	factory.creatProduct('A');
	factory.creatProduct('B');

运行 node app.js

	ProductA is coming
	ProductB is coming

由此可见可以通过factory.js来生产A实例和B实例。


####接口模式

接口提供了一种用来说明一个对象应该具有哪些方法的手段，他只是表明这个对象需要拥有实现这些功能方法，但并不关心内部实现。例如，一个人应该有一个名字，但不关心这个名字怎么来。

新建接口模式文件 interface.js

	function Interface(name,methods){
		if(arguments.length!=2){
			throw new Error('Interface 接口必须有两个参数，第一个为接口名 string类型，第二个为要实现的方法名 array 类型');
		}
		this.name = name;
		this.methods = [];
		for(var i=0;i<methods.length;i++){
			if(typeof methods[i] != 'string'){
				throw new Error('要实现的方法名位字符串类型');
			}
			this.methods.push(methods[i]);
		}
	}
	
	Interface.prototype.mustFn = function(obj) {
		if(arguments.length<2){
			throw new Error('参数不正确，至少两个参数');
		}
		for(var i=1,len = arguments.length; i<len;i++){
			var interface = arguments[i];
			if(interface.constructor != Interface){
				throw new Error('mustFn的参数需要两个或以上的Interface的实例对象');
			}
			//循环实现每个接口里的方法
			for(var j=0,mLen = interface.methods.length;j<mLen;j++){
				var method = interface.methods[j];
				if(!obj[method]||typeof obj[method] !== 'function'){
					throw new Error(obj.constructor+'的'+method+'方法没有实现');
				}
			}
		}
	};
	
	
	module.exports = Interface;



修改 app.js 文件如下：

	var Interface = require('./interface');
	var USB = new Interface('USB',['read','write']);
	var Play = new Interface('Play',['play']);
	
	function MP3(){
	
	}
	MP3.prototype.read = function() {
		console.log('MP3 is reading');
	};
	MP3.prototype.write = function() {
		console.log('MP3 is writing');
	};
	MP3.prototype.play = function(){
		console.log('MP3 is playing');
	};
	
	function Ipod(){
	
	}
	Ipod.prototype.read = function() {
		console.log('Ipod is reading');
	};
	Ipod.prototype.write = function() {
		console.log('Ipod is writing');
	};
	Ipod.prototype.play = function(){
		console.log('Ipod is playing');
	};
	
	function Ibook(){
	
	}
	Ibook.prototype.read = function() {
		console.log('Ibook is reading');
	};
	Ibook.prototype.write = function() {
		console.log('Ibook is writing');
	};
	Ibook.prototype.play = function(){
		console.log('Ibook is playing');
	};
	
	
	function USBmanger(){
		this.devices = [];
	}
	USBmanger.prototype.add = function(device) {
		//实现USB 和 Play 两个接口
		Interface.prototype.mustFn(device,USB,Play);
		this.devices.push(device);
	};
	
	var mp3 = new MP3();
	var ipod = new Ipod();
	var ibook = new Ibook();
	
	var usbManger = new USBmanger();
	
	usbManger.add(mp3);
	usbManger.add(ipod);
	usbManger.add(ibook);
	
	console.log('所有接口都已实现');
	
	mp3.play();


运行 node app.js，结果如下：

	所有接口都已实现
	MP3 is playing

如果其中某个实例的方法没有实现，如注释掉下面的代码：
	
	/*
	Ibook.prototype.play = function(){
		console.log('Ibook is playing');
	};
	*/

运行 node app.js，结果如下：

	throw new Error(obj.constructor+'的'+method+'方法没有实现');
	Error: function Ibook(){}的play方法没有实现


由此可见，可以通过接口模式来规范一些实例对象。


####观察者模式

观察者模式又叫发布订阅模式（Publish/Subscribe），它定义了一种一对多的关系，让多个观察者对象同时监听某一个主题对象，这个主题对象的状态发生变化时就会通知所有的观察者对象，使得它们能够自动更新自己。

新建观察者模式 observer.js 文件，用于监听实例的update函数。

	function Observer(){
		this.observer = {};
	}
	
	Observer.prototype = {
		add : function(key,obj){
			this.observer[key] = obj;
		},
		remove : function(key){
			if(this.observer[key]){
				delete this.observer[key];
			}
		},
		doAction : function(){
			this.notifyAll();
		},
		notifyAll : function(){
			for(var key in this.observer){
				this.observer[key].update();
			}
		}
	};
	
	module.exports = new Observer; 

再新建两个观察者对象模块 watcherOne.js 和 watcherTwo.js，代码分别如下：

	//one
	function WatchOne(){
		this.name = 'WatchOne';
	}
	WatchOne.prototype = { 
		update : function(){
			console.log(this.name);
		}
	};
	module.exports = new WatchOne;
	
	//two
	function WatchTwo(){
		this.name = 'WatchTwo';
	}
	WatchTwo.prototype = { 
		update : function(){
			console.log(this.name);
		}
	};
	module.exports = new WatchTwo;


修改 app.js 文件如下：

	var watcher1 = require('./watcherOne');
	var watcher2 = require('./watcherTwo');//引入两个被观察者
	var observer = require('./observer');//观察对象
	
	observer.add('watcher1',watcher1);
	observer.add('watcher2',watcher2); //添加观察对象，建立联系
	
	//observer.remove('watcher2');	//删除一个被观察者
	
	observer.doAction(); //执行观察命令，查看结果

运行 node app.js

	WatchOne
	WatchTwo

如果将 `observer.remove('watcher2');` 注释取消，只会输出WatchOne，由此可见通过observer可以实现一对多或多对一的观察。


####其他的一些模式

 - 适配器模式 ： 个人觉得就是对继承的类中某些方法进行重写，只是重写时，引入外部要求的或提供的接口
 - 装饰者模式 ： 个人觉得就是对继承的类进行某些方法的补充，完善。


**以上都是个人的一些理解，可能有些不准确的地方。**