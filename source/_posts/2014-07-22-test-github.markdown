---
layout: post
title: "快速开发小技巧:如何通过一个命令行将apk从多台设备安装或卸载"
date: 2014-07-22 23:20:24 +0800
comments: true
categories: 
---


  最近，开发手机应用的时候发现从设备上安装和卸载应用花费我太多的时间。如果是多台设备的话就更能体会到到时间的消耗了。本文我将解决如何用一条命令行管理多台设备。

  首先我介绍一个拥有多个并排开关按钮和USB接口的设备（你可以从[Amazon](http://www.amazon.com/gp/product/B007S642BW/ref=oh_details_o08_s00_i00?ie=UTF8&psc=1)
买到）。我选择这个设备是因为它可以通过开关按钮方便的添加和移除设备以及它价格便宜。
<!--more-->

![device](/imgs/android_usb_bus.jpg)

  将这个设备插到电脑上，然后把Android设备插到此线板上。然后我们就可以连接电脑通过命令行管理手机了。安装APK到Android设备我们需要在命令行输入以下命令：

```
adb install -r path/to/the/app/to/install.apk
```    

  此命令在只有一台设备的时候能能够正常运行（adb就是为一台设备设计的），但是如果有多台设备的话，就会出现下面的错误：

```
- waiting for device -
 
error: more than one device and emulator
 
- waiting for device -
 
error: more than one device and emulator
 
- waiting for device -
 
Failure
```

  我们需要指定命令对哪台设备有效。为了获取设备ID我们需要输入如下命令：

```
adb devices
```

  此命令将返回如下结果：

```
List of devices attached
 
04544fawebf0b595dc device
 
13928weff7c device
```

  如果我们要安装APK到一个设备，我们需要多次执行下面的命令.

```
adb -s (deviceID) install -r path/to/the/app/to/install.apk
```

  如果我要安装APK到所有的设备，以前我是这样做的：依次复制Device ID，在命令行依次输入上面的命令并把Device ID粘贴进去。虽然有了上面的集线器，
但是这样还是不够效率。通过在网上搜索，最终在[Stackoverflow](http://stackoverflow.com/questions/17882474/running-adb-commands-on-all-connected-devices/17882578#17882578)上找到一个很好解决这个问题的脚本。

#####安装步骤：#####

  首先，新建文件名为adb+的文件，复制到下面脚本到adb+。

```
#!/bin/bash
# Script adb+
# Usage
# You can run any command adb provides on all your currently connected devices
# ./adb+ <command> is the equivalent of ./adb -s <serial number> <command>
#
# Examples
# ./adb+ version
# ./adb+ install apidemo.apk
# ./adb+ uninstall com.example.android.apis
adb devices | while read line
do
  if [ ! "$line" = "" ] && [ `echo $line | awk '{print $2}'` = "device" ]
  then
      device=`echo $line | awk '{print $1}'`
      echo "adb -s $device $@ ..."
      adb -s $device $@
  fi
done
```

  然后将此文件保存到你的Android SDK安装目录中和adb命令同文件夹的文件中（platform-tools文件夹）。

#####使用方法：#####

  此脚本使用非常方便。在保证你的所有的设备连接到电脑的情况下，直接将adb命令替换成adb+命令就OK了（简单吧）。例如，安装APK到设备命令如下：

```
adb+ install -r path/to/the/app/to/install.apk
```

  此命令将返回如下：

```
04544fabs23f0b595dc install -r /path/to/your/app/name_of_the_app.apk ...

4619 KB/s (13249277 bytes in 2.800s)

    pkg: /data/local/tmp/name_of_the_app.apk

Success

1399834vskj28f7c install -r/path/to/your/app/name_of_the_app.apk ...

3608 KB/s (13249277 bytes in 3.585s)

    pkg: /data/local/tmp/name_of_the_app.apk

Success
```

#####可能遇到的问题：#####

  如果运行adb+遇到如下提示信息：

```
-bash: adb+: Permission denied
```

  这个问题是因为你的命令没有可执行的权限。解决办法很简单，在adb+所在的目录执行如下命令:

```
chmod 755 ./adb+
```

  现在再尝试一次，你应该能够成功了。

  就是如此简单，新的命令将遍历所有的设备执行相同的命令。APK可以用很少的时间就安装到所有连接的设备上。你觉得如何呢？这个是否能够帮助你管理多台设备上的应用？如果有问题请留言。
	