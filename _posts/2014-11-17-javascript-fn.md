---
layout: post
title:  "一些常用的javascript方法总汇"
date:   2014-11-18
keywords: 一些常用的javascript方法总汇
description:  一些常用的javascript方法总汇
categories:
- blog
permalink: javascript-fn
---

###前端是个不断积累的过程

>下面整理了些 js 的方法，方便以后工作

----

 1、一维数组去重

    var arr=[1,2,2,4,2];
    for(var i=0; i<arr.length; i++){
        for(var j=i+1; j<arr.length; j++){
            if(arr[i] == arr[j]){
                arr.splice(j,1);
                j--;
            }
        }
    }
    alert(arr);

2、原生的Ajax封装 (get/post)

    function ajax(url, fnOnSucc, fnOnFaild){
        var oAjax=null;
        
        //1.初始化Ajax对象
        if(window.ActiveXObject){
            oAjax=new ActiveXObject("Msxml2.XMLHTTP")||new ActiveXObject("Microsoft.XMLHTTP");
        }else{
            oAjax=new XMLHttpRequest();
        }
        
        //2.建立连接
        oAjax.open('get', url, true);
        
        //3.监控请求状态
        oAjax.onreadystatechange=function (){
            //readyState->Ajax对象内部的状态
            //status->服务器返回的请求结果
            if(oAjax.readyState==4){
                //alert('请求完成，请求结果是：'+oAjax.status);
                //alert(oAjax.responseText);
                if(oAjax.status==200){
                    if(fnOnSucc){
                        fnOnSucc(oAjax.responseText);
                    }
                }else{
                    if(fnOnFaild){
                        fnOnFaild(oAjax.status);
                    }
                }
            }
            //alert(oAjax.readyState);
            //alert(typeof oAjax.status);
        };
        
        //4.发送请求
        oAjax.send();
        
        //5.*清理
        //oAjax.onreadystatechange=null;
        //oAjax=null;
    }
     
    function ajaxPost(url, sData, fnOnSucc, fnOnFaild){
        var oAjax=null;
        
        //1.初始化Ajax对象
        if(window.ActiveXObject){
            oAjax=new ActiveXObject("Msxml2.XMLHTTP")||new ActiveXObject("Microsoft.XMLHTTP");
        }else{
            oAjax=new XMLHttpRequest();
        }
        
        //2.建立连接
        oAjax.open('post', url, true);
        
        //3.监控请求状态
        oAjax.onreadystatechange=function (){
            //readyState->Ajax对象内部的状态
            //status->服务器返回的请求结果
            if(oAjax.readyState==4){
                //alert('请求完成，请求结果是：'+oAjax.status);
                //alert(oAjax.responseText);
                if(oAjax.status==200){
                    if(fnOnSucc){
                        fnOnSucc(oAjax.responseText);
                    }
                }else{
                    if(fnOnFaild){
                        fnOnFaild(oAjax.status);
                    }
                }
            }
            //alert(oAjax.readyState);
            //alert(typeof oAjax.status);
        };
        
        //4.发送请求
        oAjax.setRequestHeader('content-type', 'urlencode');
        oAjax.send(sData);
        
    }




3、生成随机字符串 (0~9,a~z)

    /**
        * @function:generateRandomAlphaNum->生成随机的字符串
        * @param:len->生存随机字符串的长度
        * @tdd->IE6-9 chrome Firefox通过测试
    **/
    function generateRandomAlphaNum(len) {
        var rdmString = "";
        //toSting接受的参数表示进制，默认为10进制。36进制为0-9 a-z
        for (var i=0; rdmString.length < len; rdmString += Math.random().toString(36).substr(2));
        return rdmString.substr(0, len);
    }
    alert(generateRandomAlphaNum(5)) //示例





4、将伪数组转化为数组

    /**
        * 这里把符合以下条件的对象称为伪数组 
        * 1，具有length属性 
        * 2，按索引方式存储数据 
        * 3，不具有数组的push,pop等方法 
    **/
     
    //将伪数组转换成数组，ps:已测试IE6-10、chrome、Firefox
    function toArray(arg){
      try {
        //转换arguments
        return Array.prototype.slice.call(arg);
      }catch(e){
        //转换元素节点元素
        var arr = [];
        for(var i = 0; i < arg.length; i++){
          arr[i] = arg[i];
        }
        return arr;
      }
    }
    //用法示例
    var $arr=$('.box');
    console.log(toArray($arr).push('a'));   //4


5、判断输入的字符是否全部是中文字符

    /**
    * @descrition: 测试给定的参数是否全部为中文字符，如"中test"不会通过 。
    * @param->str : 传入的参数，类型为字符串。
    * @return : true表示全部为中文,false为不全是中文，或没有中文，包含了中文标点。
    * */
    function isChinese(str) {
        var pattern = /^[\u0391-\uFFE5]+$/g;
        return pattern.test(str);
    }
    alert(isChinese('chinese中文'));


6、判断输入的参数是否为空

    /**
    * @description : 判断输入的参数是否为空
    * @return : true表示为输入参数为空 
    * */
    function isEmpty (str) {
        //空引用  空字符串  空输入
        return str == null || typeof str == "undefined" || str.trim() == "" ? true : false;
    }


7、身份证检测

    /**
    * @descrition: 这个函数对输入的参数检查时候是合格的身份证号码，其身份证有效性无法判断。检测的颗粒度可以定制。
    * @param->str : 待验证的参数
    * @return : true是合格的身份证   false为不合法的身份证
    **/
    function checkIdCard (num) {
            num = num.toUpperCase();
     
            var cityCode = {11: "北京", 12: "天津", 13: "河北", 14: "山西", 15: "内蒙古", 21: "辽宁", 22: "吉林", 23: "黑龙江 ", 31: "上海", 32: "江苏", 33: "浙江", 34: "安徽", 35: "福建", 36: "江西", 37: "山东", 41: "河南", 42: "湖北 ", 43: "湖南", 44: "广东", 45: "广西", 46: "海南", 50: "重庆", 51: "四川", 52: "贵州", 53: "云南", 54: "西藏 ", 61: "陕西", 62: "甘肃", 63: "青海", 64: "宁夏", 65: "新疆", 71: "台湾", 81: "香港", 82: "澳门", 91: "国外 "};
     
            if(!cityCode[num.substr(0,2)]){
                alert("地址编码错误");
                return false;
            }
            //身份证号码为15位或者18位，15位时全为数字，18位前17位为数字，最后一位是校验位，可能为数字或字符X。
            if (!(/(^\d{15}$)|(^\d{17}([0-9]|X)$)/.test(num))) {
                //alert('输入的身份证号长度不对，或者号码不符合规定！\n15位号码应全为数字，18位号码末位可以为数字或X。');
                return false;
            }
            //校验位按照ISO 7064:1983.MOD 11-2的规定生成，X可以认为是数字10。
            //下面分别分析出生日期和校验位
            var len, re;
            len = num.length;
            if (len == 15) {
                re = new RegExp(/^(\d{6})(\d{2})(\d{2})(\d{2})(\d{3})$/);
                var arrSplit = num.match(re);
     
                //检查生日日期是否正确
                var dtmBirth = new Date('19' + arrSplit[2] + '/' + arrSplit[3] + '/' + arrSplit[4]);
                var bGoodDay;
                bGoodDay = (dtmBirth.getYear() == Number(arrSplit[2])) && ((dtmBirth.getMonth() + 1) == Number(arrSplit[3])) && (dtmBirth.getDate() == Number(arrSplit[4]));
                if (!bGoodDay) {
                    //alert('输入的身份证号里出生日期不对！');
                    return false;
                }else {
                    //将15位身份证转成18位
                    //校验位按照ISO 7064:1983.MOD 11-2的规定生成，X可以认为是数字10。
                    var arrInt = new Array(7, 9, 10, 5, 8, 4, 2, 1, 6, 3, 7, 9, 10, 5, 8, 4, 2);
                    var arrCh = new Array('1', '0', 'X', '9', '8', '7', '6', '5', '4', '3', '2');
                    var nTemp = 0, k;
                    num = num.substr(0, 6) + '19' + num.substr(6, num.length - 6);
                    for (k = 0; k < 17; k++) {
                        nTemp += num.substr(k, 1) * arrInt[k];
                    }
                    num += arrCh[nTemp % 11];
                    return true;
                }
            }
            if (len == 18) {
                re = new RegExp(/^(\d{6})(\d{4})(\d{2})(\d{2})(\d{3})([0-9]|X)$/);
                var arrSplit = num.match(re);
     
                //检查生日日期是否正确
                var dtmBirth = new Date(arrSplit[2] + "/" + arrSplit[3] + "/" + arrSplit[4]);
                var bGoodDay;
                bGoodDay = (dtmBirth.getFullYear() == Number(arrSplit[2])) && ((dtmBirth.getMonth() + 1) == Number(arrSplit[3])) && (dtmBirth.getDate() == Number(arrSplit[4]));
                if (!bGoodDay) {
                    //alert(dtmBirth.getYear());
                    //alert(arrSplit[2]);
                    //alert('输入的身份证号里出生日期不对！');
                    return false;
                }else {
                    //检验18位身份证的校验码是否正确。
                    //校验位按照ISO 7064:1983.MOD 11-2的规定生成，X可以认为是数字10。
                    var valnum;
                    var arrInt = new Array(7, 9, 10, 5, 8, 4, 2, 1, 6, 3, 7, 9, 10, 5, 8, 4, 2);
                    var arrCh = new Array('1', '0', 'X', '9', '8', '7', '6', '5', '4', '3', '2');
                    var nTemp = 0, k;
                    for (k = 0; k < 17; k++) {
                        nTemp += num.substr(k, 1) * arrInt[k];
                    }
                    valnum = arrCh[nTemp % 11];
                    if (valnum != num.substr(17, 1)) {
                        //alert('18位身份证的校验码不正确！应该为：' + valnum);
                        return false;
                    }
                    return true;
                }
            }
            return false;
    }


8、电话、QQ、邮箱的检测

    /**
    * @descrition:判断输入的参数是否是个合格的固定电话号码。
    * @param:str->待验证的固定电话号码。
    * @return : true表示验证合格。
    **/
    var isfixedphone = function(str) {
        /**
         *
         * @desctition:规则->区号3-4位，号码7-8位,可以有分机号，分机号为3-4为，格式如下："0775-85333333-123"
         * 
         */
         var pattern =  /^\d{3,4}-\d{7,8}(-\d{3,4})?$/;
         return pattern.test(str);
    }

    /**
    * @descrition:判断输入的参数是否是个合格的手机号码，不能判断号码的有效性，有效性可以通过运营商确定。
    * @param:str ->待判断的手机号码
    * @return: true表示合格输入参数
    * 
    */
    var isCellphone = function(str) {
        /**
        *@descrition:手机号码段规则
        * 13段：130、131、132、133、134、135、136、137、138、139
        * 14段：145、147
        * 15段：150、151、152、153、155、156、157、158、159
        * 17段：170、176、177、178
        * 18段：180、181、182、183、184、185、186、187、188、189
        * 
        */
        var pattern =  /^(13[0-9]|14[57]|15[012356789]|17[0678]|18[0-9])\d{8}$/;
        return pattern.test(str);
    }

    /**
    * @descrition:判断输入的参数是否是个合格的QQ号码
    * @param->str:待验证的参数
    * @return: true验证成功
    */
    var isQQ = function(str) {
            /**
            *@descrition:规则
            * 1-9开头，最少5位。
            */
            var pattern = /^[1-9][0-9]{4,}$/
            return pattern.test(str);
    }
     
    /**
    *
    * @descrition:判断输入的参数是否是国内合法的邮编地址(ps:国内不包含国外的邮编)
    * @link: http://www.youbianku.com/%E9%A6%96%E9%A1%B5
    * @param: str为待验证的邮编号码
    * @return: true表示为合法的邮编号码
    * 
    */
    var isPostcode = function(str) {
            //国内邮编以0-8开头的6为数字
            var pattern = /^[0-8]\d{5}$/;
            return pattern.test(str);
            
    }
    
    /**
    * @descrition:判断输入的参数是否是个合格标准的邮箱,并不能判断是否有效，有效只能通过邮箱提供商确定。
    * @param:str ->待验证的参数。
    * @return -> true表示合格的邮箱。
    * */
    var isEmail = function(str){
        /**
        * @descrition:邮箱规则
        * 1.邮箱以a-z、A-Z、0-9开头，最小长度为1.
        * 2.如果左侧部分包含-、_、.则这些特殊符号的前面必须包一位数字或字母。
        * 3.@符号是必填项
        * 4.右则部分可分为两部分，第一部分为邮件提供商域名地址，第二部分为域名后缀，现已知的最短为2位。最长的为6为。
        * 5.邮件提供商域可以包含特殊字符-、_、.
        */
        var pattern = /^([a-zA-Z0-9]+[-_.]?)*[a-zA-Z0-9]+@([a-zA-Z0-9]+[-_.]?)*[a-zA-Z0-9]+\.[a-zA-Z]{2,6}$/;
        return pattern.test(str);
    }


9、判断是否为合理的IP/URL

    /**
    * @descrition:判断是否是合理的IP地址
    * @param:str->待验证的IP地址
    * @return :true合理的IP地址
    **/
    var isIP = function (str) {
      var pattern = /^(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/;
     
      return pattern.test(str);
    }
     
    /**
    * @descrition:判断输入的参数是否是个合格的URL,由于url的灵活性和多样性，一下代码并不能测试所有的url都是合法的
    * @param:str->待判断的url参数
    * @return ：true表示符合改正则。
    **/
    var isURL = function (str) {
        var strRegex = "^((https|http|ftp|rtsp|mms)?://)"
                + "?(([0-9a-z_!~*'().&=+$%-]+: )?[0-9a-z_!~*'().&=+$%-]+@)?" //ftp的user@
                + "(([0-9]{1,3}.){3}[0-9]{1,3}" // IP形式的URL- 199.194.52.184
                + "|" // 允许IP和DOMAIN（域名）
                + "([0-9a-z_!~*'()-]+.)*" // 域名- www.
                + "([0-9a-z][0-9a-z-]{0,61})?[0-9a-z]." // 二级域名
                + "[a-z]{2,6})" // first level domain- .com or .museum
                + "(:[0-9]{1,4})?" // 端口- :80
                + "((/?)|"
                + "(/[0-9a-z_!~*'().;?:@&=+$,%#-]+)+/?)$";
        var re = new RegExp(strRegex);
     
        return re.test(str);
    }


10、js判断浏览器类别

    /*
    * @link :http://browserhacks.com
    * IE篇
    */
    var ie6=!-[1,]&&!window.XMLHttpRequest; //IE6为true,其他都为false
    var isIE = !!window.ActiveXObject; //ie6-10都为true, ie11为false
    /*返回IE6-9的版本号*/
    var _IE = (function(){
        var v = 3, div = document.createElement('div'), all = div.getElementsByTagName('i');
        while (
            div.innerHTML = '<!--[if gt IE ' + (++v) + ']><i></i><![endif]-->',
            all[0]
        );
        return v > 4 ? v : false ;
    }());
     
    /*
    * @link :http://browserhacks.com/
    * chrome篇
    */
    var isChromium = !!window.chrome;  //Chromium为true.
    var isChrome = !!window.chrome && !!window.chrome.webstore; //chrome为true.
    /*
    * @link :http://browserhacks.com/
    * Firefox篇
    */
    var isFF = !!window.sidebar;


11、滚动文字或图片效果--感觉写的挺简单的收藏了

    //html 结构
    <div class="tabBox">
        <ul class="visiter">
            <li><span class="firstSpan">第一周幸运用户</span><span>18601583176</span><span>巴西世界杯之旅6日游</span></li>
            <li><span class="firstSpan">第二周幸运用户</span><span>18601583176</span><span>巴西世界杯之旅6日游</span></li>
            <li><span class="firstSpan">第三周幸运用户</span><span>18601583176</span><span>巴西世界杯之旅6日游</span></li>
            <li><span class="firstSpan">第四周幸运用户</span><span>18601583176</span><span>巴西世界杯之旅6日游</span></li>
            <li><span class="firstSpan">第五周幸运用户</span><span>18601583176</span><span>巴西世界杯之旅6日游</span></li>
            <li><span class="firstSpan">第六周幸运用户</span><span>18601583176</span><span>巴西世界杯之旅6日游</span></li>
            <li><span class="firstSpan">第七周幸运用户</span><span>18601583176</span><span>巴西世界杯之旅6日游</span></li>
            <li><span class="firstSpan">第八周幸运用户</span><span>18601583176</span><span>巴西世界杯之旅6日游</span></li>
        </ul>
    </div>
     
    //js代码
    $(function(){
        var _listName = $('.visiter');
        var _geshu = _listName.find('li').length;
        var _runH  = 30;
        if(_geshu>4){
            setInterval(function(){ gundong(_listName,_runH);},2000);
        }
        function gundong(_listName,_runH){
            var first_html =  _listName.find('li').first().html();
            _listName.find('li').last().after('<li>'+first_html+'</li>');
            _listName.find('li').first().animate({'margin-top':-_runH},500,function(){
                $(this).remove();
            });
        }
     
    });


12、类似书目录的一种布局

    <!doctype html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>无标题文档</title>
    <style>
    *{margin:0;padding:0;}
    .clearfix:before, .clearfix:after {content:"";display:table;}
    .clearfix:after{clear:both;}
    .clearfix{*zoom:1;/*ie6,7*/}
    .list li{list-style-type:none;height:20px;line-height:20px;}
    .list li p{float:left;}
    .list li em{float:right;font-style:normal;padding:0 5px;}
    .list li span{border-bottom:1px dashed #000;height:10px;line-height:10px;display:block; overflow:hidden;}
    </style>
    </head>
     
    <body>
    <ul class="list">
        <li class="clearfix">
          <p>影视动漫设计</p>
          <em>知识+4</em>
          <span></span>
        </li>
         <li  class="clearfix">
          <p>科普</p>
          <em>知识+4</em>
           <span></span>
        </li>
         <li class="clearfix">
          <p>Dreamweaver</p>
          <em>知识+4</em>
           <span></span>
        </li>
         <li class="clearfix">
          <p>劳动法</p>
          <em>知识+4</em>
          <span></span>
        </li>
      </ul>
    </body>
    </html>


13、jquery cookie 封装

    (function ($) {
        $.xcookie = {
            cookie: function (name, value, options) {
                var path = options.path ? '; path=' + (options.path) : '',
                    domain = options.domain ? '; domain=' + (options.domain) : '',
                    secure = options.secure ? '; secure' : '',
                    expires = '';
                if (options.expires && (typeof options.expires == 'number' || options.expires.toUTCString)) {
                    var date;
                    if (typeof options.expires == 'number') {
                        date = new Date();
                        date.setTime(date.getTime() + (options.expires * 24 * 60 * 60 * 1000));
                    } else {
                        date = options.expires;
                    }
                    expires = '; expires=' + date.toUTCString();
                    
                }
                document.cookie = [name, '=', encodeURIComponent(value), expires, path, domain, secure].join('');
            },
            setCookie: function (name, value, options) {
                var options = options || {};
                this.cookie(name, value, options);
            },
            getCookie: function (name) {
                var cookieValue = null,
                    doc = document;
                if (doc.cookie && doc.cookie != '') {
                    var cookies = doc.cookie.split(';');
                    for (var i = 0; i < cookies.length; i++) {
                        var cookie = $.trim(cookies[i]);
                        if (cookie.substring(0, name.length + 1) == (name + '=')) {
                            cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                            break;
                        }
                    }
                }
                return cookieValue;
            },
            deleteCookie: function (name, options) {
                var options = options || {},
                    value = '';
                options.expires = -1;
                this.cookie(name, value, options);
            }
        }
    })(jQuery);
     
     
    //使用方法
    // 1、设置
    $.xcookie.setCookie('gemeOld', ‘cookie值’, { path: '/',expires:1000});//expires是天数
     
    //2、获取
    $.xcookie.getCookie('gemeOld')
     
    //3、删除
    $.xcookie.deleteCookie('gemeOld',{path : '/'})



14、微博腾讯空间分享接口

    /**  微博  **/
    var imgName = '1.jpg'; //开发，传入生成后的图片名称。 
    var title = '分享文字'; //定义分享文字内容，等待 
    var url = 'http://www.lvmama.com/zt/promo/ztname/'; //要分享的页面链接 
    var img = url+'imgnew/'+imgName; //分享的图片路径 
    var fxHref = 'http://service.weibo.com/share/share.php?title='+ title +'&url='+ url +'&source=bookmark&appkey=2992571369&pic='+ img +'&ralateUid=&sudaref=s.jiathis.com'; 
    window.open(fxHref);
    //window.location.href=fxHref
    /** QQ分享 项目中发现QQ分享必须在有ip地址的服务器上才有效果 **/
    var title = shareTxt; //定义分享，等待 
    var url = encodeURIComponent(shareUrl); //页面链接 
    var img = shareImgSrc; //分享的图片路径
    var fxHref = 'http://sns.qzone.qq.com/cgi-bin/qzshare/cgi_qzshare_onekey?url='+url+'&title='+title +'&desc=&summary=&site=&pics='+img
    window.open(fxHref); 


15、获取选中的文字

    //选择文字
    function selectText(){
        if(document.selection){  //IE
            return document.selection.createRange().text;
        }else{                   //标准
            return window.getSelection().toString();
        }
    }


16、jQuery 扩展 tween 算法

    $.extend(jQuery.easing , {
        
        easeIn: function(x,t, b, c, d){  //加速曲线
            return c*(t/=d)*t + b;
        },
        easeOut: function(x,t, b, c, d){  //减速曲线
            return -c *(t/=d)*(t-2) + b;
        },
        easeBoth: function(x,t, b, c, d){  //加速减速曲线
            if ((t/=d/2) < 1) {
                return c/2*t*t + b;
            }
            return -c/2 * ((--t)*(t-2) - 1) + b;
        },
        easeInStrong: function(x,t, b, c, d){  //加加速曲线
            return c*(t/=d)*t*t*t + b;
        },
        easeOutStrong: function(x,t, b, c, d){  //减减速曲线
            return -c * ((t=t/d-1)*t*t*t - 1) + b;
        },
        easeBothStrong: function(x,t, b, c, d){  //加加速减减速曲线
            if ((t/=d/2) < 1) {
                return c/2*t*t*t*t + b;
            }
            return -c/2 * ((t-=2)*t*t*t - 2) + b;
        },
        elasticIn: function(x,t, b, c, d, a, p){  //正弦衰减曲线（弹动渐入）
            if (t === 0) { 
                return b; 
            }
            if ( (t /= d) == 1 ) {
                return b+c; 
            }
            if (!p) {
                p=d*0.3; 
            }
            if (!a || a < Math.abs(c)) {
                a = c; 
                var s = p/4;
            } else {
                var s = p/(2*Math.PI) * Math.asin (c/a);
            }
            return -(a*Math.pow(2,10*(t-=1)) * Math.sin( (t*d-s)*(2*Math.PI)/p )) + b;
        },
        elasticOut: function(x,t, b, c, d, a, p){    //正弦增强曲线（弹动渐出）
            if (t === 0) {
                return b;
            }
            if ( (t /= d) == 1 ) {
                return b+c;
            }
            if (!p) {
                p=d*0.3;
            }
            if (!a || a < Math.abs(c)) {
                a = c;
                var s = p / 4;
            } else {
                var s = p/(2*Math.PI) * Math.asin (c/a);
            }
            return a*Math.pow(2,-10*t) * Math.sin( (t*d-s)*(2*Math.PI)/p ) + c + b;
        },    
        elasticBoth: function(x,t, b, c, d, a, p){
            if (t === 0) {
                return b;
            }
            if ( (t /= d/2) == 2 ) {
                return b+c;
            }
            if (!p) {
                p = d*(0.3*1.5);
            }
            if ( !a || a < Math.abs(c) ) {
                a = c; 
                var s = p/4;
            }
            else {
                var s = p/(2*Math.PI) * Math.asin (c/a);
            }
            if (t < 1) {
                return - 0.5*(a*Math.pow(2,10*(t-=1)) * 
                        Math.sin( (t*d-s)*(2*Math.PI)/p )) + b;
            }
            return a*Math.pow(2,-10*(t-=1)) * 
                    Math.sin( (t*d-s)*(2*Math.PI)/p )*0.5 + c + b;
        },
        backIn: function(x,t, b, c, d, s){     //回退加速（回退渐入）
            if (typeof s == 'undefined') {
               s = 1.70158;
            }
            return c*(t/=d)*t*((s+1)*t - s) + b;
        },
        backOut: function(x,t, b, c, d, s){
            if (typeof s == 'undefined') {
                s = 3.70158;  //回缩的距离
            }
            return c*((t=t/d-1)*t*((s+1)*t + s) + 1) + b;
        }, 
        backBoth: function(x,t, b, c, d, s){
            if (typeof s == 'undefined') {
                s = 1.70158; 
            }
            if ((t /= d/2 ) < 1) {
                return c/2*(t*t*(((s*=(1.525))+1)*t - s)) + b;
            }
            return c/2*((t-=2)*t*(((s*=(1.525))+1)*t + s) + 2) + b;
        },
        bounceIn: function(x,t, b, c, d){    //弹球减振（弹球渐出）
            return c - this['bounceOut'](x,d-t, 0, c, d) + b;
        },       
        bounceOut: function(x,t, b, c, d){
            if ((t/=d) < (1/2.75)) {
                return c*(7.5625*t*t) + b;
            } else if (t < (2/2.75)) {
                return c*(7.5625*(t-=(1.5/2.75))*t + 0.75) + b;
            } else if (t < (2.5/2.75)) {
                return c*(7.5625*(t-=(2.25/2.75))*t + 0.9375) + b;
            }
            return c*(7.5625*(t-=(2.625/2.75))*t + 0.984375) + b;
        },      
        bounceBoth: function(x,t, b, c, d){
            if (t < d/2) {
                return this['bounceIn'](x,t*2, 0, c, d) * 0.5 + b;
            }
            return this['bounceOut'](x,t*2-d, 0, c, d) * 0.5 + c*0.5 + b;
        }
        
    });


17、javascript原生扩展tween

    function startMove(obj,json,times,fx,fn){
        
        if( typeof times == 'undefined' ){
            times = 400;
            fx = 'linear';
        }
        
        if( typeof times == 'string' ){
            if(typeof fx == 'function'){
                fn = fx;
            }
            fx = times;
            times = 400;
        }
        else if(typeof times == 'function'){
            fn = times;
            times = 400;
            fx = 'linear';
        }
        else if(typeof times == 'number'){
            if(typeof fx == 'function'){
                fn = fx;
                fx = 'linear';
            }
            else if(typeof fx == 'undefined'){
                fx = 'linear';
            }
        }
        
        var iCur = {};
        
        for(var attr in json){
            iCur[attr] = 0;
            
            if( attr == 'opacity' ){
                iCur[attr] = Math.round(getStyle(obj,attr)*100);
            }
            else{
                iCur[attr] = parseInt(getStyle(obj,attr));
            }
            
        }
        
        var startTime = now();
        
        clearInterval(obj.timer);
        
        obj.timer = setInterval(function(){
            
            var changeTime = now();
            
            var t = times - Math.max(0,startTime - changeTime + times);  //0到2000
            
            for(var attr in json){
                
                var value = Tween[fx](t,iCur[attr],json[attr]-iCur[attr],times);
                
                if(attr == 'opacity'){
                    obj.style.opacity = value/100;
                    obj.style.filter = 'alpha(opacity='+value+')';
                }
                else{
                    obj.style[attr] = value + 'px';
                }
                
            }
            
            if(t == times){
                clearInterval(obj.timer);
                if(fn){
                    fn.call(obj);
                }
            }
            
        },13);
        
        function getStyle(obj,attr){
            if(obj.currentStyle){
                return obj.currentStyle[attr];
            }
            else{
                return getComputedStyle(obj,false)[attr];
            }
        }
        
        function now(){
            return (new Date()).getTime();
        }
        
    }
    var Tween = {
        linear: function (t, b, c, d){  //匀速
            return c*t/d + b;
        },
        easeIn: function(t, b, c, d){  //加速曲线
            return c*(t/=d)*t + b;
        },
        easeOut: function(t, b, c, d){  //减速曲线
            return -c *(t/=d)*(t-2) + b;
        },
        easeBoth: function(t, b, c, d){  //加速减速曲线
            if ((t/=d/2) < 1) {
                return c/2*t*t + b;
            }
            return -c/2 * ((--t)*(t-2) - 1) + b;
        },
        easeInStrong: function(t, b, c, d){  //加加速曲线
            return c*(t/=d)*t*t*t + b;
        },
        easeOutStrong: function(t, b, c, d){  //减减速曲线
            return -c * ((t=t/d-1)*t*t*t - 1) + b;
        },
        easeBothStrong: function(t, b, c, d){  //加加速减减速曲线
            if ((t/=d/2) < 1) {
                return c/2*t*t*t*t + b;
            }
            return -c/2 * ((t-=2)*t*t*t - 2) + b;
        },
        elasticIn: function(t, b, c, d, a, p){  //正弦衰减曲线（弹动渐入）
            if (t === 0) { 
                return b; 
            }
            if ( (t /= d) == 1 ) {
                return b+c; 
            }
            if (!p) {
                p=d*0.3; 
            }
            if (!a || a < Math.abs(c)) {
                a = c; 
                var s = p/4;
            } else {
                var s = p/(2*Math.PI) * Math.asin (c/a);
            }
            return -(a*Math.pow(2,10*(t-=1)) * Math.sin( (t*d-s)*(2*Math.PI)/p )) + b;
        },
        elasticOut: function(t, b, c, d, a, p){    //正弦增强曲线（弹动渐出）
            if (t === 0) {
                return b;
            }
            if ( (t /= d) == 1 ) {
                return b+c;
            }
            if (!p) {
                p=d*0.3;
            }
            if (!a || a < Math.abs(c)) {
                a = c;
                var s = p / 4;
            } else {
                var s = p/(2*Math.PI) * Math.asin (c/a);
            }
            return a*Math.pow(2,-10*t) * Math.sin( (t*d-s)*(2*Math.PI)/p ) + c + b;
        },    
        elasticBoth: function(t, b, c, d, a, p){
            if (t === 0) {
                return b;
            }
            if ( (t /= d/2) == 2 ) {
                return b+c;
            }
            if (!p) {
                p = d*(0.3*1.5);
            }
            if ( !a || a < Math.abs(c) ) {
                a = c; 
                var s = p/4;
            }
            else {
                var s = p/(2*Math.PI) * Math.asin (c/a);
            }
            if (t < 1) {
                return - 0.5*(a*Math.pow(2,10*(t-=1)) * 
                        Math.sin( (t*d-s)*(2*Math.PI)/p )) + b;
            }
            return a*Math.pow(2,-10*(t-=1)) * 
                    Math.sin( (t*d-s)*(2*Math.PI)/p )*0.5 + c + b;
        },
        backIn: function(t, b, c, d, s){     //回退加速（回退渐入）
            if (typeof s == 'undefined') {
               s = 1.70158;
            }
            return c*(t/=d)*t*((s+1)*t - s) + b;
        },
        backOut: function(t, b, c, d, s){
            if (typeof s == 'undefined') {
                s = 3.70158;  //回缩的距离
            }
            return c*((t=t/d-1)*t*((s+1)*t + s) + 1) + b;
        }, 
        backBoth: function(t, b, c, d, s){
            if (typeof s == 'undefined') {
                s = 1.70158; 
            }
            if ((t /= d/2 ) < 1) {
                return c/2*(t*t*(((s*=(1.525))+1)*t - s)) + b;
            }
            return c/2*((t-=2)*t*(((s*=(1.525))+1)*t + s) + 2) + b;
        },
        bounceIn: function(t, b, c, d){    //弹球减振（弹球渐出）
            return c - Tween['bounceOut'](d-t, 0, c, d) + b;
        },       
        bounceOut: function(t, b, c, d){
            if ((t/=d) < (1/2.75)) {
                return c*(7.5625*t*t) + b;
            } else if (t < (2/2.75)) {
                return c*(7.5625*(t-=(1.5/2.75))*t + 0.75) + b;
            } else if (t < (2.5/2.75)) {
                return c*(7.5625*(t-=(2.25/2.75))*t + 0.9375) + b;
            }
            return c*(7.5625*(t-=(2.625/2.75))*t + 0.984375) + b;
        },      
        bounceBoth: function(t, b, c, d){
            if (t < d/2) {
                return Tween['bounceIn'](t*2, 0, c, d) * 0.5 + b;
            }
            return Tween['bounceOut'](t*2-d, 0, c, d) * 0.5 + c*0.5 + b;
        }
    }
    //调用方式
    startMove(obj,json,times,fx,fn) //fx为tween参数形式

18、JS解析URL各部分通用方法

    function parseURL(url) {
        var a =  document.createElement('a');
        a.href = url;
        return {
            source: url,
            protocol: a.protocol.replace(':',''),
            host: a.hostname,
            port: a.port,
            query: a.search,
            params: (function(){
                var ret = {},
                    seg = a.search.replace(/^\?/,'').split('&'),
                    len = seg.length, i = 0, s;
                for (;i<len;i++) {
                    if (!seg[i]) { continue; }
                    s = seg[i].split('=');
                    ret[s[0]] = s[1];
                }
                return ret;
            })(),
            file: (a.pathname.match(/\/([^\/?#]+)$/i) || [,''])[1],
            hash: a.hash.replace('#',''),
            path: a.pathname.replace(/^([^\/])/,'/$1'),
            relative: (a.href.match(/tps?:\/\/[^\/]+(.+)/) || [,''])[1],
            segments: a.pathname.replace(/^\//,'').split('/')
        };
    }
    //调用示例
    var myURL = parseURL('http://abc.com:8080/dir/index.html?id=255&m=hello#top');
     
    myURL.file;     // = 'index.html'
    myURL.hash;     // = 'top'
    myURL.host;     // = 'abc.com'
    myURL.query;    // = '?id=255&m=hello'
    myURL.params;   // = Object = { id: 255, m: hello }
    myURL.path;     // = '/dir/index.html'
    myURL.segments; // = Array = ['dir', 'index.html']
    myURL.port;     // = '8080'
    myURL.protocol; // = 'http'
    myURL.source;   // = 'http://abc.com:8080/dir/index.html?id=255&m=hello#top'

15、将arguments转换成数组

    var argArray = Array.prototype.slice.call(arguments);

16、将一个数组附加到另外一个数组

    var array1 = [12 , "foo" , {name "Joe"} , -2458];
    var array2 = ["Doe" , 555 , 100];
    Array.prototype.push.apply(array1, array2);
    console.log(array1);
    /* array1 will be equal to  [12 , "foo" , {name "Joe"} , -2458 , "Doe" , 555 , 100] */


17、监听窗口关闭事件

    window.onbeforeunload = onbeforeunload_handler;     
    window.onunload = onunload_handler;     
    function onbeforeunload_handler(){     
        var warning="确认退出?";             
        return warning;     
    }     
         
    function onunload_handler(){     
        var warning="谢谢光临";     
        alert(warning);     
    }


18、将serialize序列化的字符串转为json对象

    //html
    <form id="myform" action="" method="post">
        姓名<input type="text" name="user" value=""><br>
        手机<input type="text" name="tel" value=""><br>
        城市<select name="address">
            <option value="北京">北京</option>
            <option value="上海">上海</option>
        </select><br>
        性别<input type="radio" name="sex" value="男">男<input type="radio" name="sex" value="女">女<br>
        爱好<input type="checkbox" name="enjoy" value="听歌">听歌<br><input type="checkbox" name="enjoy" value="电影">电影<br><input type="checkbox" name="enjoy" value="读书">读书<br>
    </form>
    //js
    var str = $('#myform').serialize();
    //将form表单的内容组装层 json 对象
    function serializeJson(serializeString){
        var arr = decodeURI(serializeString).split('&');
        var sendJson = {};
        for(var i=0; i<arr.length; i++){
            var key = arr[i].split('=')[0];
            var val = arr[i].split('=')[1];
            if(sendJson[key]){
                var tmp = sendJson[key];
                if(tmp instanceof Array){
                    sendJson[key].push(val);
                }else{
                    var innerArr = [];
                    innerArr.push(tmp);
                    innerArr.push(val);
                    sendJson[key] = innerArr;
                }
            }else{
                sendJson[key] = val;
            }
        }
        return sendJson;
    }







