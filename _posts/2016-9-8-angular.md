---
layout: post
title:  "Angular 的一些注意事项"
date:   2016-9-9
keywords: angular 依赖注入 ng-if ng-show service provide factory link compile 区别
categories:
- blog
permalink: angular
---


#### angular 的依赖注入

angular 通过sevice定义写服务(及内置服务)，在angular应用启动时类似做了一个 map 映射 将 对应的字符串分别对应其服务的构造函数(即通过 service provide factory 定义的服务函数)，当在控制器中注入依赖时，通过fn的tosting方法查找到对应的服务构造函数，并进行实例化生成服务的实例对象，并将实例对象返回到控制器中。

也可以通过 模块名.$injector = ['依赖服务名数组'] 来实现注入

angular.injector('moduleName').invoke('customService') or angular.injector('moduleName').get('customService')


#### angular 的 service factory  provide区别

factory  返回一个 object = {} ， object中里面添加属性和方法
service  直接将属性和方法定义在 this 上
provide  是唯一一个可以在 config 中获取到 this的 属性和方法的，而在 controller中 获取到的是 this.$get 中返回的对象 object {}中的属性和方法。另外 如果在config中使用 this 上定义的属性或方法，在注入config时的参数形式为 服务名+Provider 如：

    myApp.provide('movie',function(){
         this.version = '';
         this.setVersion = function(v){
             console.log(v)
             this.version = v
         }
         this.$get = function(){
             return {
                 title : 'this version is '+ this.version
             }
         }
    });

    myApp.config(function(movieProvider){
         movieProvider.setVersion('0.0.1');
    });




####  angular 中 ng-if 与 ng-show 等的区别

ng-if 只有当条件满足时才会进行渲染，另外会生成一个独立scope 作用域，为了防止踩坑可以将 数据挂载到 一个对象上再将该对象挂载到 $scope上

#### angular 中ng-repeat 对应重复数据的处理

angular中ng-repeat 无法遍历重复的数据，如果数据中有重复的需要 添加 track by $index 进行遍历


#### angular 服务中的 value constant 等 定义常量

myApp.value('person','{"name":"blue","age":24}');
myApp.constant('NAME','blue');


#### 在config中定义 服务、常量、指令和filter

    angular.module('myApp',[]).config(function($provide,$compliePrivoider,$filterProvider){
        $prvoide.value('name','blue');
        $provide.factory('service1',function(){});
        $compliePrivoider.directive('myDirective',function(){});
        $filterProvider.register('filterName',function(){return fn});
    })

   这种方式等同于

   angular.module('myApp',[]).factory('service1',function(){}).directive('myDirective',function(){}).value(...).filter(...)


#### 控制器 filter 指令的本质

控制器 filter 指令本质也是个 服务 provider  angular内部有 $controller $filter $compile 服务及它们的 provider $controllerProvider $filterProvider $compileProvider 都可以在 config 中配置 如

    angular.module('myApp',[]).config(function($provide,$controllerProvider,$compliePrivoider,$filterProvider){
        $prvoide.value('name','blue');
        $provide.factory('service1',function(){});
        $compliePrivoider.directive('myDirective',function(){});
        $controllerProvider.register('myCtroller',function($scope){})
        $filterProvider.register('filterName',function(){return fn});
    })


#### 指令中如何注入服务

在指令中使用服务(或自定义服务) 实例如下:


    myModel.provider('movie',function(){
         this.version = '';
         this.setVersion = function(v){
             this.version = v
         }
         this.$get = function(){
             return {
                 version : 'this version is '+ this.version
             }
         }
    });

    myModel.directive('angular',['movie',function(movie){
        return {
            'scope':{},
            'restrict' : 'E',
            'template' : '<h1>Hello world {{angularVersion}}</h1>',
            'replace' : true,
            'link' : function(scope,element,attr){
                //console.log(scope,element,attr);
                scope.angularVersion = movie.version;
                element.on('click',function(){
                    console.log('directive version:'+movie.version);
                    console.log('click');
                })
            }
        }
    }]);



#### 指令相关参数中的link 与 compile

link 可以接受五个参数 scope elem attrs parentCtrl transcludefn ，compile 可接受三个参数 elem attrs, transclude。link函数主要用来对DOM元素绑定事件，监视模型属性变化，并更新DOM，可以多次执行。compile函数主要用来在link函数运行之前进行一些DOM转化，只执行一次且必须返回一个函数即link函数（同一个指令有几个实例存在，compile函数也只会运行一次）
compile 不能访问 scope

更多指令详解点击[这里](https://segmentfault.com/a/1190000004956878)



> 我的一些angular实例测试[DEMO](https://github.com/CooLNuanfeng/angular-demos)
