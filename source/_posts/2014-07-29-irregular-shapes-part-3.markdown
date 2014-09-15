---
layout: post
title: "Android切图之不规则形状-3 [译]"
date: 2014-07-29 22:03:31 +0800
comments: true
categories: 
keywords: Android切图,不规则图形，Android图像合并,Android不规则图形,irregular shape
---

  在之前不规则图形切图系列中，我们采用了两种方法来实现图片的圆角。在这篇文章中我们将会创建像WhatsApp中消息气泡一样的效果。

  为了实现这个效果，我们需要使用上文中提到的基于BitmapShader的技术。圆角矩阵跟之前实现方法一样，但是需要在图片左侧留出一定的距离。在本文的代码中将画一个三角形来代替WhatsApp的消息气泡的“句柄”，这根就像漫画中指向人嘴的消息。

<!--more-->
  我们知道如何画圆角矩形，这就实现了我们效果的一半了。所以，我们需要做的就是画一个三角形。问题来了，在Canvas中没有画三角形的方法，所以我们需要用一些原始的方法来画三角形。Canvas支持moveTo()和lineTo()方法，通过这两个方法我们可以实现由直线组成的简单形状。但是我们有不能填充三角形内部，所以我们又需要使用Path对象。Path也包含moveTo()和lineTo()方法。Path不仅能够画简单的图形，而且还能够用BitmapShader来渲染图形内部。

  为了画一个三角形我们首先需要创建Path对象：

```
Path triangle = new Path();
```

  然后我们使用moveTo()来吧起点移动到开始位置。在我们的需求中，我们需要把起点移动到Canvas左边缘并向下一点点。这个点是气泡消息的三角形的最外侧的一个一个点：

```
triangle.moveTo(0, TRIANGLE_OFFSET);
```

  然后我们需要使用lineTo()方法画一条线。这条线起点是我们用moveTo()方法设定的点，终点是圆角矩形的边界。这条线需要向上倾斜：

```
triangle.lineTo(TRIANGLE_WIDTH, TRIANGLE_OFFSET - (TRIANGLE_HEIGHT / 2));
```

  再然后，我们需要用lineTo()方法画第二条线。这次我们希望最后的一条线是向下倾斜的，所以我们需要把终点选择在起点（moveTo()确定的点）一下：

```
triangle.lineTo(TRIANGLE_WIDTH, TRIANGLE_OFFSET + (TRIANGLE_HEIGHT / 2));
```

  最后，我们画最后一条线来完成整个三角形。一般来说，我们会选择从lineTo()最终的点直接画线到moveTo()的点。但是，有更简单的方法：close()。这个方法可以帮助我们自动完成多边形最后一遍的绘制。代码如下：

```
triangle.close();
```

  现在我们的三角形也画好了，我们剩下的唯一需要做的就是用Shader把三角形画到Canvas上。完整方法如下：

```
private static final float RADIUS_FACTOR = 8.0f;
private static final int TRIANGLE_WIDTH = 120;
private static final int TRIANGLE_HEIGHT = 100;
private static final int TRIANGLE_OFFSET = 300;

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

    RectF rect = new RectF(TRIANGLE_WIDTH, 0, 
        bitmap.getWidth(), bitmap.getHeight());
    canvas.drawRoundRect(rect, radius, radius, paint);

    Path triangle = new Path();
    triangle.moveTo(0, TRIANGLE_OFFSET);
    triangle.lineTo(TRIANGLE_WIDTH, 
        TRIANGLE_OFFSET - (TRIANGLE_HEIGHT / 2));
    triangle.lineTo(TRIANGLE_WIDTH, 
        TRIANGLE_OFFSET + (TRIANGLE_HEIGHT / 2));
    triangle.close();
    canvas.drawPath(triangle, paint);

    return bmp;
}
```

  运行后效果如下图。三角形和圆角矩形组合通过Shader实现了消息气泡的效果。

![part3](/imgs/post/part3.jpg)

  在Android切图之不规则图形这个系列里面，我们下篇文章将通过其他方法实现一些复杂点的图形（其实如果我们把它拆解后会发现其实也很简答的哦）。

  切图之不规则图形系列[源码下载](/download/StylingAndroid-irregular-shapes.zip)，英文原版[地址](http://blog.stylingandroid.com/irregular-shapes-part-3/)。