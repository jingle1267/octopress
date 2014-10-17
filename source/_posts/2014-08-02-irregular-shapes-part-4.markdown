---
layout: post
title: "Android切图之不规则形状-4 [译]"
date: 2014-08-02 13:01:50 +0800
comments: true
categories: 
keywords: Android切图,不规则图形，Android图像合并,Android不规则图形,irregular shape
---


  前面几篇文章我们实现了生成不规则图形，以及实现消息气泡效果。在最后这片文章中，我们将要实现心型图片。

  我知道软件工程师是非常浪漫的一群人，所以我想在情人节的时候发表这篇博客，但是最终决定现在发布，这样腾出来几天时间可以给你们增加一些更有趣的东西。

<!--more-->

  我们只需要很简单的几步画一个心形的图片。为了画心形的图片，我参考了[Heart Curve](http://www.mathematische-basteleien.de/heart.htm)。 Heart Curve提过了好几种画心形的方法。我最终选择符合我们要求的Heart Curve上提供的第四种方法。

  在开始画心形图案之前，我们需要设置我们的BitmapShader, Canvas和Paint对象：

``` java
Bitmap bmp;

bmp = Bitmap.createBitmap(bitmap.getWidth(),
    bitmap.getHeight(), Bitmap.Config.ARGB_8888);
BitmapShader shader = new BitmapShader(bitmap,
    BitmapShader.TileMode.CLAMP,
    BitmapShader.TileMode.CLAMP);

Canvas canvas = new Canvas(bmp);
Paint paint = new Paint();
paint.setAntiAlias(true);
paint.setShader(shader);
```

  因为下一步计算需要，我们需要把Bitmap的宽和高保存起来：

``` java
float width = bitmap.getWidth();
float height = bitmap.getHeight();
```

  为了画心形，我们还需要创建Path, Matrix和Regiond对象。我们也可以把在用的时候单独创建。

``` java
Path oval = new Path();
Matrix matrix = new Matrix();
Region region = new Region();
```

  有了前面的准备工作，接下来我们来画心形。首先我们需要做的是定义一个矩形用来画椭圆。矩形位于Bitmap的水平正中间位置，高度和Bitmap的高度相同，宽度是Bitmap的3/4：

``` java
RectF ovalRect = new RectF(width / 8, 0,
    width - (width / 8), height);
```

  接下来，我们在定义好的矩形中画椭圆：

``` java
oval.addOval(ovalRect, Path.Direction.CW);
```

  如果我们渲染了这个Path，我们将看到Betty出现在椭圆形状中：

![oval_betty](/imgs/post/oral_betty.jpg)
  下一步我们需要做的是将椭圆旋转30度。我们需要使用之前创建的Matrix对象来实现旋转。使用Matrix，我们可以将物体进行各种变换，还可以通过一些辅助方法实现一些基本的方法。在我们的例子中，我们需要以椭圆中心为轴旋转30度：

``` java
matrix.postRotate(30, width / 2, height / 2);
oval.transform(matrix, oval);
```

  旋转后效果如下：

![oral_rotate_betty](/imgs/post/oral_rotate_betty.jpg)

  值得注意的一点：我们不需要全部的椭圆，只需要右侧的一半就行了。所以，我们用之前创建的Region对象来裁剪椭圆。Region代表矩形区域。下面来截取右侧的部分：

``` java
region.setPath(oval, new Region((int)width / 2, 0, 
(int)width, (int)height));
canvas.drawPath(region.getBoundaryPath(), paint);
```

  截取之后的效果图如下:

![half_heart](/imgs/post/half_heart.jpg)

  接下来我们需要画另外一部分：

``` java
matrix.reset();
oval.reset();
```

  接下来重复之前的操作，画左侧的部分：

``` java
oval.addOval(ovalRect, Path.Direction.CW);
matrix.postRotate(-30, width / 2, height / 2);
oval.transform(matrix, oval);
region.setPath(oval, 
new Region(0, 0, (int)width / 2, (int)height));
canvas.drawPath(region.getBoundaryPath(), paint);
```

  最终结果如图：

![heart](/imgs/post/betty_heart.jpg)

  值得注意的是，使用Path对象和Region对象很容易实现图片裁剪。但是这些需要API 19。

  虽然我们本系列文章都是来讲解不规则图形切图的，但是我们用到的技术不仅仅局限于图片。我们也可以用这写技术来实现立体的，多彩的和渐变等等的效果。最后说一下，作为轻量级的图片裁剪技术，本系列文章算是比较节省系统开销。

  切图之不规则图形系列[源码下载](/download/StylingAndroid-irregular-shapes.zip)，英文原版[地址](http://blog.stylingandroid.com/irregular-shapes-part-4/)。

  







