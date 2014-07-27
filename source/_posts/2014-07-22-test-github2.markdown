---
layout: post
title: "Android切图之不规则形状-1"
date: 2014-07-22 23:25:50 +0800
comments: true
categories: 
keywords: Android切图,不规则图形，Android图像合并,Android不规则图形
---

  不久之前，我被问到如何创建一个像WhatsApp中气泡消息一样的，图像边缘是不规则图形的图片。更多的关于不规则图形方面的知识将在接下来的一系列的《Android切图之不规则形状》中学习到。

  在继续下一步之前，我必须承认我忘记是谁问的我关于切图的问题了。如果这个人是正在看此文的你，请联系我，我非常感谢你在一定程度上激励了我写这一系列的文章。
<!--more-->
  我们用一个很简单的不规则图形开始：我一个圆角矩形。值得注意的是，圆角矩形可以通过Android SDK的API20中的RoundRectShapeDrawable来实现，但是这个不能够实现我们将来需要实现的更为复杂的效果（这些之后的文章中会出现），所以我们将继续使用常规的方法。

  我们将要用到的第一个技术就是我们之前可能用过的透明浮层。

  浮层的方法是非常简单的。我们把两张图重叠起来：第一张是想要显示圆角的矩形图和第二张是是圆角矩形图。有了这两张图形我们只需要利用Porter Duff的透明规则让浮层透明就可以实现我们想要的效果。

  下面是我们需要准备的两张图片：

![betty](/imgs/post/betty_sm.jpg) ![mask](/imgs/post/mask_sm.png)

  第一张是我们切图系列文章的模特Betty。第二章是圆角矩形的浮层图像。这张绿色的浮层图像只是想让我们能够看到圆角矩形的形状（实际上是没有用的）。

  代码是很简单的：
 
```
public Bitmap combineImages(Bitmap bgd, Bitmap fg) {
   	Bitmap bmp;
   	int width = bgd.getWidth() > fg.getWidth() ? 
       	bgd.getWidth() : fg.getWidth();
   	int height = bgd.getHeight() > fg.getHeight() ? 
       	bgd.getHeight() : fg.getHeight();

   	bmp = Bitmap.createBitmap(width, height, Bitmap.Config.ARGB_8888);
   	Paint paint = new Paint();
   	paint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.SRC_ATOP));

   	Canvas canvas = new Canvas(bmp);
   	canvas.drawBitmap(bgd, 0, 0, null);
   	canvas.drawBitmap(fg, 0, 0, paint);

   	return bmp;
}
```

  我们创建的新的Bitmap是源图和浮层图的最大值。***我们需要先画浮层图像然后用设置了PorterDuffXFerMode.SRC_STOP的Paint对象来画Betty***。最后结果如下图：

![part1](/imgs/post/part1.jpg)

  OK,我们实现了预设的目标。但是现在的方法存在很多的问题。

  首先，我们需要两张相同大小的图片来合成新图。我们可能需要写一些代码来缩小放大图片，但是放大图片的时候会是图片产生模糊。我们也可能会遇到两张图片大小不同导致圆角矩形产生扭曲。

  另外一个大问题是这种方法效率不高。为了实现我们想要的效果，我们需要加载两张图片到内存，然后合并他们到第三张图片。如果图片非常大，当我们采用这种方式就很容易产生OutOfMemoryError。

  不过，值得庆幸的是，在接下来的文章中我会引入更高效的方法来实现我们想要的效果。

  源码稍后附上。



参考内容：
--------
- [PorterDuff及Xfermode详解](http://blog.csdn.net/t12x3456/article/details/10432935 "PorterDuff及Xfermode详解")

- [16条Porter-Duff规则简要说明](http://pcq019.blog.163.com/blog/static/124602323201232710853970/ "16条Porter-Duff规则简要说明  ")

