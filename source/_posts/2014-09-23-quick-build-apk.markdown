---
layout: post
title: "Android快速打包"
date: 2014-09-23 21:06:33 +0800
comments: true
categories: 
keywords: Android快速打包,快速打包,Android,打包多渠道,快速打包多渠道APK,打包APK
---

  当开发应用需要推广到多个渠道，而且针对不同的渠道需要打不通的APK。传统的打包方式，打包速度不能满足用较少时间打包多个APK。如果应用代码量较小编译速度还尚可勉强接受，倘若应用比较大，那打包就会浪费很多时间。此博文主要通过减少编译过程和简化打包过程，来有效减少总的打包时间。

<!--more-->

  Android快速打包方法需要用到Google的APK编译工具[APKTool](http://baike.baidu.com/view/9152472.htm?fr=aladdin#ref_[1]_9160842)。想要快速打包还有一个很重要的前提就是：<b>不同渠道的应用基于配置实现</b>，即目前只能修改配置文件中资源，无法修改以编译好的代码。

## 快速打包步骤：
 * 1.decode.  例如：apktool d cloudLauncher_121101_1.2.8.apk cache
 * 2.修改原始包相应的manifest.xml文件 config.xml
 * 3.build.  例如：apktool b cache
 * 4.签名.   例如：jarsigner -verbose -keystore ezhuo_releasekey.releasekey -signedjar e-Zhuo_25100_v1.2.6.apk ./cache/dist/e-Zhuo_25000_v1.2.6.apk ezhuo

  APKTool可以到Google[官网下载](https://code.google.com/p/android-apktool/)或APKTool1.5.2 [本地下载](/download/apktool1.5.2.rar)
	
  如有任何问题可以留言或者<a href="mailto:jingle1267@163.com">邮件联系我</a>，多谢。
