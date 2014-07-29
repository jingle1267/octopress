---
layout: post
title: "Android切图之不规则形状-2"
date: 2014-07-27 19:01:46 +0800
comments: true
categories: 
keywords: Android切图,不规则图形，Android图像合并,Android不规则图形,irregular shape
---

  上篇文章我们实现了用透明图片的方式来实现圆角切图。但是采用透明浮层的方法有很多问题不完全符合我们的要求。在这篇文章中我们采用一种不使用额外的图片的替代方案来实现相同的圆角图片的效果。

  在本文开始之前，我首先声明写着一些列的技术文章是我完全免费为大家提供的。我第一次学习用BitmapShader创建圆角图片是从Romain的一篇[博客文章](http://www.curious-creature.org/2012/12/11/android-recipe-1-image-with-rounded-corners/)。我们将会创建一些更酷的效果，下面先罗列一些用到的技术及说明：
<!--more-->
  
  ***Shader***是非常有用和给力的图像填充类。Paint对象通过使用Shader增加渲染效果，然后画图到Canvas。

  ***BitmapShader***除了具有Shader的特性外还支持对Bitmapde各种操作。本文中我们不再需要关心图片的大小问题了。

  为了跟上篇文章中的图像效果看起来相同，我们计算图片的圆弧半斤大概是图片最小边的1/8。

```
public Bitmap processImage(Bitmap bitmap) {
    Bitmap bmp;

    bmp = Bitmap.createBitmap(bitmap.getWidth(), 
        bitmap.getHeight(), Bitmap.Config.ARGB_8888);
    BitmapShader shader = new BitmapShader(bitmap, 
        BitmapShader.TileMode.CLAMP, 
        BitmapShader.TileMode.CLAMP);

    float radius = Math.min(bitmap.getWidth(), 
        bitmap.getHeight()) / RADIUS_FACTOR;
    Canvas canvas = new Canvas(bmp);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setShader(shader);

    RectF rect = new RectF(0, 0, 
        bitmap.getWidth(), bitmap.getHeight());
    canvas.drawRoundRect(rect, radius, radius, paint);

    return bmp;
}
```

  这也是一个很简单的例子。我们只需要用源图创建一个BitmapShader，然后添加给Paint对象，最后用Paint画圆角矩形。

  比较上一篇文章很容易就可以发现内存需求小多了。这里我们只需要两个Bitmap对象而在上篇博客中我们需要三个Bitmap对象。还有就是这种方法对图片的大小没有要求。采用浮层的方法我们需要对图片进行放大缩小。从产生的效果图片中我们可以发现，本文提供的方法比较之前的方法很难出现图片变模糊的家问题。

![part2](/imgs/post/part2.jpg)

  就像我之前提过的，这种方法除了实现圆角外还可以实现很多非常赞的效果。在下一篇文章中我们将要实现像WhatsApp中一样的聊天消息形状的效果。
  