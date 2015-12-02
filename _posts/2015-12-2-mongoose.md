---
layout: post
title:  "Mongoose的使用测试用例"
date:   2015-12-2
keywords: mongoose, mongoDB, examples, mongoose基础示例,mongoose用例
categories:
- blog
permalink: mongoose
---


####**Mongoose的使用测试用例**

精华基础讲解博客，点击[这里](https://cnodejs.org/topic/504b4924e2b84515770103dd)

####快速开始示例

	var mongoose = require('mongoose');
	mongoose.connect('mongodb://localhost/testData');
	
	var db = mongoose.connection;
	
	db.on('error',function(){
		console.log('链接失败');
	});
	
	db.once('open',function(){
		// 声明 属性 和 方法
		var kittySchema = mongoose.Schema({
			name : String,
			age : Number,
			color : String
		});
	
		kittySchema.methods.speak = function(){
			return this.name ? "Meow name is "+ this.name : "I don't have a name";
		};

		kittySchema.statics.findAll = function(cb){
			return this.find({},cb);
		};
	
		kittySchema.methods.findKitten = function(cb){
			return this.model('Kitten').find({},cb);
		};
	
		// 构造 实例模型
		var Kitten = mongoose.model('Kitten',kittySchema);
	
	
		// 构造 实例对象
		var slience = new Kitten({
			name : 'Slience',
			age : 3,
			color : 'white'
		});
	
		var fluffy = new Kitten({
			name : 'Fluffy',
			age : 2,
			color : 'black'
		});
	
		console.log(slience.name,slience.color);
		console.log(slience.speak());
	
		// 将实例对象保存到数据库中
		slience.save(function(err,obj){
			if (err) {
				console.log(err,'保存失败');
			}else{
				console.log(obj);
			}
		});
	
		fluffy.save(function(err,obj){
			if (err) {
				console.log(err,'保存失败');
			}else{
				console.log(obj);
			}
		});
		
		//methods 和 statics 调用
		Kitten.findAll(function(err,objs){
			console.log('statics查找所有',objs);
		});
	
		slience.findKitten(function(err,objs){
			console.log('methods查找所有',objs);
		});
	});

运行后结果：
	
	Slience white
	Meow name is Slience
	{ _id: 564ad0aa40aedbca97b3822a,
	  color: 'white',
	  age: 3,
	  name: 'Slience',
	  __v: 0 }
	{ _id: 564ad260eb9db61a98cb7465,
	  color: 'black',
	  age: 2,
	  name: 'Fluffy',
	  __v: 0 }

在可视化工具中观察如下 ： 

![](http://coolnuanfeng.github.io/assets/images/mongodb.png)





####**methods 和 statics 区别示例**

定义 methods  注意 `return this.model('name')`

	// define a schema
	var animalSchema = new Schema({ name: String, type: String });
	
	// assign a function to the "methods" object of our animalSchema
	animalSchema.methods.findSimilarTypes = function (cb) {
	  return this.model('Animal').find({ type: this.type }, cb);
	}

使用methods，在实例对象上使用 `dog.findSimilarTypes`

	var Animal = mongoose.model('Animal', animalSchema);
	var dog = new Animal({ type: 'dog' });
	
	dog.findSimilarTypes(function (err, dogs) {
	  console.log(dogs); // woof
	});


定义和使用 statics，定义时`return this` 使用时时在 Model实例上使用`Animal.findByName`

	// assign a function to the "statics" object of our animalSchema
	animalSchema.statics.findByName = function (name, cb) {
	  return this.find({ name: new RegExp(name, 'i') }, cb);
	}
	
	var Animal = mongoose.model('Animal', animalSchema);
	Animal.findByName('fido', function (err, animals) {
	  console.log(animals);
	});


####**virtual使用示例**

虚拟属性，可以设置和获取定义的值，不会保存到mongoDB中。

	// define a schema
	var personSchema = new Schema({
	  name: {
	    first: String,
	    last: String
	  }
	});
	
	// compile our model
	var Person = mongoose.model('Person', personSchema);
	
	// create a document
	var bad = new Person({
	    name: { first: 'Walter', last: 'White' }
	});

	console.log(bad.name.first + ' ' + bad.name.last); // Walter White

	personSchema.virtual('name.full').get(function () {
	  return this.name.first + ' ' + this.name.last;
	});
	console.log('%s is insane', bad.name.full); // Walter White is insane


反之也可以设置

	personSchema.virtual('name.full').set(function (name) {
	  var split = name.split(' ');
	  this.name.first = split[0];
	  this.name.last = split[1];
	});
	mad.name.full = 'Breaking Bad';
	console.log(mad.name.first); // Breaking
	console.log(mad.name.last);  // Bad


####**schema验证器**

- required 非空验证
- min/max 范围验证（边值验证）
- enum/match 枚举验证/匹配验证
- validate 自定义验证规则

		var PersonSchema = new Schema({
		     name:{
		        type:'String',
		        required:true //姓名非空
		      },
		      age:{
		        type:'Nunmer',
		        min:18,       //年龄最小18
		        max:120     //年龄最大120
		      },
		      city:{
		        type:'String',
		        enum:['北京','上海']  //只能是北京、上海人
		      },
		      other:{
		        type:'String',
		        validate:[validator,err]  //validator是一个验证函数，err是验证失败的错误信息
		      }
		    });


####**一些关于Model的增删改查方法实例**

**find**

	PersonModel.find({"name":"blue"},function(err,persons){
		if(err){
			console.log('error');
			return ;
		}
		console.log(persons);
	});

**findById**

	var personSchema = mongoose.Schema({
			name : {
				type : String,
				required: true
			},
			age : {
				type : Number,
				min: 18,
				max: 120
			},
			city : {
				type : String,
				enum :['北京','上海']
			}
		});
	
		var PersonModel = mongoose.model('Person',personSchema);
	
	
		PersonModel.findById('564d2bfe6fb3718a0bcc192a',function(err,person){
			person.name = "jack";
			person.city = "北京";
			person.save(function(err,obj){
				if(err){console.log('error'); return ;}
				console.log('save success');
				console.log(obj);
			});
	
		});


**update和findByIdAndUpdate**

update第一个参数是查询条件，第二个参数是更新的对象，但不能更新主键。
需要注意，Document的CRUD操作都是异步执行，callback第一个参数必须是err，而第二个参数各个方法不一样，update的callback第二个参数是更新的数量，如果要返回更新后的对象，则要使用findByIdAndUpdate方法，findByIdAndUpdate只能基于id查找。

	PersonModel.update({_id:'564d2ccd5509a3fc0b6c0dbd'},{$set:{name:'luccy',age:26}},function(err,mesg){
		if(err){
			console.log('error');
			return ;
		}
		console.log(mesg);
	});
	
	PersonModel.findByIdAndUpdate({_id:'564d2ccd5509a3fc0b6c0dbd'},{$set:{name:'luccy',age:26}},function(err,person){
		if(err){
			console.log('error');
			return ;
		}
		console.log(person);
	});


**save和create**

两种新增方法区别在于，如果使用Model新增时，传入的对象只能是纯净的JSON对象，不能是由Model创建的实体，原因是：由Model创建的实体krouky虽然打印是只有{name:'krouky'}，但是krouky属于Entity，包含有Schema属性和Model数据库行为模型。如果是使用Model创建的对象，传入时一定会将隐藏属性也存入数据库，虽然3.x追加了默认严格属性，但也不必要增加操作的报错

	var tom = {
		"name" : "tom",
		"age" : 24,
		"city" : "江苏",
	};

	PersonModel.create(tom,function(err,person){
		if(err){
			console.log('error');
			return ;
		}
		console.log(person);
	});

	var daivd = new PersonModel({
		"name" : 'daivd',
		"age" : 26,
		"city" : "山东"
	});

	daivd.save(function(err,person){
		if(err){
			console.log('err');
			return;
		}
		console.log(person);
	});

	
**remove**

	PersonModel.remove({name:'blue'},function(err){
		if(err){
			console.log('err');
			return;
		}
		console.log('success');
	});



