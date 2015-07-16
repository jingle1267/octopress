---
layout: post
title: "Android批量打包极速版"
date: 2015-07-16 16:08:08 +0800
comments: true
categories: Android
keywords: 批量打包, 快速打包, phthon快速批量打包
---

  本文章介绍利用在META-INF目录内添加空文件的方式，实现批量快速打包Android应用。

<!--more-->

## 实现原理 ##

  Android应用安装包apk文件其实是一个压缩文件，可以将后缀修改为zip直接解压。解压安装文件后会发现在根目录有一个META-INF目录。如果在META-INF目录内添加空文件，可以不用重新签名应用。因此，通过为不同渠道的应用添加不同的空文件，可以唯一标识一个渠道。

  “采用这种方式，每打一个渠道包只需复制一个apk，在META－INF中添加一个使用渠道号命名的空文件即可。这种打包方式速度非常快，900多个渠道不到一分钟就能打完。”

## 实现步骤 ##

```
1. 应用中添加获取应用渠道号逻辑
2. IDE打包第一个应用
3. 整理渠道号
4. 配置打包脚本
5. 运行脚本打包
```

##### 1) 应用中添加获取应用渠道号逻辑 #####

  获取应用号采用<code>ChannelUtil.java</code>工具类代替就好了，实现逻辑代码片段

```java
package com.czt.util;

import java.io.IOException;
import java.util.Enumeration;
import java.util.zip.ZipEntry;
import java.util.zip.ZipFile;

import android.content.Context;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.content.pm.ApplicationInfo;
import android.content.pm.PackageManager.NameNotFoundException;
import android.preference.PreferenceManager;
import android.text.TextUtils;

public class ChannelUtil {
	
	private static final String CHANNEL_KEY = "cztchannel";
	private static final String CHANNEL_VERSION_KEY = "cztchannel_version";
	private static String mChannel;
	/**
	 * 返回市场。  如果获取失败返回""
	 * @param context
	 * @return
	 */
	public static String getChannel(Context context){
		return getChannel(context, "");
	}
	/**
	 * 返回市场。  如果获取失败返回defaultChannel
	 * @param context
	 * @param defaultChannel
	 * @return
	 */
	public static String getChannel(Context context, String defaultChannel) {
		//内存中获取
		if(!TextUtils.isEmpty(mChannel)){
			return mChannel;
		}
		//sp中获取
		mChannel = getChannelBySharedPreferences(context);
		if(!TextUtils.isEmpty(mChannel)){
			return mChannel;
		}
		//从apk中获取
		mChannel = getChannelFromApk(context, CHANNEL_KEY);
		if(!TextUtils.isEmpty(mChannel)){
			//保存sp中备用
			saveChannelBySharedPreferences(context, mChannel);
			return mChannel;
		}
		//全部获取失败
		return defaultChannel;
    }
	/**
	 * 从apk中获取版本信息
	 * @param context
	 * @param channelKey
	 * @return
	 */
	private static String getChannelFromApk(Context context, String channelKey) {
		//从apk包中获取
        ApplicationInfo appinfo = context.getApplicationInfo();
        String sourceDir = appinfo.sourceDir;
        //默认放在meta-inf/里， 所以需要再拼接一下
        String key = "META-INF/" + channelKey;
        String ret = "";
        ZipFile zipfile = null;
        try {
            zipfile = new ZipFile(sourceDir);
            Enumeration<?> entries = zipfile.entries();
            while (entries.hasMoreElements()) {
                ZipEntry entry = ((ZipEntry) entries.nextElement());
                String entryName = entry.getName();
                if (entryName.startsWith(key)) {
                    ret = entryName;
                    break;
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (zipfile != null) {
                try {
                    zipfile.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        String[] split = ret.split("_");
        String channel = "";
        if (split != null && split.length >= 2) {
        	channel = ret.substring(split[0].length() + 1);
        }
        return channel;
	}
	/**
	 * 本地保存channel & 对应版本号
	 * @param context
	 * @param channel
	 */
	private static void saveChannelBySharedPreferences(Context context, String channel){
		SharedPreferences sp = PreferenceManager.getDefaultSharedPreferences(context);
		Editor editor = sp.edit();
		editor.putString(CHANNEL_KEY, channel);
		editor.putInt(CHANNEL_VERSION_KEY, getVersionCode(context));
		editor.commit();
	}
	/**
	 * 从sp中获取channel
	 * @param context
	 * @return 为空表示获取异常、sp中的值已经失效、sp中没有此值
	 */
	private static String getChannelBySharedPreferences(Context context){
		SharedPreferences sp = PreferenceManager.getDefaultSharedPreferences(context);
		int currentVersionCode = getVersionCode(context);
		if(currentVersionCode == -1){
			//获取错误
			return "";
		}
		int versionCodeSaved = sp.getInt(CHANNEL_VERSION_KEY, -1);
		if(versionCodeSaved == -1){
			//本地没有存储的channel对应的版本号
			//第一次使用  或者 原先存储版本号异常
			return "";
		}
		if(currentVersionCode != versionCodeSaved){
			return "";
		}
		return sp.getString(CHANNEL_KEY, "");
	}
	/**
	 * 从包信息中获取版本号
	 * @param context
	 * @return
	 */
	private static int getVersionCode(Context context){
		try{
			return context.getPackageManager().getPackageInfo(context.getPackageName(), 0).versionCode;
		}catch(NameNotFoundException e) {
			e.printStackTrace();
		}
		return -1;
	}
}
```

##### 2) IDE打包第一个应用 #####

  这个没有什么好说的，按照之前打包方式，打包一个签名的应用包。

##### 3) 整理渠道号 #####

  编辑一个文本文件，添加渠道号，多个渠道之间用换行隔开。

##### 4) 配置打包脚本 #####

  Python脚本<code>MultiChannelBuildTool.py</code>实现批量打包代码如下：

```java
# coding=utf-8
import zipfile
import shutil
import os

# 空文件 便于写入此空文件到apk包中作为channel文件
src_empty_file = 'info/empty.txt'
# 创建一个空文件（不存在则创建）
f = open(src_empty_file, 'w') 
f.close()

# 获取当前目录中所有的apk源包
src_apks = []
# python3 : os.listdir()即可，这里使用兼容Python2的os.listdir('.')
for file in os.listdir('.'):
    if os.path.isfile(file):
        extension = os.path.splitext(file)[1][1:]
        if extension in 'apk':
            src_apks.append(file)

# 获取渠道列表
channel_file = 'info/channel.txt'
f = open(channel_file)
lines = f.readlines()
f.close()

for src_apk in src_apks:
    # file name (with extension)
    src_apk_file_name = os.path.basename(src_apk)
    # 分割文件名与后缀
    temp_list = os.path.splitext(src_apk_file_name)
    # name without extension
    src_apk_name = temp_list[0]
    # 后缀名，包含.   例如: ".apk "
    src_apk_extension = temp_list[1]
    
    # 创建生成目录,与文件名相关
    output_dir = 'output_' + src_apk_name + '/'
    # 目录不存在则创建
    if not os.path.exists(output_dir):
        os.mkdir(output_dir)
        
    # 遍历渠道号并创建对应渠道号的apk文件
    for line in lines:
        # 获取当前渠道号，因为从渠道文件中获得带有\n,所有strip一下
        target_channel = line.strip()
        # 拼接对应渠道号的apk
        target_apk = output_dir + src_apk_name + "_" + target_channel + src_apk_extension  
        # 拷贝建立新apk
        shutil.copy(src_apk,  target_apk)
        # zip获取新建立的apk文件
        zipped = zipfile.ZipFile(target_apk, 'a', zipfile.ZIP_DEFLATED)
        # 初始化渠道信息
        empty_channel_file = "META-INF/paipai_channel*{channel}".format(channel = target_channel)
        # 写入渠道信息
        zipped.write(src_empty_file, empty_channel_file)
        # 关闭zip流
        zipped.close()

```

将上述脚本文件中渠道文件修改为第三部中整理的渠道文件

```python
# 获取渠道列表
channel_file = 'info/channel.txt'
```

拷贝第二步中打包好的应用到当前脚本所在目录

##### 5) 运行脚本打包 #####

  命令行模式进入当前目录输入

```java
python MultiChannelBuildTool.py  
```

大功告成，在当前目录下新生成了一个文件夹，改文件夹下就是我们各个渠道包了。

PS: apk用的是java那一套签名，放在META-INF文件夹里的文件原则上是不参与签名的。如果Google修改了apk的签名规则，这一套可能就不适用了。

源码地址：[https://github.com/jingle1267/AndroidMultiChannelBuildTool](https://github.com/jingle1267/AndroidMultiChannelBuildTool)

## 参考地址 ##

美团Android自动化之旅：[http://tech.meituan.com/mt-apk-packaging.html](http://tech.meituan.com/mt-apk-packaging.html)  
Android批量打包提速：[http://www.cnblogs.com/ct2011/p/4152323.html](http://www.cnblogs.com/ct2011/p/4152323.html)


