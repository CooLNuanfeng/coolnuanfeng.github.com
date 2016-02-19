---
layout: post
title:  "requestAnimationFrame的使用"
date:   2016-2-19
keywords: requestAnimationFrame的使用,requestAnimationFrame实例
categories:
- blog
permalink: requestAnimationFrame
---


####requestAnimationFrame的使用

以往JS控制的动画大多使用setInterval 或者setTimeout 每隔一段时间刷新元素的位置，来达到动画的效果，但是这种方式并不能准确地控制动画帧率，尽管主流的浏览器（相当一部分的浏览器的显示频率是16.7ms,）对于这两个函数实现的动画都有一定的优化，但是这依然无法弥补它们性能问题。主要原因是因为JavaScript的单线程机制使得其可能在有阻塞的情况下无法精确到毫秒触发。

requestAnimationFrame()方法正是为了满足高性能动画的需求而提供的API，通过setInterval方法控制的动画其调用的间隔由程序员设置，而requestAnimationFrame()无须设置调用间隔， 它自动紧跟浏览器的绘制的帧率。

window.requestAnimationFrame()这个方法是用来在页面重绘之前，通知浏览器调用一个指定的函数，以满足开发者操作动画的需求。**这个方法接受一个函数为参数，该函数会在重绘前调用。该函数只会被传入一个DOMHighResTimeStamp参数，这个参数指示当前被 requestAnimationFrame 序列化的函数队列被触发的时间。**

**注意**: 如果想得到连贯的逐帧动画，函数中必须重新调用 requestAnimationFrame()。

> 个人简单理解：由于大部分浏览器对帧显示频率为16.7ms，传统setTimeout和setInterval如果要求在16.7ms之内渲染一帧动画的话，浏览器来不及处理会有丢帧现象出现卡顿，而requestAnimationFrame则是在每次渲染一帧动画前调用，利用多次调用实现动画，不会丢帧，更流畅



一个实例代码：

	//从网页中获取一个元素
	var box = document.getElementById('box')
	shake(box, 500, 15)

	//接受三个参数：动画元素，持续时间，晃动距离
	function shake(elm, dur, distance) {
	  if (elm) {    
	     var dur = dur || 500
	     var distance = distance || 10   
	//保存原样式
	    var original_css = elm.style.cssText
	    elm.addEventListener('click', ani, false)
	  } else {
	    return 'no param'
	  }

	  function ani() {
	    var start = null
	   requestAnimationFrame(act)
	//requestAnimationFrame每次调用向callback中传入一个时间戳time_stamp，时间戳为每次调用的时间点
	    function act(time_stamp) {
	      if(start === null) start = time_stamp
	      var elapsed = time_stamp - start
	      if ((elapsed / dur) < 1) {
	      //乘以4PI，来回往复两次， 使用正弦函数得到比例
	        elm.style.transform = 'translateX(' + distance * Math.sin((elapsed / dur) * 4 * Math.PI) + 'px' + ')'
	        //调用该方法将返回一个ID值用于结束调用
	        var time_id = requestAnimationFrame(act)
	      } else {
	      //恢复原样式，停止动画
	        elm.style.cssText = original_css
	        cancelAnimationFrame(time_id)
	      }
	    }
	  }
	}


更多资料：

- [CSS3动画那么强，requestAnimationFrame还有毛线用？](http://www.zhangxinxu.com/wordpress/2013/09/css3-animation-requestanimationframe-tween-%E5%8A%A8%E7%94%BB%E7%AE%97%E6%B3%95/)
- [性能更好的js动画实现方式——requestAnimationFrame](http://www.cnblogs.com/2050/p/3871517.html)
- [HTML5探秘：用requestAnimationFrame优化Web动画](http://www.webhek.com/requestanimationframe/)
