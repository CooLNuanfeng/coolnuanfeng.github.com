---
layout: post
title:  "WEB缓存机制"
date:   2015-5-20
description:  WEB缓存机制
categories:
- blog
permalink: webCache
---

##WEB缓存机制

####什么是Web缓存

Web缓存是指一个Web资源（如html页面，图片，js，数据等）存在于Web服务器和客户端（浏览器）之间的副本。缓存会根据进来的请求保存输出内容的副本；当下一个请求来到的时候，如果是相同的URL，缓存会根据缓存机制决定是直接使用副本响应访问请求，还是向源服务器再次发送请求。比较常见的就是浏览器会缓存访问过网站的网页，当再次访问这个URL地址的时候，如果网页没有更新，就不会再次下载网页，而是直接使用本地缓存的网页。只有当网站明确标识资源已经更新，浏览器才会再次下载网页。

####Web缓存的作用

使用Web缓存的作用其实是非常显而易见的：
 1. 减少网络带宽消耗
 2. 降低服务器压力
 3. 减少网络延迟，加快页面打开速度


####Web缓存的类型

在Web应用领域，Web缓存大致可以分为以下几种类型：

 1. 数据库数据缓存
 2. 服务器端缓存
    -  代理服务器缓存
    - CDN缓存
 3. 浏览器端缓存
 4. Web应用层缓存


####Web缓存的工作原理

所有的缓存都是基于一套规则来帮助他们决定什么时候使用缓存中的副本提供服务（假设有副本可用的情况下，未被销毁回收或者未被删除修改）。这些规则有的在协议中有定义（如HTTP协议1.0和1.1），有的则是由缓存的管理员设置（如DBA、浏览器的用户、代理服务器管理员或者应用开发者）。

####浏览器端的缓存规则

对于浏览器端的缓存来讲，这些规则是在HTTP协议头和HTML页面的Meta标签中定义的。他们分别从新鲜度和校验值两个维度来规定浏览器是否可以直接使用缓存中的副本，还是需要去源服务器获取更新的版本。

 1、新鲜度（过期机制）：也就是缓存副本有效期。一个缓存副本必须满足以下条件，浏览器会认为它是有效的，足够新的，满足以下两个情况的一种，浏览器会直接从缓存中获取副本并渲染。
 

 - 含有完整的过期时间控制头信息（HTTP协议报头），并且仍在有效期内；
 - 浏览器已经使用过这个缓存副本，并且在一个会话中已经检查过新鲜度；


2、校验值（验证机制）：服务器返回资源的时候有时在控制头信息带上这个资源的实体标签Etag（Entity Tag），它可以用来作为浏览器再次请求过程的校验标识。如过发现校验标识不匹配，说明资源已经被修改或过期，浏览器需求重新获取资源内容。


####web缓存过程：

当一个用户发起一个静态资源请求的时候,浏览器会通过以下几步来获取资源:

 1. 当第一次发送请求，http返回200的状态码，如果没有关闭缓存请求的话（没标明不使用缓存，下述）则会在返回头中返回包含last-Modified以及Etag和Expires的字段（这些字段下面慢慢说），然后将文件保存在Cache目录下。
 2. 当后续请求该文件时候，先在本地查找该资源，如果在本地缓存找到对应的资源,但是不知道该资源是否过期或者已经过期, 则发一个http请求到服务器,然后服务器判断这个请求,如果请求的资源在服务器上没有改动过,则返回304, 让浏览器使用本地找到的那个资源，而如果当服务器发现请求的资源已经修改过,或者这是一个新的请求(本地无对应资源),服务器则返回该资源的数据,并且返回200。当然这个是指找到资源的情况下,如果服务器上没有这个资源,则返回404。
经过上面的流程梳理,我们基本了解整个缓存处理过程，不过对于前端来说，我们需求无非就是使用缓存或者不使用缓存，在了解下面内容之后我们再根据原理去慢慢实现我们的需求。


####浏览器缓存的控制

1、使用HTML Meta 标签
Web开发者可以在HTML页面的<head>节点中加入<meta>标签，代码如下：

		<meta http-equiv="Pragma" content="no-cache">

上述代码的作用是告诉浏览器当前页面不被缓存，每次访问都需要去服务器拉取。使用上很简单，但只有部分浏览器可以支持，而且所有缓存代理服务器都不支持，因为代理不解析HTML内容本身。


2、使用缓存有关的HTTP消息报头
在HTTP请求和响应的消息报头中，常见的与缓存有关的消息报头有：

**Cache-Control与Expires**：Cache-Control与Expires的作用一致，都是指明当前资源的有效期，控制浏览器是否直接从浏览器缓存取数据还是重新发请求到服务器取数据。只不过Cache-Control的选择更多，设置更细致，如果同时设置的话，其优先级高于Expires。
**Last-Modified/ETag与Cache-Control/Expires**：配置Last-Modified/ETag的情况下，浏览器再次访问统一URI的资源，还是会发送请求到服务器询问文件是否已经修改，如果没有，服务器会只发送一个304回给浏览器，告诉浏览器直接从自己本地的缓存取数据；如果修改过那就整个数据重新发给浏览器。

Cache-Control/Expires则不同，如果检测到本地的缓存还是有效的时间范围内，浏览器直接使用本地副本，不会发送任何请求。两者一起使用时，Cache-Control/Expires的优先级要高于Last-Modified/ETag。即当本地副本根据Cache-Control/Expires发现还在有效期内时，则不会再次发送请求去服务器询问修改时间（Last-Modified）或实体标识（Etag）了。

一般情况下，使用Cache-Control/Expires会配合Last-Modified/ETag一起使用，因为即使服务器设置缓存时间, 当用户点击“刷新”按钮时，浏览器会忽略缓存继续向服务器发送请求，这时Last-Modified/ETag将能够很好利用304，从而减少响应开销。

**Last-Modified与ETag**：你可能会觉得使用Last-Modified已经足以让浏览器知道本地的缓存副本是否足够新，为什么还需要Etag（实体标识）呢？HTTP1.1中Etag的出现主要是为了解决几个Last-Modified比较难解决的问题：

 1. Last-Modified标注的最后修改只能精确到秒级，如果某些文件在1秒钟以内，被修改多次的话，它将不能准确标注文件的新鲜度
 2. 如果某些文件会被定期生成，当有时内容并没有任何变化，但Last-Modified却改变了，导致文件没法使用缓存
 3. 有可能存在服务器没有准确获取文件修改时间，或者与代理服务器时间不一致等情形


####哪些请求不能被缓存？

无法被浏览器缓存的请求：

 1. HTTP信息头中包含Cache-Control:no-cache，pragma:no-cache，或Cache-Control:max-age=0等告诉浏览器不用缓存的请求
 2. 需要根据Cookie，认证信息等决定输入内容的动态请求是不能被缓存的
 3. 经过HTTPS安全加密的请求（有人也经过测试发现，ie其实在头部加入Cache-Control：max-age信息，firefox在头部加入Cache-Control:Public之后，能够对HTTPS的资源进行缓存）
 4. POST请求无法被缓存
 5. HTTP响应头中不包含Last-Modified/Etag，也不包含Cache-Control/Expires的请求无法被缓存


> 前面了解了Web缓存的运行机制极其重要性之后，我们可以从以下这些方面去努力改善我们的站点，保证缓存被最有效的利用，达到最佳的性能。

1、 同一个资源保证URL的稳定性
URL是浏览器缓存机制的基础，所以如果一个资源需要在多个地方被引用，尽量保证URL是固定的。同时，比较推荐使用公共类库，比如Google Ajax Library等，有利于最大限度使用缓存。

2、给Css、js、图片等资源增加HTTP缓存头，并强制入口Html不被缓存，假设服务器是 Apache，可以通过配置 .htaccess 文件来启用缓存：

		<IfModule mod_expires.c>
		    # 启用缓存
		    ExpiresActive On
		
		    #默认缓存时间为 1 天
		    ExpiresDefault "access plus 1 day"
		
		    #图片的缓存时间设置为一周
		    ExpiresByType image/x-icon "access plus 7 days"
		    ExpiresByType image/jpeg "access plus 7 days"
		    ExpiresByType image/png "access plus 7 days"
		    ExpiresByType image/gif "access plus 7 days"
		
		
		    ExpiresByType application/x-shockwave-flash "access plus 7 days"
		
		    #CSS 的缓存时间也设置成一周
		    ExpiresByType text/css "access plus 7 day"
		
		    #Javascript 的缓存时间同样设置为一周
		    ExpiresByType text/javascript "access plus 7 day"
		    ExpiresByType application/x-javascript "access plus 7 day"
		
		
		    ExpiresByType text/html "access plus 10 minutes"
		    ExpiresByType application/xhtml+xml "access plus 10 minutes"
		</IfModule>
		<IfModule mod_headers.c>
		    #设置代理缓存
		  <FilesMatch "\.(js|css|xml|gz|html)$">
		    Header append Vary: Accept-Encoding
		  </FilesMatch>
		</IfModule>


对于不经常修改的静态资源，比如Css，js，图片等，可以设置一个较长的过期的时间，或者至少加上Last-Modified/Etag，而对于html页面这种入口文件，不建议设置缓存。这样既能保证在静态资源不变了情况下，可以不重发请求或直接通过304避免重复下载，又能保证在资源有更新的，只要通过给资源增加时间戳或者更换路径，就能让用户访问最新的资源。

3、减少对Cookie的依赖
过多的使用Cookie会大大增加HTTP请求的负担，每次GET或POST请求，都会把Cookie都带上，增加网络传输流量，导致增长交互时间；同时Cache是很难被缓存的，应该尽量少使用，或者这在动态页面上使用。

4、减少对HTTPS加密协议的使用
通过HTTPS请求的资源，默认是不会被缓存的，必须通过特殊的配置，才能让资源得到缓存。建议只对涉及敏感信息的请求使用HTTPS传输，其他类似Css，Js，图片这些静态资源，尽量避免使用。

5、多用Get方式请求动态Cgi
如果动态脚本或CGI输入的内容在一定的时间范围内是固定的，或者根据GET参数相同，输入的内容相同，我们也认为请求是可以被缓存的，有以下几种方式，可以达到这个效果：

- 让动态脚本定期将内容改变时导出成静态文件，Web直接访问带有Last-Modified/Etag的静态文件
- 开发者可以通过代码给动态脚本的响应头中添加Cache-Control: max-age，告诉浏览器在过期前可以直接使用副本
- 通过代码给动态脚本的响应头添加Last-Modified/Etag信息，浏览器再次请求的时候，可以通过解析If-Modified-Since/If-None-Match得知浏览器是否存在缓存，由代码逻辑控制是否返回304



####Ajax中的缓存设置

Ajax调用就像任何其他的HTTP请求一样可以用来构建一个网页。然而，由于其动态的性质人们常常忽略了可以缓存它们而从中受益。

现代浏览器的HTTP系统和缓存系统要比Ajax的XMLHttpRequest对象更靠近底层. 在这个层面上，浏览器并不知道或关心Ajax请求。它只是服从正常的基于从服务器返回的HTTP响应头缓存规则。

如果你已经知道HTTP缓存，您可以将这种知识应用到Ajax的缓存。

当然，如果您使用Ajax的POST方法，这并不适用，因为POST请求不会被缓存。如果您的Ajax请求有安全性要求，你应该总是使用POST方法，例如：银行帐户的资金转账。 

示例代码如下：

		var xhr = $.ajax({
			url: 'data.php',
			type: 'GET',
			dataType: 'text',
			data: {name: 'blue'},
			headers:{  //设置请求头信息
				'Cache-Control' : "max-age=6",
				"Accept-Language" : "zh-CN,utf-8"
			}
			// beforeSend: function (xmlHttp) {    //设置请求头信息
			//     xmlHttp.setRequestHeader('Cache-Control',"max-age=5");
			//     xmlHttp.setRequestHeader("Accept-Language","zh-CN");
			// },
		})
		.done(function(res){
			console.log(res);
			console.log( xhr.getResponseHeader("Connection") );  //获取响应头
		})
		.fail(function() {
			console.log("error");
		})
		.always(function() {
			console.log("complete");
		});


####相关参考文档

 1. [web缓存机制系列---腾讯AlloyTeam](http://www.alloyteam.com/2012/03/web-cache-1-web-cache-overview/)
 2. [写给前端的http缓存详解](http://blog.csdn.net/u012545279/article/details/17679061)
 3. [翻译：web制作、开发人员需知的Web缓存知识](http://www.zhangxinxu.com/wordpress/2013/05/caching-tutorial-for-web-authors-and-webmasters/)
 4. [Web 前端优化 – 缓存页面组件](http://www.zfanw.com/blog/caching-in-http.html) 