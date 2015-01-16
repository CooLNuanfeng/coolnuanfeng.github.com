---
layout: post
title:  "linux下一些常用工具的安装方法"
date:   2015-1-16
description:  整理下linux下安装一些常用工具的方法，方便以后安装和使用,使用的是virtualbox中安装的liunxmint
keywords: linuxmint install nginx php5 mysql,node,openFtp,openSSH,install rvm ruby sass,install mongoDB
categories:
- blog
permalink: linuxInstall
---

整理下在linux虚拟机中安装一些常用服务和工具的一些命令，方便以后的安装和使用。

####nginx相关服务的安装

 1. 执行： sudo apt-get install nginx php5-fpm
 2. 然后编辑配置文件，找到相应的文件 vim  /etc/nginx/site-available/default，找到location ~ .php$的地方，5行取消注释，变成这样：
 
         location ~ .php$ {

            #   fastcgi_split_path_info ^(.+.php)(/.+)$;
    
            #   # NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
    
            #
        
            #   # With php5-cgi alone:
    
            #   fastcgi_pass 127.0.0.1:9000;
    
            #   # With php5-fpm:
    
            fastcgi_pass unix:/var/run/php5-fpm.sock;
    
            fastcgi_index index.php;
    
            include fastcgi_params;
        }

 3. root下执行 service nginx restart，nginx安装成功
 4. 安装mysql，执行 sudo apt-get install mysql-server，安装后，重启service service mysql start

PHP服务的相关配置扩展：

- default文件中，找到 **index index.html index.htm**  这行，添加index.php,这就可以用php文件做默认主页
- default文件中，在server{}指示符的 location / {} 指示符内，加入 **autoindex on**; 当文件夹内没有index文件，就会自动索引文件。
- server{} 指示符的 root 行是文件根目录，自行修改就能把那个文件夹作为网站根目录

#### 安装node.js

 1. sudo add-apt-repository ppa:chris-lea/node.js 
 2. sudo apt-get update
 3. sudo apt-get install nodejs   //注意不是 node.js而是nodejs
 4. node -v
 5. sudo apt-get install npm  //如果没有npm命令安装npm

####openSSH和openFtp

 1. sudo apt-get install openssh-server   //openssh
 2. sudo apt-get install vsftpd         //openFtp
 3.  root登陆修改配置 vim /etc/vsftpd.conf
     - 修改为anonymous_enable=YES
     - 解开注释 write_enable=YES
     - 解开注释 ftp_banner=welcome to blah FTP service
 4. root下执行 service vsftpd restart  

####安装ruby和依赖ruby的sass

 1. sudo apt-get update
 2. sudo apt-get install curl
 3. \curl -L https://get.rvm.io | bash -s stable --ruby
 4. ruby -v
 5. gem install sass

####安装mongoDB

 1. sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
 2. echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' \| sudo tee /etc/apt/sources.list.d/mongodb.list
 3. sudo apt-get update
 4. sudo apt-get install mongodb
 5. sudo service mongodb restart
 6. mongo

    