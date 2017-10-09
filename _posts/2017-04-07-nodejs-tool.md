---
layout: post
title:  "nodejs 命令行小工具"
date:   2017-04-07
keywords: nodejs 命令行小工具.
categories:
- blog
permalink: nodejs-tool
---



开始
-------------

#### Step one

创建文件夹 cd 到文件夹 npm init 填写相应内容

#### Step two

在 package.json 中 添加 bin 项 的启动文件，如下：

	"bin" : {
        "[启动命令]" : "[启动入口文件]"
    }

#### Step three

在当前文件的根目录执行 npm link 将 bin 下 的指令添加到全局，这样可以直接在本地调试你的代码，本质是做了两个软连接，最后指向了当前目录，这样就可以全局调用该命令


#### Step four

添加注释，编写代码

	#!/usr/bin/env node
	...code




commander
---------------------

commander.js是TJ所写的一个工具包，其作用是让node命令行程序的制作更加简单。commander.js中命令行有两种常用api，一个叫做option，意为选项。一个叫做command，意为命令。


	program
	   .version('0.0.1')
	   .option('-C, --chdir <path>', 'change the working directory')
	   .option('-c, --config <path>', 'set config path. defaults to ./deploy.conf')
	   .option('-T, --no-tests', 'ignore test hook')
	 program
	   .command('setup')
	   .description('run remote setup commands')
	   .action(function() {
	     console.log('setup');
	   });
	 program
	   .command('exec <cmd>')
	   .description('run the given remote command')
	   .action(function(cmd) {
	     console.log('exec "%s"', cmd);
	   });
	 program
	   .command('teardown <dir> [otherDirs...]')
	   .description('run teardown commands')
	   .action(function(dir, otherDirs) {
	     console.log('dir "%s"', dir);
	     if (otherDirs) {
	       otherDirs.forEach(function (oDir) {
	         console.log('dir "%s"', oDir);
	       });
	     }
	   });
	 program
	   .command('\*')
	   .description('deploy the given env')
	   .action(function(env) {
	     console.log('deploying "%s"', env);
	   });
	 program.parse(process.argv);




- 通过option设置的选项可以通过program.chdir或者program.noTests来访问。
- 通过command设置的命令通常在action回调中处理逻辑

#### version

用法： .version('x.y.z')
用于设置命令程序的版本号

#### option

用法：`.option('-n, --name <name>', 'your name', 'GK')`

- 第一个参数是选项定义，分为短定义和长定义。用|，,， 连接。
参数可以用<>或者[]修饰，前者意为必须参数，后者意为可选参数。
- 第二个参数为选项描述
- 第三个参数为选项参数默认值，可选。


#### command

用法：`.command('init <path>', 'description')`

- command的用法稍微复杂，原则上他可以接受三个参数，第一个为命令定义，第二个命令描述，第三个为命令辅助修饰对象。
- 第一个参数中可以使用<>或者[]修饰命令参数
- 第二个参数可选。
	- 当没有第二个参数时，commander.js将返回Command对象，若有第二个参数，将返回原型对象。
	- 当带有第二个参数，并且没有显示调用action(fn)时，则将会使用子命令模式。
	- 所谓子命令模式即，./pm，./pm-install，./pm-search等。这些子命令跟主命令在不同的文件中。
- 第三个参数一般不用，它可以设置是否显示的使用子命令模式。

#### description

用法：`.description('command description')`
用于设置命令的描述

#### action

用法：`.action(fn)`
用于设置命令执行的相关回调。fn可以接受命令的参数为函数形参，顺序与command()中定义的顺序一致。

#### parse

用法：`.parse(process.argv)`
此api一般是最后调用，用于解析 process.argv。

#### outputHelp

用法：`.outputHelp()`
一般用于未录入参数时自动打印帮助信息。



实例DEMO
--------------------


- [我的一些小工具](https://www.npmjs.com/~coolnuanfeng)
- [有道百度翻译工具](https://github.com/CooLNuanfeng/translator)
- [网易云课堂灌水小工具](http://study.163.com/course/introduction/1003588036.htm#/courseDetail)
- [nodejs-tools](https://github.com/swxy/nodejs-tools)





相关文档
------------------

- [Node.js 命令行程序开发教程](http://www.ruanyifeng.com/blog/2015/05/command-line-with-node.html)
- [commander.js API](http://tj.github.io/commander.js/)
- [使用commander.js做一个Nodejs命令行程序](http://blog.gejiawen.com/2016/09/21/make-a-node-cli-program-by-commander-js/)
- [Commander写自己的Nodejs命令](http://blog.fens.me/nodejs-commander/)
- [使用commander.js创建nodejs命令行工具](http://zhiye.li/2015-01-15-intro-to-commander.js-zh-cn.html)
- [node命令行小工具开发【翻译小工具】](https://segmentfault.com/a/1190000008714075)
