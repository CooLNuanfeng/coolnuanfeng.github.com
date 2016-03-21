---
layout: post
title:  "一些移动端遇到的问题汇总"
date:   2016-3-18
keywords: 移动端问题汇总
categories:
- blog
permalink: mobileIssue
---





#### Q1: IOS中阻止当页面滚动到顶部或底部时页面整体滚动
IOS中浏览页面当页面滚动到顶部或底部时继续滚动会将整个页面滑动，阻止这种效果可以使用下面的函数,参数为最外层容器的选择器

    preventPageScroll('.container');
    function preventPageScroll(id){
        var el = document.querySelector(id);
        var sy = 0;
        el.addEventListener('touchstart', function (e) {
          sy = e.pageY;
        });
        el.addEventListener('touchmove', function (e) {
          var down = (e.pageY - sy > 0);
          //top
          if (down && el.scrollTop <= 0) {
            e.preventDefault();
          }
          //bottom
          if (!down && el.scrollTop >= el.scrollHeight - el.clientHeight) {
            e.preventDefault();
          }
        });
    }


#### Q2: 使用zepto常见的点透问题
对于点透问题有以下方案可供解决参考

1. 引入fastclick.js，[源码地址](https://github.com/ftlabs/fastclick)，使用参考github

2. 使用touchend代替tap，并阻止掉默认行为


	    $(dom).on('touchend',function(ev){
	        // to doing
	        ev.preventDefault();
	    });

3. 延迟一定时间(300ms+)处理

	    $(dom).on('tap',function(){
	       setTimeout(function(){
	           // to doing
	       },320)
	    });

#### Q3:获取窗口宽度

获取窗口宽度有以下一些方法（这里假设已经将样式 reset）：

- window.innerWidth
- document.body.clientWidth
- document.body.offsetWidth

zepto中有

- \$(window).width()
- \$(document).width()

曾经遇到过在页面第一次打开时 window 和 document 获取的width 是相同的，但是当再次刷新页面时获取的两个宽度却不再相同，所以为了避免出现样式错乱，**尽量使用 document 获取 width**


#### Q4: Android部分机型中给img设置border-radius失效

![radius](http://coolnuanfeng.github.io/assets/images/radius.png)

解决给img外嵌一个元素

    //结构
    <div>
        <img src="">
    </div>
    //样式
    div{
        display: inline-block;
        border-radius: 50%;
        border: 4px solid #FF7000;
    }
    img{
        vertical-align: top;
    }


#### Q5: 部分机型中圆角元素，背景颜色会溢出

![border-radius](http://coolnuanfeng.github.io/assets/images/border-radius.png)

解决给该元素添加下面样式

		{background-clip:padding-box;}


#### Q6：圆角使用Animation 做loading动画时，圆角背景溢出

解决使用一个同等大小的圆角图片做蒙版遮罩


		{
			background-color: #F9CEAC;
	        border-radius: 32px 0 0 32px;
	        -webkit-mask-image: url(./image/btn_mask.png);
	    }


蒙版图片![btn_mask](http://coolnuanfeng.github.io/assets/images/btn_mask.png)

#### Q7: CSS 三角在 Android 上显示为方块

解决：可能是对这个三角使用了圆角，去掉 `border-radius` 即可



	    {
	        border: 10px solid transparent;
	        border-left-color: #000;
	        /*border-radius: 2px;*/
	    }


#### Q8:Android 上使用 svg 作为 background-image 时显示模糊

解决：设置 `background-size`


	    {
	        -webkit-background-size: 100%;
	        background-size: 100%;
	    }


#### Q9: IOS中 `:active` 样式不生效

Safari 默认禁用了元素的 active 样式，我们通过声明 touchstart 来覆盖默认事件，就可以让 active 样式重新激活。

解决：

    document.addEventListener("touchstart", function() {},false);
    //或者 body标签添加 ontouchstart
    <body ontouchstart="">


此外，默认点击按钮会有一个灰色的外框，通过这段 CSS 可以清除：

    html {
        -webkit-tap-highlight-color: rgba(0,0,0,0);
    }


#### Q10: 多行文字超出截断需要出现省略号方法

单行文本截断并末尾出现省略号一般写法是：

	    {
	        white-space: nowrap;
	        overflow: hidden;
	        text-overflow: ellipsis;
	    }

多行文版截断并出现省略号的纯CSS方法，其中的 `-webkit-line-clamp: 2` 即用来控制文本超出两行时截断并出现省略号。 在使用中如果出现第三行文字露一点头出来的问题，设置合理的 `line-height` 即可解决


	    {
	        display: -webkit-box;
	        overflow: hidden;
	        text-overflow: ellipsis;
	        -webkit-line-clamp: 2;
	        -webkit-box-orient: vertical;
	    }



#### Q11: 1px 线条、边框

在高版本设备中可以直接设置 0.5px 边框，但底版不能友好支持，一个比较合适的方法：原理是把原先元素的 border 去掉，然后利用 `:before` 或者 `:after` 重做 `border` ，并 `transform` 的 `scale` 缩小一半，原先的元素相对定位，新做的 `border` 绝对定位。

- 单条 border

	    .hairlines li{
	        position: relative;
	        border:none;
	    }
	    .hairlines li:after{
	        content: '';
	        position: absolute;
	        left: 0;
	        background: #000;
	        width: 100%;
	        height: 1px;
	        -webkit-transform: scaleY(0.5);
	                transform: scaleY(0.5);
	        -webkit-transform-origin: 0 0;
	                transform-origin: 0 0;
	    }

- 四条 border

	    .hairlines li{
	        position: relative;
	        margin-bottom: 20px;
	        border:none;
	    }
	    .hairlines li:after{
	        content: '';
	        position: absolute;
	        top: 0;
	        left: 0;
	        border: 1px solid #000;
	        -webkit-box-sizing: border-box;
	        box-sizing: border-box;
	        width: 200%;
	        height: 200%;
	        -webkit-transform: scale(0.5);
	        transform: scale(0.5);
	        -webkit-transform-origin: left top;
	        transform-origin: left top;
	    }

样式使用的时候，也要结合 JS 代码，判断是否 Retina 屏

    if(window.devicePixelRatio && devicePixelRatio >= 2){
        document.querySelector('ul').className = 'hairlines';
    }

更多请参考[这篇文章](http://jinlong.github.io/2015/05/24/css-retina-hairlines/)


#### Q12: 当模块使用系统的横向滚动时，不想显示出系统的滚动条样式

解决:

- Android

		::-webkit-scrollbar{ opacity: 0; }

- Ios

	    //html
	    <div class="wrap">
	        <div class="box"></div>
	    </div>
	    //css
	    .wrap{ height: 100px; overflow: hidden; }
	    .box{ width: 100%; height: -webkit-calc(100% + 5px); overflow-x: auto; overflow-y: hidden; -webkit-overflow-scrolling: touch; }

原理：`.box` 元素的横向滚动条通过其外层元素 `.wrap` 的 `overflow:hiden` 来隐藏。 （5px 是 iOS 上滚动条元素的高度）


#### Q13:横向滚动的元素，滑动时有时图片显示不出来/文字显示不出来

解决：给每个横滑的元素块使用硬件加速

    li{ -webkit-transform: translateZ(0); }

#### Q14:使用 animation 动画后，页面上 overflow:auto 的元素滚动条不能滑动

解决：不使用 translate 方式的动画，换为使用 left/top 来实现元素移动的动画

#### Q15:上下滑动页面时候，页面元素消失

解决：检查是否使用了 fadeIn 的 animation，如有则 fill-mode 使用 backwards 模式


	    { -webkit-animation: fadeIn 0.5s ease backwards; }



#### Q16: 页面上数字自动变成了可以点击的链接

解决：在页面 `<head>` 里添加


    <meta name="format-detection" content="telephone=no">

#### Q17:input 在 iOS 中圆角、内阴影去不掉
解决：

    input{ -webkit-appearance: none; border-radius: 0; }

#### Q18:焦点在 input 时，placeholder 没有隐藏
解决：

    input:focus::-webkit-input-placeholder{ opacity: 0;}

#### Q19:input 输入框调出数字输入键盘
解决：

    <input type="number" />
    <input type="number" pattern="[0-9]*" />
    <input type="tel" />

分别对应下图的1、2、3
![keyboard_number](http://coolnuanfeng.github.io/assets/images/keyboard_number.png)

需要注意的是，单独使用 type="number" 时候， iOS 上出现并不是九宫格的数字键盘，如果需要九宫格的数字键盘，可选择使用 2、3 的方法。 1、2、3 在 Android 上均可以唤起九宫格的数字键盘
