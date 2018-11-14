---
layout: post
title:  "排序算法"
date:   2018-11-12
keywords: 程序中的几种排序
description:
categories:
- blog
permalink: sort-algorithm
---


#### 冒泡排序

    $arr = [4,2,6,20,13,16,18];
    for($i=0; $i<count($arr); $i++){
        $temp = '';
        for($j=0;$j<count($arr)-1-$i;$j++){
            if($arr[$j+1] > $arr[$j]){
                $temp = $arr[$j+1];
                $arr[$j+1] = $arr[$j];
                $arr[$j] = $temp;
            }
        }
    }
    echo '<pre>';
    print_r($arr);



### 插入排序


    $len=count($arr);
    for($i=1; $i<$len; $i++) {
        //获得当前需要比较的元素值。
        $tmp = $arr[$i];
        //内层循环控制 比较 并 插入
        for($j=$i-1; $j>=0; $j--) {
            //$arr[$i];//需要插入的元素; $arr[$j];//需要比较的元素
            if($tmp > $arr[$j]) {
                //发现插入的元素要小，交换位置
                //将后边的元素与前面的元素互换
                $arr[$j+1] = $arr[$j];
                //将前面的数设置为 当前需要交换的数
                $arr[$j] = $tmp;
            } else {
                //如果碰到不需要移动的元素
                //由于是已经排序好是数组，则前面的就不需要再次比较了。
                break;
            }
        }
    }
    echo '<pre>';
    print_r($arr);




### 二分法查找法


    function bin_search($arr,$key){
       $high = count($arr);
       if($high <= 0)
           return 0;
       $low = 0;
       while($low <= $high)
       {
           //当前查找区间arr[low..high]非空
             $mid=intval(($low + $high) / 2);
           if($arr[$mid] == $key)
               return $mid; //查找成功返回
           if($arr[$mid] > $key)
               $high = $mid - 1; //继续在arr[low..mid-1]中查找
           else
               $low = $mid + 1; //继续在arr[mid+1..high]中查找
       }
       return 0; //当low>high时表示查找区间为空，查找失败
    }
    $arr = [4,2,6,20,13,16,18];
    echo bin_search($arr, 8);
