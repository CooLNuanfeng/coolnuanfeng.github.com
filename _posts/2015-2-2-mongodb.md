---
layout: post
title:  "mongoDB的一些常用命令"
date:   2015-2-2
description:  整理下关于mongoDB的一些常用的操作命令
keywords: mongodb
categories:
- blog
permalink: mongodb
---


###数据库常用指令

 1. mongo 进入命令行 或 mongo + 数据库名字
 2. show dbs 显示数据库列表
 3. use + 数据库名字  使用指定数据库
 4. show collections 显示当前数据库下的所有集合（类似表）
 5. db.collectionName.find()  显示集合里的数据
 6. db.collectionName.find({"name":"blue"})  查找集合中name = blue的数据的所有信息
 7. db.collectionName.remove({"name":"blue"}) 删除集合中name=blued的数据
 8. db.dropDatabase()  删除当前使用的数据库
 9. db.cloneDatabase('localhost') 将指定主机的数据库克隆到当前数据库
 10. db.copyDatabase("mydb","temp","127.0.0.1") 将本机的mydb数据库复制到temp数据库
 11. db.repairDatabase(); 修复当前数据库
 12. db或db.getName() 获取当前使用的数据库名称
 13. db.stats() 显示当前db状态
 14. db.getMongo() 显示当前db的链接地址

###集合collection 常用指令

 1. db.createCollection("collectionName"，{size:20,capped:5,max:100})  创建一个集合，通常mongdb可以自动创建
 2. db.getCollection('collectionName') 得到知道名称的集合
 3. db.getCollectionNames() 得到当前db的所有集合  === show collections
 4. db.printCollectionStats() 显示当前db所有集合的索引状态

###用户相关

 1. db.addUser('username')  添加一个用户，如：db.addUser('username','pwd123',true) 添加用户，密码 是否只读
 2. db.auth("username","password")  数据库认证，安全模式
 3. show users  显示当前所有用户
 4. db.removeUser("username") 删除用户

###其他关于集合常用命令

####PS : 假设集合名称为 person

 1. db.person.count() 当前集合的数据条数
 2. db.person.dataSize() 查看数据空间大小
 3. db.person.getDB() 得到当前集合的所的数据库
 4. db.person.stats() 得到当前集合的状态
 5. db.person.totalSize() 得到集合的总大小
 6. db.person.stroageSize() 得到存储的空间大小
 7. db.person.renameCollection('newName') 对集合重新命名
 8. db.person.drop() 删除当前集合
 9. db.person.find() 查询当前集合的所有数据
 10. db.person.distinct("name")  查询去掉后的当前集合中某列的重复数据
 11. db.person.find({"age":20}) 按条件查找，如：

> db.person,find({"age":{\$gt:22}}) 查找age大于22的记录
> db.person.find({"age":{\$lt:22}}) 查找age小于22的记录
> db.person.find({"age":{\$gte:23,\$lte:26}});  23<= age <=26

 12. db.person.find({"name": 正则表达式})  按指定正则查找
 13. db.person.find({},{"name":false})  查询person中所有数据，并去掉每条数据中的name信息 （过滤信息）
 14. db.person.find().sort({age:1}) 按年龄升序排序查找  -1 表示降序
 15. db.person.find({name:'zhangsan',age:21}) 查询name为zhansan age为21的数据相关信息
 16. db.person.find().limit(5)  查询前5条信息
 17. db.person.find().skip(10) 查询10条以后的数据
 18. db.person.find().limit(10).skip(5) 查询 5-10条数据
 19. db.person.find({$or:[{age:22},{age:25}]}) or查询 查找age为22或25
 20. db.person.find({age:{$gte:25}}).count() 查找age>=25 的结果数据的条数

###增删改操作

####PS : 假设集合名称为 person

 1. db.person.save({"name":"blue","age":23,"sex":true}) 添加一条数据
 2. db.person.update({age:25},{$set:{name:'blue'}},false,true) 跟新person中age为25 的所有数据的 name 为 blue
 3. db.person.update({name:'blue'},{$inc:{age:10}},false,true) 跟新person中name为blue 的所有数据的 age都加10
 4. b.person.update({name: 'Lisi'}, {$inc: {age: -5}, $set: {name: 'blue'}}, false, true); 更新person中name为lisi的所有数据的age 减5同时设置name为blue
 5. db.person.remove({name:“blue”，age:"23"}) 删除person中name为blue age为23的所有数据
 
 
 


    