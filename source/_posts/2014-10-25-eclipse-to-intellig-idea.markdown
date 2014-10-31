---
layout: post
title: "Eclipse转移到IntelliJ IDEA"
date: 2014-10-25 17:19:14 +0800
comments: true
categories: 
keywords: Eclipse to intellij idea, 从Eclipse转移到IntelliJ IDEA心得
---


  本人使用IntelliJ IDEA其实并不太久，用了这段时间以后，觉得的确很是好用。刚刚从Eclipse转过来的很多人开始可能不适应，我就把使用过程中的一些经验和常用功能分享下。
 
<!--more-->
 
### 显示行号

  在Eclipse中设置显示行号很简单，转移到Intellij Idea也不难。只需要在IDE Settings中设置，具体设置如下图:

![show_line_numbers](/imgs/post/show_line_numbers.png)

### 文件和导航关联

  我们在写代码的时候经常需要打开某个文件的时候，想在左边的文件树上自动打开关联到位置，这样很方便的找寻本目录下的其他文件或者其他的相关文件，其实这个IntelliJ提供了好几种解决方案，第一个就是和Eclipse一样的方式，如下:

![source_autoscroll](/imgs/post/source_autoscroll.png)

  在上图的浮层中可以看到两个选项AutoScroll to Source和AutoScroll from source，IntelliJ很人性化的分了两个设置在里面，你两个都勾选就是基本和Eclipse的功能保持了完全一致，只是勾选一个自然也可以.

  还有一种方式就是利用IntelliJ提供的Navigation Bar，IntelliJ很多功能操作都可以键盘化，这个就是喜欢键盘化操作的人的必备功能，操作方式就是打开文件的时候，使用快捷键⌘+UP (Jump to Navigation Bar)，然后就会出现一个下拉，基本就是这个文件所在目录的文件列表，还可以使用上下左右的键位来选择其他目录，是不是很酷？

  备注:此处建议把View > Navigation Bar的选项点掉，这样就使用快捷键之后就会在你鼠标的位置出现，感觉会更输入一些，当然这样你就基本失去了使用鼠标的权利，这个需要你根据个人癖好使用即可。

### 版本控制设置

  大家以前Eclipse的时候在本地文件和版本库不一致的时候，那么文件以及所在的文件夹都会出现一个〉表示，大家能很轻松的看到本地文件修改了哪一些，但是IntelliJ中默认是不能这样的。仅仅是给变化的文件在修改的时候提供了颜色上的变化，不包括其所在的各个父级文件夹哦，如果想和Eclipse一样的话需要如下图把设置勾选就可以了：
  
![vcs_show_dir_status](/imgs/post/vcs_show_dir_status.png)

  其实看英文解释就可以明白了，这个默认开启以后如果想调整文件夹的显示颜色的时候就直接去Settings > Editor > Colors & Fonts > File Status中调整即可了，可以调整成自己喜欢的样子。
  
### Eclipse项目导入

  Intellij Idea导入Eclipse项目很简单，操作如下图：
 
 ![import_eclipse_web_project](/imgs/post/import_eclipse_web_project.png)

  先写着几点，还有很多功能来探索。总体感觉Intellij Idea比Eclipse更好用。
  






