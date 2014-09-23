---
layout: post
title: "Android应用程序架构"
date: 2014-09-12 23:06:53 +0800
comments: true
categories: 
keywords: Android应用程序架构
---

  前一段时间一直在研究架构方面的知识，加上公司程序的架构也有一些问题，所以我决定找个合适的时间来写一下Android应用程序架构的文章。

  此文旨在描述我的一些架构上观点，以及之前几个月我调查研究的一些架构相关知识。

<!--more-->

前言
----
  <font color="green"><Strong>我们知道想要写高质量的软件是非常艰难和复杂的：</Strong></font>不只是满足需求，还需要代码健壮、易维护、可测试以及足够灵活来满足需求增加和改变。上述几点特征正是任何软件需要遵循的方法。

  实现好的Android应用程序架构代表以下一组生产系统的实践：
 * 独立的架构
 * 可以测试的
 * 独立的UI
 * 独立的数据库
 * 独立其他外部组件

![clean_architecture1](/imgs/post/clean_architecture1.png)

  上图中可以看到架构有此层，但是实际中不一定是4层。此图只是一个纲要。在开发中我们需要依据<font color="green"><Strong>依赖规则：</Strong></font>只可以外层依赖内层，外层对内层来说是透明的。

  为了更好的理解上图含义，下面对上图中的术语作一个简单的阐述：
 * Entities:程序的业务实体
 * Use Cases:用例统筹业务实体中数据。
 * Interface Adapters:适配器接口把来自用例（Use Cases）和实体（Entities）的数据转换为UI（Frameworks and Drivers）需要的数据。
 * Frameworks and Drivers:所有的细节：UI、工具、框架等。


  通过[这篇文章](http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html)或[视频](http://vimeo.com/43612849)了解更多解释。

我的方案
-------

  我以一个简单的例子开始：创建一个简单的应用，这个应用显示一个来自网络的用户列表，当点击任何一个用户，跳转到此用户的详情页面。

Android架构
-----------

  通过保持业务逻辑不需要了解外部世界来实现关系的分离，因此，可以不依赖外部元素进行测试。

  为了实现关系分离，<font color="green"><Strong>我建议分三层架构，</Strong></font>每层与其他层分离，每层有自己的职责。值得注意的是，每层使用自己的数据模型来保证每层的独立性。如果不在引用中使用一套数据模型，付出的代价将是需要使用数据映射来完成数据变换。关系见下图：

![clean_architecture_android](/imgs/post/clean_architecture_android.png)

PS: 我没有采用任何第三方的库(除了JSON数据解析库GSON和测试库Junit、Mockito、Robolectric和Espresso)，这样保证了Demo尽量简单。我们平日比较常用能让我们开发更简单的orm框架、依赖注入框架以及其他的工具或三方库都没有使用。<font color="green"><Strong>“重复发明轮子是非常不建议的”</Strong></font>
  
###表现层

  这一层主要是控件逻辑相关和动画展示。这层实现不仅限于<font color="green"><Strong>MVP</Strong></font>，还包括其他的模式，比如MVC或者MVVM。这我不再赘述。Fragments和activities只是用来显示和UI逻辑以及渲染，没有任何逻辑操作。

###领域层


###数据层

未完待续 ...

  原文地址：[Architecting Android…The clean way?](http://fernandocejas.com/2014/09/03/architecting-android-the-clean-way/)




