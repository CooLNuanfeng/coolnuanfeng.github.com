---
layout: post
title:  "underscore的一些常用工具方法"
date:   2015-2-27
description:  关于underscore工具方法的一些常用方法的整理
keywords: underscore.js
categories:
- blog
permalink: underscore
---


####关于数组和对象通用的处理函数

 1、_.each 遍历每一个元素

    var arr1 = ['blue','tom','jack'];
    var json1 = {
        'name' : 'blue',
        'age' : 23
    };
    // 依次输出 0:blue, 1:tom, 2:jack
    _.each(arr1,function(value,key){
        console.log(key+':'+value); 
    }); 
    // 依次输出 name:blue, age:23
    _.each(json1,function(value,key){
        console.log(key+':'+value);
    }); 

2、_.map 遍历每一个元素，并进行相关操作，返回操作后的结果数组

    var arr2 = [3,4,5];
    var json2 = {
        0: 1,
        1:2,
        2:3
    };
    // [9,12,15]
    console.log( _.map(arr2,function(item){
        return item*3;
    }));
    // [3,6,9] 
    console.log( _.map(json2,function(item){
        return item*3;
    })); 


3、_.reduce 在一个变量的基础上累计， 如下 num 为第三个参数的值，本例为2，则2*3*4*5= 120

    var arr2 = [3,4,5];
    // 120
    console.log('reduce', _.reduce(arr2,function(num,item){
        return item*num;
    },2));

4、_.every 判断数组或json中的每个元素是否满足条件，如果每个都满足则返回true，有一个不满足返回false， _.some 则恰好相反，有一个返回true，一个都没有返回false

    var arr3 = [1,2,3,4,5,6,7,8];
    var json3 = {
        'one':2,
        'two':4,
        'three':6
    };
    // true
    console.log(_.every(json3,function(item){
        return item>=2 ;
        // return  item%==2
    }));
    //true
    console.log(_.some(arr3,function(item){
        return item>=6 ;
        // return  item%==2
    }));

5、_.size 返回集合成员的数量，可以用来判断json的长度
    
    var arr4 = [1,2,3,4,5,6,7,8];
    var json4 = {
        'one':2,
        'two':4,
        'three':6
    };
    console.log(_.size(json4)); //3
    console.log(_.size(arr4)); //8

6、_.filter 对数据过滤只返回符合条件的部分，与 _.reject 相反， _.reject 返回不符合条件的部分

    var arr5 = [1,2,3,4,5,6,7,8];
    var json5 = {
        'one':2,
        'two':4,
        'three':6,
        'four': 7,
        'five':9
    };
    //[2,4,6,8]
    console.log(_.filter(arr5,function(item){
        return item%2 == 0;
    }));
    //[7,9]
    console.log(_.reject(json5,function(item){
        return item%2 == 0;
    }));

7、_.find 依次对数据进行操作，只返回符合条件的第一个元素，如果都没有返回undefined

    // 7
    console.log(_.find(json5,function(item){
        return item%2 == 1;
    }));

8、_.contains 查看某个值是否在数组或json中

    console.log(_.contains(json5,10)); //false

9、_.countBy 依次对集合的每个成员进行某种操作，将操作结果相同的成员作一类，最后返回一个对象，表明每种操作结果对应的成员数量。

    //{"false":2,"true":3}
    console.log(_.countBy(json5,function(item){
        return item%2==0;
    }));

10、 _.toArray 将json对象转为数组

    var json6 = {
        'one':'blue',
        'two':'yellow',
        'three':'red',
        'four': 'green',
        'five':'orange'
    };
    //['blue','yellow','red','green','orange']
    console.log(_.toArray(json6));

11、_.pluck 将多个对象的某个属性的值，提取成一个数组

    var arr7 = [{name : 'moe', age : 40}, {name : 'larry', age : 50}, {name : 'curly', age : 60}];
    var json7 = {
        'person1' :{
            'name' : 'moe',
            'age': 23
        },
        'person2' :{
            'name' : 'larry',
            'age': 25
        },
        'person3' :{
            'name' : 'curly',
            'age': 26
        }
    };
    //['moe','larry','curly']
    console.log(_.pluck(arr7,'name'));
    //['moe','larry','curly']
    console.log(_.pluck(json7,'name'));

12、_.where 遍历返回符合条件的结果集

    var arr8 = [{name : 'moe', age : 24}, {name : 'larry', age : 25}, {name : 'curly', age : 26},{name : 'tom', age : 23},{name : 'blue', age : 26},{name : 'jack', age : 23}];
    var json8 = {
        "name" : ['moe','larry','curly','tom','blue','jack'],
        "age" : [24,25,26,23,26,23],
    };
    //[{name : 'tom', age : 23},{name : 'jack', age : 23}]
    console.log(_.where(arr8,{age:23}));

13、_.shuffle 随机打乱一个数组或 json

    var arr9 = [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15];
    var json9 = {
        'name' : 'blue',
        'age': 23
    };
    console.log(_.shuffle(arr9));

14、_.max _.min 取出最大和最小元素，可以自定义比较器

    var arr10 = [52,23,18,46,37,78,98,66,100];
    var json10 = [
        {"name":"blue","age":23},
        {"name":"tom","age":24},
        {"name":"moe","age":30},
        {"name":"jack","age":26}
    ];
    console.log(_.max(arr10),'_.max'); //100
    //{"name":"moe","age":30}
    console.log(_.max(json10,function(obj){
        return obj.age;  //自定义比较器
    }),'_.max');
    console.log(_.min(arr10),'_.min'); //18
    //{"name":"blue","age":23}
    console.log(_.min(json10,function(obj){
        return obj.age;  //自定义比较器
    }),'_.max');

15、_.sortBy 自定义排序

    console.log(_.sortBy(arr10),'_.sortBy');
    console.log(_.sortBy(json10,function(num){
        return num.age;
    }),'_.sortBy');


####数组的操作

1、_.first _.last 取出数组的第一个和最后一个

    var arr11 = [52,23,34,18,46];
    console.log(_.first(arr11),'_.first'); //52
    console.log(_.last(arr11),'_.last'); //46

2、_.initial _.rest 分别为去掉数组的后面n个和去掉数组前面n个

    //[52,23,34]
    console.log(_.initial(arr11,2),'_.initial');
    //[14,46]
    console.log(_.rest(arr11,3),'_.rest');

3、_.indexOf 取出数组中某个值第一次出现在数组中的索引， _.lastIndexOf 取出数组中某个值最后一次出现在数组中的索引

    var arr12 = [52,23,18,46,18,68,34];
    console.log(_.indexOf(arr12,18), '_.indexOf'); //2
    console.log(_.lastIndexOf(arr12,18), '_.lastIndexOf'); //4

4、_.range([start],stop,[step]) 创建一个有规律的数组

    //[0,2,4,6,8,10,12,14,16,18]
    console.log(_.range(0, 20, 2), '_.range'); 
    //[0,-3,-6,-9,-12,-15,-18,-21,-24,-27]
    console.log(_.range(0, -30, -3), '_.range');
    //[-27,-24,-21,-18,-15,-12,-9,-6,-3,0]
    console.log((_.range(0, -30, -3)).reverse(), '_.range');

5、_.compact 去除数组中的 0 空值 false 和 undefined 等 假值

    var arr13 = [0, '0', 12, 'blue',false,'true',undefined,'',true];
    //['0',12,'blue','true',true]
    console.log(_.compact(arr13),'_.compact'); 
    //注意  字符串 0 被保留

6、_.without 去掉数组中指定的值

    var arr14 = [1,3,5,3,6,5,8,6,9];
    //[1,6,8,6]
    console.log(_.without(arr14,3,5,9), '_.without');

7、_.union 并集 _.intersection 交集 _.difference差集

    var arr15 = [1,2,3,4,5];
    var arr16 = [2,6,8,4,3];
    //_.union 将多个数组合并，重复的只出现一次
    console.log(_.union(arr15,arr16),'_.union'); //[1,2,3,4,5,6,8]
    //取多个数组的交集
    console.log(_.intersection(arr15,arr16), '_.intersection'); //[2,3,4]
    //取出第一个数组与后面的数组中都不同的部分
    console.log(_.difference(arr15,arr16), '_.difference'); //[1,5]

8、_.zip 与 groupBy 相反,合成的每个元素都是数组,  _.object 合成json对象

    var arr17=['blue','red','orange','yellow']
    var arr18 = ['蓝','红','橙','黄'];
    //[['blue','蓝'],['red','红'],['orange','橙'],['yellow','黄']]
    console.log(_.zip(arr17,arr18), '_.zip');
    //{blue: "蓝",orange: "橙",red: "红",yellow: "黄"}
    console.log(_.object(arr17,arr18), '_.object');


####对象部分

1、_.keys 将json的keys 提取出组成一个数组

    var json20 = {
        'one': 1,
        "two" :2,
        "three" : 3
    };
    //['one','two','three']
    console.log(_.keys(json20), '_.keys');

2、_.values  将json的values提取出组成一个数组

    // [1,2,3]
    console.log(_.values(json20), '_.values');

3、_.pairs 将json 中的每个值分离为单个数组,最后返回这些单个数组组成的一个数组

    //[['one',1],['two',2],['three',3]]
    console.log(_.pairs(json20), '_.pairs');

4、_.invert  将 json 的 key 和 value 位置互换

    //{1: "one",2: "two",3: "three"}
    console.log(_.invert(json20), '_.invert');

5、_.functions()  返回对象上的所有函数名

    var jsonFn = {
        fn1: function(){},
        fn2: function(){},
        fn3: function(){}
    };
    //['fn1','fn2','fn3']
    console.log(_.functions(jsonFn), '_.functions');

6、_.extend  复制对象的所有属性到目标对象上，覆盖已有属性

    var json21 ={
        'one': 1,
        "two" :2,
        "three" : 3
    };
    var json22 = {
        "four": 4,
        "five" :5,
        "three" : 4
    };
    //{five: 5,four: 4,one: 1,three: 4,two: 2}
    console.log(_.extend(json21,json22), '_.extend');

7、_.defaults 复制对象的所有属性到目标对象上，跳过已有属性

    //{five: 5,four: 4,one: 1,three: 4,two: 2}
    console.log(_.defaults(json21,json22), '_.defaults');


8、_.pick _.omit: 返回一个对象的副本，保留指定的属性或去掉指定的属性

    var json23 = {
        'name' : 'blue',
        'age' : 23,
        'job' : 'web'
    };
    //{job: "web",name: "blue"}
    console.log(_.pick(json23,'name','job'),'_.pick');
    //{age: 23,name: "blue"}
    console.log(_.omit(json23,'job'),'_.omit');

9、_.has  对象是否有指定属性

    // true
    console.log(_.has(json23, 'job'), '_.has');

10、_.isEqual 判断两个对象是否是 值 相等
    
    var json24 = {
        'name' : 'blue',
        'age' : 23,
        'job' : ['web','php']
    };
    var json25 = {
        'name' : 'blue',
        'age' : 23,
        'job' : ['web','php']
    };
    console.log(json24 == json25); //false
    console.log(_.isEqual(json24, json25),'_.isEqual'); //true


11、判断对象类型的方法，下面反回值都是true

    console.log(_.isEmpty({}),'_.isEmpty');
    console.log(_.isArray([1,2,3]),'_.isArray');
    console.log(_.isObject({}),'_.isObject');
    console.log((function(){ return _.isArguments(arguments); })(1, 2, 3),'_.isArguments');
    console.log(_.isFunction(console.log),'_.isFunction');
    console.log(_.isString("moe"),'_.isString');
    console.log(_.isNumber(8.4 * 5),'_.isNumber');
    console.log(_.isFinite(-101),'_.isFinite');
    console.log(_.isBoolean(true),'_.isBoolean'); 
    console.log(_.isDate(new Date()),'_.isDate');
    console.log(_.isNaN(NaN),'_.isNaN');
    console.log(_.isNull(null),'_.isNull');
    console.log(_.isUndefined(undefined),'_.isUndefined');


####补充部分

1、产生指定范围的随机数

    console.log( _.random(0, 100) ); 



    