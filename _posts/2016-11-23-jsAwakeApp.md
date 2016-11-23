---
layout: post
title:  "Javascript 唤起 App 的回调处理"
date:   2016-11-23
keywords: JavaScript 唤起 App, js 拉起App, 拉起App的回调处理
categories:
- blog
permalink: jsAwakeApp
---


JavaScript 唤起(拉起)App 通常有两种方式

- location.href
- iframe

具体原理可参考这篇[文章](http://coolnuanfeng.github.io/jsbridge)


下面主要介绍唤起成功/失败时分别对应的回调处理，代码以iframe为例(貌似也只能是iframe)：


	function openApp(openUrl, callback) {
		function checkOpen(cb) {
			var clickTime = +(new Date());
			function check(elsTime) {
				if (elsTime > 7000) { // 7000 = 5000 + toast的 2000
					cb(1); //超过7秒切回时状态
				} else {
					cb(0); //大于5秒且少于7秒切回时 或 失败状态
				}
			}
			//启动间隔20ms运行的定时器，并检测累计消耗时间是否超过3000ms，超过则结束
			var count = 0,
				intHandle;
			intHandle = setInterval(function() {
				count++;
				var elsTime = +(new Date()) - clickTime;
				if (count >= 250 || elsTime > 5000) {
					clearInterval(intHandle);
					check(elsTime);
				}
			}, 20);
		}
		var ifr = document.createElement('iframe');
		ifr.src = openUrl;
		ifr.style.display = 'none';
		if (callback) {
			checkOpen(function(opened) {
				callback && callback(opened);
			});
		}
		document.body.appendChild(ifr);
		setTimeout(function() {
			document.body.removeChild(ifr);
		}, 2000);
	}


当给页面按钮点击时，调用openApp，同时传入唤起的URL和回调处理函数。调用如下：

	$('#btn').on('click',function(){
	    openApp('url',function(status){
	        if(!status){ //失败
	            toast('aa');
	        }else{ //成功
	            toast('bb');
	        }
	    })
	});



代码解释说明如下：

#### 当唤起失败的情况

- 调用openApp会创建 iframe 并插入body中开始拉起App ，如果callback传入则启动checkOpen中的定时器。
- 启动定时器后，每20ms count加一，由于未安装App或其他情况导致拉不起来，cout累计到250(即5秒) checkOpen中的判断条件满足(此时第二个条件elsTime也满足)，会执行check函数，此时的elsTime应该理论是5000，总之是不会满足check的添加判断的，固执行 cb(0)，参数0，表示失败，通过回调函数的参数形式返回。弹出 toast('aa')


#### 当唤起成功时，且在很长时间后(该例子为超过7s)切回

- 调用openApp会创建 iframe 并插入body中开始拉起App ，如果callback传入则启动checkOpen中的定时器。
- 启动定时器后，此时由于已经拉起App, checkOpen中的定时器会暂停，count不会累加，在很长时间后切回到该页面(超过7s)，定时器继续工作，由于count不会累加，只能是 elsTime 满足条件，从而进入check函数，进入check函数后也一定会满足check中的条件，固会执行cb(1)函数。弹出 toast('bb')


#### 当唤起成功时，且在很短时间后(该例子为5s内)切回

- 调用openApp会创建 iframe 并插入body中开始拉起App ，如果callback传入则启动checkOpen中的定时器。
- 启动定时器后，此时由于已经拉起App, checkOpen中的定时器会暂停，cout不会累加，很短时间后(该例子为5s内)切回，定时器继续工作，count累加已经不再准确，主要是获取时间的 elsTime 满足时，才进入check函数，会执行 cb(0)函数。弹出toast('aa')



具体事例效果参照 	QQ一键登录和 JD一键登录
