---
layout: post
title: "使用octopress发布文章"
date: 2014-09-23 22:22:07 +0800
comments: true
categories: 
keywords: octopress发布文章,octopress post article,octopress,发布文章
---

  自从使用octopress来发布博文之后，感觉不是一般的爽。 但是经常忘记发布流程和指令，故在此写下一文，以备忘记之后还能有据可看。

<!--more-->

##发布文章步骤如下：

 * 1.创建文章文件
 * 2.写博文
 * 3.生成静态博文
 * 4.预览博文
 * 5.发布博文
 * 6.提交源码

### 1.创建文章文件
  
  要发布博文，首先需要使用下面指令创建博文MarkDown文件。

```
rake new_post['my topic']
```

  注意，rake new_post['my first octopress blog'] 中的 my first octopress blog 并不是博客标题，而是和生成的文件名以及url地址有关，该名称不支持中文。博客标题可以在生成的markdown文件中修改。生成的markdown文件在octopress/source/_posts目录中。

### 2.写博文

  找对生成的markdown文件，编辑内容，当然是使用markdown语法来编辑，将标题可以修改为中文标题，还可以设置分类等信息以及编写正文部分。

### 3.生成静态博文

  每次执行了添加博客的命令，或是修改了现有博客的内容后，都要执行下面命令进行重新生成。

```
rake generate
```

### 4.预览博文
  
  博文写好，想要预览，那马就需要下面这条指令了。

```
rake preview
```
  输入了上面这条指令，然后就可以直接访问[http://localhost:4000](http://localhost:4000)来进行预览。

### 5.发布博文

  想要把写好的博文同步到Github服务器上，那么就需要下面这条指令了。

```
rake deploy
```

  执行成功之后，就可以访问 <username>.github.io 查看线上效果了。

### 6.提交源码

  最后需要将Octopress的源文件推送到Github的Source分支上，执行下面命令即可：

```
git add .
git commit -m “your message”
git push origin source
```

  在安装的过程中可能会碰到各种问题，根据错误提示信息google，肯定会找到答案。