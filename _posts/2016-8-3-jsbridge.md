---
layout: post
title:  "JSBridge 介绍及实现原理"
date:   2016-8-3
keywords: JSBridge HTML5 Native webApp ios android
categories:
- blog
permalink: jsbridge
---


####  **JSBridge 介绍**
JSBridge是一座用JavaScript搭建起来的桥，一端是web，一端是native。我们搭建这座桥的目的也很简单，让native可以调用web的js代码，让web可以 “调用” 原生的代码。

#### **实现原理**

Android实现：

我们来想想如何实现上面的功能。难点有这么几个：

	1、如何创建一个webview？如何在webview中加载网页？
	2、web如何调用android？

对于第一点，不是本文的重点，你可以参考这个[链接](http://blog.csdn.net/fengyuzhengfan/article/details/38326861)来自行解决。
对于第二点，我们继续分析。
web和android，就像是一个中国人和一个美国人。想要不同语言之间的人进行有效沟通的话，必须要满足以下两个基本要素。

你在听我说话 => 你在监听我的动作 =>javascript的某个行为会被java捕获到。我们手上有一本中英双译字典 => 我们之间有协议规范 => 对于传输的信息，javascript和java要用规定好的方式来编码和解码
先来看动作监听。javascript的哪些操作会被java捕获到？整个web页面都是运行在java提供的webview实例当中。javascript执行以下四种行为会被webview监听到，箭头后面是对应触发的Java方法。

	1、window.alert => onJSAlert
	2、window.confirm => onJSConfirm
	3、window.prompt => onJsPrompt
	4、window.location => shouldOverrideUrlLoading


好，动作监听我们解决了。接下来是要制定通信协议。说到通信协议，我们首先想到的是http协议，没错，我们可以仿照http协议制定我们自己的协议。比如http协议是这样子的： `http://www.baidu.com?param` 。 我们制定的协议是这样子： `ywjs://toast?message`
其中ywjs是scheme，代表我们用的是某种自定义的协议。toast是方法名，也就是我希望调用的是系统的toast方法。toast方法所需要的参数，也就是弹出的提示字符串。

相关文章介绍：

- [Android JSBridge的原理与实现](http://blog.csdn.net/sbsujjbcy/article/details/50752595)
- [SBridge实现原理探索](https://github.com/youngwind/blog/issues/76)
- [JsBridge 实现 JavaScript 和 Java 的互相调用](https://gold.xitu.io/entry/573534f82e958a0069b27646)



IOS实现：

IOS实现与Android第4种方式相同。Javascript调用Native，并没有现成的API可以直接拿来用，而是需要间接地通过一些方法来实现。UIWebView有个特性：在UIWebView内发起的所有网络请求，都可以通过delegate函数在Native层得到通知。这样，我们就可以在UIWebView内发起一个自定义的网络请求，通常是这样的格式：jsbridge://methodName?param1=value1&param2=value2

于是在UIWebView的delegate函数中，我们只要发现是jsbridge://开头的地址，就不进行内容的加载，转而执行相应的调用逻辑。

首先通过iframe发起一个网络请求，这个请求的作用是唤起Native APP的分享组件，将网页分享到朋友圈或分享给其他好友。JS端的核心代码如下所示：

	var url = 'jsbridge://doAction?title=分享标题&desc=分享描述&link=http%3A%2F%2Fwww.baidu.com';
	var iframe = document.createElement('iframe');
	iframe.style.width = '1px';
	iframe.style.height = '1px';
	iframe.style.display = 'none';
	iframe.src = url;
	document.body.appendChild(iframe);
	setTimeout(function() {
	    iframe.remove();
	}, 100);

相关文章介绍：

- [理解JSBridge](http://travelchu.com/2016/03/14/%E7%90%86%E8%A7%A3JSBridge/)
- [JSBridge——Web与Native交互之iOS篇](http://www.jianshu.com/p/9fd80b785de1)
- [通用的JSBridge的设计与实现](http://philonpang.github.io/blog/2015/05/29/jsbridge/)
