---
layout: post
title:  "整理移动WEB常用资源"
date:   2015-11-16
keywords: 移动web mobile web meta CSS Flex 移动开发常用技巧
categories:
- blog
permalink: mobileTech
---


####**meta篇**
1、视图宽度

	<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no"/>

其中 

`width=device-width` : 是设置视窗宽度为设备视窗宽度，还可以固定宽度，例如： width=640 则是640px的宽度（常见于微信）；
`initial-scale=1.0` ：设置缩放比例为1.0；
`minimum-scale=1.0` 和 `maximum-scale=1.0`：最小缩放比例和最大缩放比例；
`user-scalable=no` ：禁止用户自由缩放，user-scalable 默认值为 yes 。

提示：刚刚那个是带全部参数的，一般常用的，有 `user-scalable=no` 就不用使用 `minimum-scale=1.0` 和 `maximum-scale=1.0` 来强制禁止缩放了。

如下：

	<meta name="viewport" content="width=device-width,initial-scale=1.0,user-scalable=no"/>


2、自动识别格式

	<meta name="format-detection" content="telephone=no"/>

content 里面的参数：`telephone=no` 是禁止浏览器自动识别手机号码，`email=no` 是禁止浏览器自动识别Email。

3、通常移动端meta设置

	<meta name="viewport" content="width=device-width,initial-scale=1.0,user-scalable=no"/>
	<meta name="format-detection" content="telephone=no"/>
	<meta name="format-detection" content="email=no"/>


####**CSS部分**

一些常用的初始化设置样式

	body {
    font-family: "Helvetica Neue", Helvetica, STHeiTi, sans-serif; /*使用无衬线字体*/
	}
	
	a, img {
	    -webkit-touch-callout: none; /*禁止长按链接与图片弹出菜单*/
	}
	
	html, body {
	    -webkit-user-select: none; /*禁止选中文本*/
	    user-select: none;
	}
	
	button,input,optgroup,select,textarea {
	    -webkit-appearance:none; /*去掉webkit默认的表单样式*/
	    outline: none; /* 去掉蓝色的边线 */
	}
	
	a,button,input,optgroup,select,textarea {
	    -webkit-tap-highlight-color:rgba(0,0,0,0); /*去掉a、input和button点击时的蓝色外边框和灰色半透明背景*/
	}
	
	input::-webkit-input-placeholder {
	    color:#ccc; /*修改webkit中input的planceholder样式*/
	}
	
	input:focus::-webkit-input-placeholder {
	    color:#f00; /*修改webkit中focus状态下input的planceholder样式*/
	}
	
	body {
	    -webkit-text-size-adjust: 100%!important; /*禁止IOS调整字体大小*/
	}
	
	input::-webkit-input-speech-button {
	    display: none; /*隐藏Android的语音输入按钮*/
	}


####**Flex部分**

这里假设flex容器为 .box ，子元素为 .item 。

1、定义容器为flex布局

	.box{
	    display: -webkit-flex; /*webkit*/
	    display: flex;
	}
	/*行内flex*/
	.box{
	    display: -webkit-inline-flex; /*webkit*/
	    display:inline-flex;
	}

2、容器样式

	.box{
	    flex-direction: row | row-reverse | column | column-reverse;
	    /*主轴方向：左到右（默认） | 右到左 | 上到下 | 下到上*/
	
	    flex-wrap: nowrap | wrap | wrap-reverse;
	    /*换行：不换行（默认） | 换行 | 换行并第一行在下方*/
	
	    flex-flow: <flex-direction> || <flex-wrap>;
	    /*主轴方向和换行简写*/
	
	    justify-content: flex-start | flex-end | center | space-between | space-around;
	    /*主轴对齐方式：左对齐（默认） | 右对齐 | 居中对齐 | 两端对齐 | 平均分布*/
	
	    align-items: flex-start | flex-end | center | baseline | stretch;
	    /*交叉轴对齐方式：顶部对齐（默认） | 底部对齐 | 居中对齐 | 上下对齐并铺满 | 文本基线对齐*/
	
	    align-content: flex-start | flex-end | center | space-between | space-around | stretch;
	    /*多主轴对齐：顶部对齐（默认） | 底部对齐 | 居中对齐 | 上下对齐并铺满 | 上下平均分布*/
	}


3、子元素样式

	.item{
	    order: <integer>;
	    /*排序：数值越小，越排前，默认为0*/
	
	    flex-grow: <number>; /* default 0 */
	    /*放大：默认0（即如果有剩余空间也不放大，值为1则放大，2是1的双倍大小，以此类推）*/
	
	    flex-shrink: <number>; /* default 1 */
	    /*缩小：默认1（如果空间不足则会缩小，值为0不缩小）*/
	
	    flex-basis: <length> | auto; /* default auto */
	    /*固定大小：默认为0，可以设置px值，也可以设置百分比大小*/
	
	    flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
	    /*flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto，*/
	
	    align-self: auto | flex-start | flex-end | center | baseline | stretch;
	    /*单独对齐方式：自动（默认） | 顶部对齐 | 底部对齐 | 居中对齐 | 上下对齐并铺满 | 文本基线对齐*/
	}

####**其他资源整合**

1、自定义苹果图标

在网站文件根目录放一个 apple-touch-icon.png 文件，苹果设备保存网站为书签或桌面快捷方式时，就会使用这个文件作为图标，文件尺寸建议为：180px × 180px。

2、自定义favicon

	<link rel="icon" href="favicon.ico" mce_href="favicon.ico" type="image/x-icon">

3、定义浏览器点击行为

	<a href="tel:020-10086">打电话给:020-10086</a>
	<a href="sms:10086">发短信给: 10086</a>
	<a href="mailto:me@22278.club">发送邮件: me@22278.club</a>

4、定义上传文件类型和格式

	<input type=file accept="image/*">

上面的文件上传框中，accept 可以限制上传文件的类型，参数为 image/* 是所有图片类型，点击会弹出图库，也可以指定图片格式，参数设置成 image/png 则可以限制图片类型为png；参数如果为 video/* 则是选择视频的意思；accept 还可以设置多个文件格式，语法为 accept="image/gif, image/jpeg" ；

5、使用box-shadow改变(挡住)表单自动填充后的黄色

	input:-webkit-autofill, textarea:-webkit-autofill, select:-webkit-autofill{
	    box-shadow:inset 0 0 0 1000px #fff;
	}

6、用CSS实现单行文本文本省略号
		
	overflow: hidden;
	white-space: nowrap;
	text-overflow: ellipsis;

7、用CSS实现多行文本省略号

	overflow : hidden;
	text-overflow: ellipsis;
	display: -webkit-box;
	-webkit-line-clamp: 2;
	-webkit-box-orient: vertical;

