---
layout: post
title:  "gulp+bourbon实现愉快的Sass样式书写"
date:   2015-7-2
keywords: gulp,sass,bourbon
description:  通过使用gulp和bourbon框架，来愉快的通过sass编写我们的样式
categories:
- blog
permalink: bourbon
---



### bourbon 是一个简单轻便的mixin Sass库，可以帮助我们快速开发样式，尤其是CSS3的样式，更多使用方法请查看官方的 [bourbon-api](http://bourbon.io/docs/),下面简单记录了如何结合gulp在项目中使用。

#### **Step one:**  创建一个工程目录，我的目录结构如下：	

	mypro
	.
	├── sass
	│   ├── css
	│   └── scss
	└── test.html	

#### **Step two:** 安装相应的包文件

	npm install gulp
	npm install gulp-sass
	npm install node-neat
	npm install node-bourbon

#### **Step three:** 创建我的gulpfile.js 文件，代码如下：

	'use strict';
	//引入所需要的模块
	var gulp = require('gulp');
	var sass = require('gulp-sass');
	var neat = require('node-neat').includePaths;
	//创建一个Sass的编译任务 
	gulp.task('sass', function () {
	  gulp.src('./sass/scss/*.scss')  // 此处对应我要编译的目标源文件的目录
	    .pipe(sass({
	    	outputStyle: 'expanded', //nested,expanded,compact,compressed编译方式
	    	includePaths: neat
	    }))
	    .pipe(gulp.dest('./sass/css')); // 此处对应我要编译的目标文件的目录
	});
	//创建一个监听Sass变化的任务
	gulp.task('sass:watch', function () {
	  gulp.watch('./sass/scss/*.scss', ['sass']);
	});
	//配置默认的gulp执行任务
	gulp.task('default', ['sass', 'sass:watch']);


#### **Step four:** 创建我的sass样式文件

分别根据bourbon的框架写了两个测试的sass文件：myapp.scss和myStyle.scss， **注意：** sass文件中引入要使用的bourbon的sass库。

myapp.scss:

	@import "bourbon";
	@import "neat";
	
	input {
	  width: 500px;
	  height: 30px;
	  color: #333;
	  @include placeholder {
	    color: #ddd;
	  }
	}
	
	#{$all-text-inputs} {
	  border: 1px solid #f00;
	  outline: none;
	}
	
	#{$all-text-inputs-focus},
	#{$all-text-inputs-hover} {
	  border: 1px solid #0f0;
	}
	
	#{$all-text-inputs-active} {
	  border: 1px solid #00f;
	}
	
	@include keyframes(fadeIn) {
	  from {
	    @include transform(scale(0));
	  }
	  to {
	    @include transform(scale(1));
	  }
	}

myStyle.scss: 

	@import "bourbon";
	@include hidpi(1.5) {
	  width: 20em;
	}


#### **Step five:** 运行gulp任务

命令行控制台中直接运行 gulp 命令即可。编译后的 css 文件如下：

myapp.css

	html {
	  box-sizing: border-box;
	}
	
	*,
	*::after,
	*::before {
	  box-sizing: inherit;
	}
	
	input {
	  width: 500px;
	  height: 30px;
	  color: #333;
	}
	
	input::-webkit-input-placeholder {
	  color: #ddd;
	}
	
	input::-moz-placeholder {
	  color: #ddd;
	}
	
	input:-moz-placeholder {
	  color: #ddd;
	}
	
	input:-ms-input-placeholder {
	  color: #ddd;
	}
	
	input[type="color"], input[type="date"], input[type="datetime"], input[type="datetime-local"], input[type="email"], input[type="month"], input[type="number"], input[type="password"], input[type="search"], input[type="tel"], input[type="text"], input[type="time"], input[type="url"], input[type="week"], textarea {
	  border: 1px solid #f00;
	  outline: none;
	}
	
	input[type="color"]:focus, input[type="date"]:focus, input[type="datetime"]:focus, input[type="datetime-local"]:focus, input[type="email"]:focus, input[type="month"]:focus, input[type="number"]:focus, input[type="password"]:focus, input[type="search"]:focus, input[type="tel"]:focus, input[type="text"]:focus, input[type="time"]:focus, input[type="url"]:focus, input[type="week"]:focus, textarea:focus,
	input[type="color"]:hover, input[type="date"]:hover, input[type="datetime"]:hover, input[type="datetime-local"]:hover, input[type="email"]:hover, input[type="month"]:hover, input[type="number"]:hover, input[type="password"]:hover, input[type="search"]:hover, input[type="tel"]:hover, input[type="text"]:hover, input[type="time"]:hover, input[type="url"]:hover, input[type="week"]:hover, textarea:hover {
	  border: 1px solid #0f0;
	}
	
	input[type="color"]:active, input[type="date"]:active, input[type="datetime"]:active, input[type="datetime-local"]:active, input[type="email"]:active, input[type="month"]:active, input[type="number"]:active, input[type="password"]:active, input[type="search"]:active, input[type="tel"]:active, input[type="text"]:active, input[type="time"]:active, input[type="url"]:active, input[type="week"]:active, textarea:active {
	  border: 1px solid #00f;
	}
	
	@-webkit-keyframes fadeIn {
	  from {
	    -webkit-transform: scale(0);
	  }
	  to {
	    -webkit-transform: scale(1);
	  }
	}
	
	@-moz-keyframes fadeIn {
	  from {
	    -moz-transform: scale(0);
	  }
	  to {
	    -moz-transform: scale(1);
	  }
	}
	
	@keyframes fadeIn {
	  from {
	    -webkit-transform: scale(0);
	    -moz-transform: scale(0);
	    -ms-transform: scale(0);
	    -o-transform: scale(0);
	    transform: scale(0);
	  }
	  to {
	    -webkit-transform: scale(1);
	    -moz-transform: scale(1);
	    -ms-transform: scale(1);
	    -o-transform: scale(1);
	    transform: scale(1);
	  }
	}


myStyle.css

	@media only screen and (-webkit-min-device-pixel-ratio: 1.5), only screen and (min--moz-device-pixel-ratio: 1.5), only screen and (-o-min-device-pixel-ratio: 1.5/1), only screen and (min-resolution: 144dpi), only screen and (min-resolution: 1.5dppx) {
	  width: 20em;
	}

	

This's working!  另外附上整个项目的目录结构：

	mypro
	.
	├── gulpfile.js
	├── js
	├── node_modules
	│   ├── gulp
	│   │   ├── bin
	│   │   ├── CHANGELOG.md
	│   │   ├── completion
	│   │   ├── index.js
	│   │   ├── lib
	│   │   ├── LICENSE
	│   │   ├── node_modules
	│   │   ├── package.json
	│   │   └── README.md
	│   ├── gulp-sass
	│   │   ├── CHANGELOG.md
	│   │   ├── CONTRIBUTING.md
	│   │   ├── index.js
	│   │   ├── LICENSE
	│   │   ├── node_modules
	│   │   ├── package.json
	│   │   └── README.md
	│   ├── node-bourbon
	│   │   ├── CHANGELOG.md
	│   │   ├── index.js
	│   │   ├── LICENSE
	│   │   ├── Makefile
	│   │   ├── node_modules
	│   │   ├── package.json
	│   │   ├── README.md
	│   │   └── test
	│   └── node-neat
	│       ├── CHANGELOG.md
	│       ├── index.js
	│       ├── LICENSE
	│       ├── Makefile
	│       ├── node_modules
	│       ├── package.json
	│       ├── README.md
	│       └── test
	├── sass
	│   ├── css
	│   │   ├── myapp.css
	│   │   └── myStyle.css
	│   └── scss
	│       ├── myapp.scss
	│       └── myStyle.scss
	└── test.html
	
	18 directories, 29 files



附录：更多详细参考文档

 - [npm-bourbon](https://www.npmjs.com/package/node-bourbon)
 - [gulp](https://github.com/gulpjs/gulp/blob/master/docs/getting-started.md)
 - [gulp-sass](https://www.npmjs.com/package/gulp-sass)
 - [other](https://laravel-news.com/2014/03/using-bourbon-neat-with-gulp/)

