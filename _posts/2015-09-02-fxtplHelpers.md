---
layout: post
title:  "繁星前端模板引擎的扩展方法"
date:   2015-9-2
keywords: fxtpl.helpers 繁星模板引擎扩展 前端模板 fxtpl.js 繁星模板
description:  fxtpl.helpers 繁星模板引擎扩展 前端模板 fxtpl.js 繁星模板
categories:
- blog
permalink: fxtplHelpers
---

#### 整理下繁星前端模板引擎的扩展方法

极速，简洁，高效，轻量级，直接在html中嵌入模板，更多[详请](https://github.com/koen301/fxtpl)，下面添加几种日常工作中经常遇到的辅助方法。

1、截取字符长度，溢出...

	Fxtpl.helpers.cut = function(str, limitLen){
	    var s = String(str).replace(/([^\x00-\xff])/g,'\f$1');
	    return limitLen >= s.length ? str : s.substr(0, limitLen-3).replace(/\f/g, '')+'...';
	}

2、格式化时间日期

	Fxtpl.helpers.dateFormat = function(date, format){
	    if(!date){
	        format = date;
	        date = new Date();
	    }
	    var map = {
	        "M": date.getMonth() + 1, //月份 
	        "D": date.getDate(), //日 
	        "h": date.getHours(), //小时 
	        "m": date.getMinutes(), //分 
	        "s": date.getSeconds(), //秒 
	        "q": Math.floor((date.getMonth() + 3) / 3), //季度 
	        "S": date.getMilliseconds() //毫秒 
	    };
	    format = format.replace(/([YMDhmsqS])+/g, function(all, t){
	        var v = map[t];
	        if(v !== undefined){
	            if(all.length > 1){
	                v = '0' + v;
	                v = v.substr(v.length-2);
	            }
	            return v;
	        }
	        else if(t === 'Y'){
	            return (date.getFullYear() + '').substr(4 - all.length);
	        }
	        return all;
	    });
	    return format;
	}


3、格式化数字，保留几位数字

	Fxtpl.helpers.numberFormat = function(number,numberLen){
		var number = Number(number);
		return number.toFixed(numberLen);
	}


4、替换文本中的指定的文字或内容

	Fxtpl.helpers.replaceText = function(str, regStr, aimStr, globalBtn){ //str要替换的模板文字,regStr要替换文字,aimStr要替换成的目标字符,globalBtn 是否全局匹配默认为true
		globalBtn = globalBtn==undefined ? true : globalBtn;
		if(globalBtn){
			var re = new RegExp(regStr,"g");
		}else{
			var re = new RegExp(regStr);
		}
		var s = String(str).replace(re,aimStr);
		return s;
	}


使用示例：

	<!DOCTYPE html>
	<html lang="en">
	<head>
		<meta charset="UTF-8">
		<title>Document</title>
	</head>
	<body>
	</body>
	<script src="jquery.js"></script>
	<script src="fxtpl.js"></script>
	<script src="fxtpl.helpers.js"></script>
	<script type="text/template" id="temp">
	市场价：<del>{{$Mtprice }}</del> 价格：{{$Price}} 省 {{($Mtprice-$Price)|numberFormat 2}} 
	<p>{{$text|replaceText '方法','methods'| cut 120 |replaceText '模板','methods'}}</p>
	<p>{{$date|dateFormat 'YYYY年MM月DD日 hh时mm分ss秒'}}</p>
	</script>
	<script>
	Fxtpl.config.leftTag = '{{';//定义新的左分隔符
	Fxtpl.config.rightTag = '}}';//定义新的右分隔符
	var data = {
		'Mtprice':52.2578,
		'Price' : "45.755669",
		'text':'Fxtpl模板的辅助方法集合（变量调节器）,Fxtpl.helpers是一个对象，你可以认为它是模板辅助方法的原型prototype，默认只有“escape”方法,你可以通过类似扩展原型的方式，扩展模板的辅助方法',
		'date': new Date()
	}
	var html = Fxtpl.render('temp',data);
	$('body').html(html);
	</script>
	</html>


欢迎继续补充。。

