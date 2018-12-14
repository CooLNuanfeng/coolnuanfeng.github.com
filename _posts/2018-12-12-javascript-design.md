---
layout: post
title:  "前端设计模式"
date:   2018-12-12
keywords: 前端设计模式代码示例 单例模式 工厂模式 观察者模式 适配器模式 代理模式...
description:
categories:
- blog
permalink: javascript-design
---

## 工厂模式

    //类模板
    class Mobile {
        constructor(name){
            this.name = name;
        }
        call(){
            console.log(this.name + ' call');
        }
    }
    //工厂
    class CreaterFactory {
        create(name){
            return new Mobile(name);
        }
    }

    var factory = new CreaterFactory();
    var iphone = factory.create('iphone');
    iphone.call();

## 单例模式

    class Person{
        constructor(){
        }
        say(){
            console.log('say...');
        }
    }
    Person.getInstance = (function(){
        let instance;
        return function(){
            if(!instance){
                instance = new Person()
            }
            return instance;
        }
    })();

    var p1 = Person.getInstance();
    p1.say();

    var p2 = Person.getInstance();
    p2.say();

    console.log(p1 === p2);


## 适配器(装饰器)模式

示例1


    class Adapter{
        constructor(){}
        outStandard(){
            return '国外电压 110v'
        }
    }

    class TargetAdapter{
        constructor(){
            this.adapter = new Adapter();
        }
        standard(){
            let outstandard = this.adapter.outStandard();
            return `${outstandard} => 国内电压 220v`;
        }
    }

    var target = new TargetAdapter();
    console.log(target.standard());


示例2

    class Circle{
        draw(){
            console.log('draw circle');
        }
    }

    class CircleDecorator{
        constructor(circle){
            this.circle = circle;
        }
        draw(){
            this.circle.draw();
            this.setColor();
        }
        setColor(){
            console.log('set circle color');
        }
    }

    var circle = new Circle();
    circle.draw();
    console.log('-------------------');
    var decorator = new CircleDecorator(circle);
    console.log(decorator);
    decorator.draw();


es7装饰器示例(需配置 babel plugins: babel-plugin-transform-decorators-legacy)

.babelrc

    {
      "plugins": ["transform-decorators-legacy"]
    }

    //es7 Decorator
    import { readonly, deprecate } from 'core-decorators';

    // function readonly(target,name,descriptor){
    //     descriptor.writable = false;
    //     return descriptor;
    // }

    class Character {
        constructor(name){
            this.name = name;
        }
        @readonly
        getname(){
            console.log(this.name);
        }
        @deprecate
        facepalm() {}
    }

    var p = new Character('blue');
    p.getname();
    p.facepalm();
    // p.getname = function(){}



## 代理模式

示例1

    class RealImg{
        constructor(filename){
            this.filename = filename;
            this.loadIn();
        }
        display(){
            console.log('display img ' + this.filename);
        }
        loadIn(){
            console.log('loading... ' + this.filename);
        }
    }

    class ProxyImg{
        constructor(filename){
            this.filename = new RealImg(filename);
        }
        display(){
            this.filename.display();
        }
    }

    var prox = new ProxyImg('example.png');
    prox.display();


es6 proxy 示例

    let star = {
        name: 'blue',
        age: 23,
        phone: 'star: 186xxxxxxxx'
    }

    let agent = new Proxy(star,{
        get(target, key){
            if(key == 'phone'){
                return 'agent: 138xxxxxxxx'
            }
            return target[key];
        },
        set(target, key, val){
            if(key === 'price'){
                if(val < 100000){
                    throw new Error('price is to low');
                }
            }
            target[key] = val;
            return true;
        }
    });

    console.log(agent.name);
    console.log(agent.phone);
    console.log(agent.price);
    agent.price = 140000;
    console.log(agent.price);



## 观察者模式

示例:

    class Substribe{
        constructor(){
            this.state = 0;
            this.observers = [];
        }
        getState(){
            return this.state;
        }
        setState(state){
            this.state = state;
            this.noticifyAll();
        }
        noticifyAll(){
            this.observers.forEach(obs => {
                obs.update()
            })
        }
        attach(obs){
            this.observers.push(obs);
        }
    }

    class Observer{
        constructor(name,substribe){
            this.name = name;
            this.substribe = substribe;
            this.substribe.attach(this);
        }
        update(){
            console.log(`${this.name} update state ${this.substribe.getState()}`);
        }
    }

    var s = new Substribe();
    var o1 = new Observer('o1',s);
    var o2 = new Observer('o2',s);
    s.setState(1);

自定义事件

    class MyEvent{
        constructor(){
            this.events = {};
        }
        on(type,fn){
            if(!this.events[type]){
                this.events[type] = [];
            }
            this.events[type].push(fn);
        }
        emit(type){
            this.events[type].forEach(fn => {
                fn();
            });
        }
    }

    var myevent = new MyEvent();
    myevent.on('aa',function(){
        console.log('aa');
    });
    myevent.on('bb',function(){
        console.log('bb');
    });
    myevent.on('aa',function(){
        console.log('from aa');
    });
    myevent.emit('aa');
    myevent.emit('bb');



## 迭代器模式(顺序访问一个有序集合)

示例

    class Iterator {
        constructor(container){
            this.list = container.list;
            this.index = 0;
        }
        next(){
            if(this.hasNext()){
                return this.list[this.index++];
            }
            return null;
        }
        hasNext(){
            if(this.index >= this.list.length){
                return false
            }
            return true;
        }
    }
    class Container{
        constructor(list){
            this.list = list;
        }
        getIterator(){
            return new Iterator(this);
        }
    }

    var container = new Container([1,2,3]);
    var iterator = container.getIterator()

    while(iterator.hasNext()){
        console.log(iterator.next());
    }


示例 es6 Iterator(简化版 使用 for ... of )


    function each(data){
        let iterator = data[Symbol.iterator]();

        let item = {done: false}
        while(!item.done){
            item = iterator.next();
            if(!item.done){
                console.log(item.value);
            }
        }
    }
    each(new Set([1,2,3]));



## 状态模式

    class TrafficLight{
        constructor(){
            this.state = null
        }
        getState(){
            return this.state;
        }
        setState(state){
            this.state = state;
        }
    }
    class LightState {
        constructor(color){
            this.color = color;
        }
        handler(context){
            console.log(`turn to ${this.color} light`);
            context.setState(this);
        }
    }

    var trafficligt = new TrafficLight();

    var red = new LightState('red');
    red.handler(trafficligt);
    console.log(trafficligt.getState());
    var green = new LightState('green');
    green.handler(trafficligt);
    console.log(trafficligt.getState());
    var yellow = new LightState('yellow');
    yellow.handler(trafficligt);
    console.log(trafficligt.getState());
