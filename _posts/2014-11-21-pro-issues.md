---
layout: post
title:  "整理项目中遇到的一些问题"
date:   2014-11-21
description:  整理微游记项目中遇到的一些问题
categories:
- blog
permalink: pro-issues
---


####前段时间做了驴妈妈的微游记项目，整理一部分感觉比较坑的问题，希望以后再碰到类似的可以更好更快的解决
--------------------------------------------------------


####1、限制和截取文字问题

>如果想在文本框中输入文字，并随时统计输入的字数（这里两个英文字母算作一个字符，一个汉字算一个字符），而且还要注意通过鼠标复制粘贴操作的情况，所以只能通过监听 keyup 与input propertychange两个事件来完成,交互效果请点击[这里](http://www.baidu.com)。

    $('textarea,input').bind('input propertychange,keyup',function(){
        var len = getLength($(this).val());
        len = Math.ceil(len/2);
        if(len>500){
            $(this).val(sub($(this).val(),1000));// 两倍关系
            $(this).next().find('span').html(500)
        }else{
            $(this).next().find('span').html(len);
        }
    });
    //截取指定长度字符
    function sub(str,n){ 
        var r=/[^\x00-\xff]/g; 
        if(str.replace(r,"mm").length<=n){return str;} 
            var m=Math.floor(n/2); 
            for(var i=m;i<str.length;i++){ 
                if(str.substr(0,i).replace(r,"mm").length>=n){ 
                return str.substr(0,i); 
            } 
        } 
        return str; 
    }
    // 统计字符长度
    function getLength(str){
        return String(str).replace(/[^\x00-\xff]/g,'aa').length;
    }

####2、日历问题
>项目一开始使用了驴妈妈自己开发的日历控件（该组件挺好可去），可后来产品经理又希望能修改年份月份，产品经理嘛，程序员都懂的，就不说了，所以才用了 jQueryUI组件，但jQueryUI对动态生成的元素不能直接使用，原因是在页面中创建了一个，然后通过对其显示隐藏来操作的，故对动态生成的要想办法对其每一个都创建一个与之对应的日历，有点类似对象下属性的意思，另外，对应已选择的日期要禁用掉，还好jQueryUI有类似的接口，万幸。。交互效果参考[这里](http://www.baidu.com)


附上驴妈妈日历效果，有兴趣的可以到网站上去取，前辈们写的很好，功能已经比较全面了。
[![](http://coolnuanfeng.io/assets/images/calendar.png)]

    calendar(); 
    //定义一个封装函数，在每次创建时调用一下
    function calendar(){ 
        $('.J_calendar').datepicker({
          changeMonth: true,
          changeYear: true,
          numberOfMonths: 2,
          beforeShowDay: nationalDays //禁用指定日期参数
        });
    }
    
    $('.icon_calendarLittle').live('click',function(){
        console.log('aa')
        $(this).prev().focus()
    });
    
    //禁用某天,每次操作重新更新数组
    natDays = [  [11,23,2013], [11,24,2014],[11,14,2014]  ];  
      
    function nationalDays(date) {  
        for (i = 0; i < natDays.length; i++) {  
            if (date.getFullYear() == natDays[i][2] && date.getMonth() == natDays[i][0] - 1 && date.getDate() == natDays[i][1]) {  
                return [false, natDays[i][2] + '_day'];  
            }  
        }  
        return [true, ''];  
    }  

####3、关于动态添加一些HTML结构或元素问题
>underscorejs是一个比较不错的库，有研究过backbone的对此不会陌生，本人也只是知道些皮毛，对于项目中要动态生成一些相同结构的代码，可以使用其提供的方法。代码如下（改库依赖jquery，故别忘记引用）:
        
    //模板代码示例，注意 type,id
    <script type="text/template" id="addTemplate">
        <div class="box" id="boxID_<%= idNum %>">
            <h5>第<%= idNum %>天</h5>
            <p><%- text %></p>
        </div>
    </script>
    //调用方式
    var $div=$('<div></div>');
    var json = {
        idNum : 1,
        text : "测试文字"
    };
    $div.html(_.template($('#addTemplate').html(), json );
    //补充：<%=  %> 接受正常文本参数 <%-  %>可接受一些特殊字符文本


####4、上传照片页面与编辑页面的上传图片问题
> 这里专门研究了下plupload插件，这里不多说，有时间要单独写篇blog整理下。这个项目遇到主要的大坑，就是plupload的插件中的触发选择照片弹框的window窗口的元素的ID是固定的，项目中必须要不停的改变该ID，当时已经开发了一大半了，换插件是伤筋动骨啊，研究了一段时间发现原来是可以封装到方法了，通过传参来改变的，大喜。。
    
    //插件的一些基本使用参数配置可在官方API中查找，此处指简单介绍部分
    function myUpload(strID){
        var uploader = new plupload.Uploader({
            runtimes : 'html5,flash,silverlight,html4',
            browse_button : strID, 
            url : 'php/uploadImage.php',
            chunk_size : '10mb',
            //unique_names : true,
            filters : {
                max_file_size : '10mb',
                mime_types: [
                    {title : "Image files", extensions : "jpg,gif,png"}
                ]
            },
            //resize : { width : 138, height : 91, quality : 90 },
            flash_swf_url : 'statics/js/Moxie.swf',
            silverlight_xap_url : 'statics/js/Moxie.xap',
        }）；
        uploader.init(); //初始化插件

        //FilesAdded方法用于将上传的元素添加到页面中，是个集合 files
        uploader.bind('FilesAdded',function(up,files){
            uploader.start();  //插件开始执行
            plupload.each(files, function(file) {                   
                //此处单个文件操作，如创建节点并插入的页面中，展示          
            });
        }；
        //UploadProgress,上传进度
        uploader.bind('UploadProgress',function(up,file){
            //此处可进行上传过程的效果，如进度条
        });
        //FileUploaded,单个文件上传完成后执行
        uploader.bind('FileUploaded',function(up,file,res){
            //此处可发送Ajax，告诉服务器相关信息，并在Ajax成功时进行一些效果的制作
        });
        //Error，上传出错时执行
        uploader.bind('Error',function(up,file,res){
        })
    }


####5、拖拽问题
> 该项目最变态的部分，简直是各种拖啊，说多了全是泪啊，拖拽原理不复杂，就不介绍了，主要是里面的关联与排序等。主要还是通过一个个if，将各个情况分开执行，这里一时半会也描述不清，慢慢写，慢慢调就好了。拖拽两个元素排序问题有个小技巧，就是判断该元素在其父元素的索引值进行比较，来进行是前面插入还是后面插入。另外，由于页面可能存在大量的可拖拽元素，必然能产生性能问题，所以，程序里尽量减少遍历查找等，通过定义变量来提高性能，还有尽量使用事件委托，通过e.target来进行操作等。但本人觉得其实最影响性能的是图片太大的问，所以，跟后台开发人员想了个办法就是再后台生成两套图片，一套是用户真正上传的原图，一套就是压缩的，只返回后台压缩的图片src对其进行拖拽操作，提高性能。
>
> 上传过程中的图片预览效果目前只能用base64来处理，但会严重影响页面性能，最后将该功能取消了。

####6、左侧导航和描点定位

> 这个类似百度百科的效果（存在动态添加元素问题），没有找到相应的插件只好自己写，比较复杂，这里只介绍下原理和注意的地方:
> 
> -  右侧滚动时左侧div的scrollTop要滚动多少？
> 由于存在动态插入问题，故要封装个函数，再每次插入后执行下重新计算高度，即getSetHeight，将父节点元素div应该scrollTop的值计算出来存在对应的data-height属性中，当window的scroll事件发生时，直接取data-height的值就好了。
> - 左侧div上的滚动监听事件
> 这里用到了一个插件mousewheel.js，使用比较简单，该插件很好的解决了两个问题：a、就是下方的小按钮的点亮与禁用效果。b、阻止了浏览器的一些默认事件，如：在该div上滚动，右侧的也随之滚动
> - 左侧锚点与右侧关联定位问题
> 我是通过检测hash值的变换来做的，并且页面刷新时也能很好的定位上去。只需要让对应的元素的属性值（data-md="m1"）与 hash值一一对应就好。当然这个项目比较坑的地方时顶部滚动时的浮动定位跟随部分，由于高度不一致，会造成scrollTop的改变，所以要注意调整scrollTop的值。参考下面代码的描点定位部分。
> - 左侧描点 点击多次时，页面会出现抖动情况
> 这个问题困扰了我一会，找不到原因，知道是顶部的浮动滚动跟随高度变化导致导致scrollTop不精确，调了好半天，总算调好了，也没太搞懂到底怎么回事，但还是会闪一下，加了个 return false， 就好了，看来有些莫名奇妙的BUG，不妨尝试下 return false。O(∩_∩)O哈哈~


    //重新计算滚动高度 
    function getSetHeight(){ 
        var H = 0; 
        $('.treeBox dl').each(function(i){ 
            H+=$('.treeBox dl').eq(i).outerHeight()+50; 
            $(this).attr('data-height', H) 
        }); 
    }
    //锚点定位
    window.onhashchange = function(){
        var hash = window.location.hash.substring(1);
        $('.writeTitle span').each(function(){
            if( $(this).attr('data-md') == hash ){
                var T = $(this).offset().top - $('.tl_topNavBox').height();
                $('html,body').animate({
                    scrollTop : T
                },'fast')
            }
        });
    };
    //页面刷新或带有描点的链接时，可直接描点到该处
    $('.writeTitle span').each(function(){
        if( $(this).attr('data-md') == hash ){
            var T = $(this).offset().top - $('.tl_topNavBox').height();
            $('html,body').animate({
                scrollTop : T
            },'fast')
        }
    });

####7、查看页面的图片预览自适应效果
> 其实也很简单，从服务器那边把图片的宽度取到，前台切换的时候改变宽度就OK了。O(∩_∩)O哈哈~

####8、最后一个问题就是百度地图与谷歌地图部分
> 个人感觉地图嘛，国外谷歌，国内搜搜地图比较好，由于公司数据存的是百度的数据（经纬度），无奈只能选择百度地图。感觉百度地图的API写的不是太好。交互效果参考[这里](http://www.baidu.com)。产品经理设计的页面中存在一个页面有多个地图问题，这无疑增大了难度，产品经理嘛，还是那句话，程序员都懂的。。