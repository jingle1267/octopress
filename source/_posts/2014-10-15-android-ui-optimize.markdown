---
layout: post
title: "Android UI优化策略"
date: 2014-10-15 17:57:53 +0800
comments: true
categories: 
keywords: Android优化,ui优化,View优化,布局优化,View optimize,android view optimize
---

Android是一个运行在移动终端上的操作系统，跟传统PC最大的不同所在就是移动终端的资源紧缺问题“比较”明显，当然对于一些屌丝机型，应该用“非常“来形容才靠谱。

废话不多说了，Android的UI优化方法主要有以下几点：

<!--more-->

 * 1.使用尽量少的组件来实现布局功能。主要包括布局层级尽量不要超过10级和布局view数量尽量不要好过80。
 * 2.使用ViewStub暂缓加载比较复杂的布局
 * 3.使用include模块化代码，达到重用布局
 * 4.去除不必要的inflate
 * 5.去除不必要的findViewById
 
###1.使用尽量少的组件来实现布局功能###

 * 单个页面布局层数尽量不超过10级
 * 单个页面不居中View数不超过80个
 * 使用merge标签来减少不必要的根节点 
 
###2.暂缓加载比较复杂的布局###

  使用ViewStub暂缓加载比较复杂的布局

###3.模块化代码,重用布局###

  使用include标签模块化代码，达到重用布局

###4.去除不必要的inflate###

  对于inflate的布局可以直接缓存，用全局变量代替局部变量，避免下次需再次inflate

###5.去除不必要的findViewById###

  因为findViewById是比较耗时的操作，故也应该采用缓存，用全局变量代替局部变量，避免重复操作。

#####参考链接：#####

  [Android：一个高效的UI才是一个拉风的UI（一）](http://www.cnblogs.com/net168/archive/2014/10/09/4004950.html)<br>
  [Android：一个高效的UI才是一个拉风的UI（二）](http://www.cnblogs.com/net168/p/4017921.html)



