---
layout: post
title: "装饰者模式"
date: 2014-11-20 21:53:20 +0800
comments: true
categories: 
keywords: Decorator Pattern,装饰者模式,Decorator
---


  在不必改变原类文件和使用继承的情况下，动态地扩展一个对象的功能。它是通过创建一个包装对象，也就是装饰来包裹真实的对象。对于不同功能之间的多种复杂的对象如果使用继承的方式，类的数量将会碰撞，而采用组合的方式，这样就可以在运行时实现不同的功能的组合。
装饰者模式可以动态的将职责附加到对象上。对于扩展功能的情况，提供了比继承更有弹性的解决方案。

<!--more-->

###设计原则###
1. 多用组合，少用继承。利用继承设计子类的行为，是在编译时静态决定的，而且所有的子类都会继承到相同的行为。然而，如果能够利用组合的做法扩展对象的行为，就可以在运行时动态地进行扩展。
2. 开闭原则。对扩展，对修改关闭。

###适用场景###
1. 在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责。
2. 需要动态地给一个对象增加功能，这些功能也可以动态地被撤销。当不能采用继承的方式对系统进行扩充或者采用继承不利于系统扩展和维护时。

###优点###
1. 装饰者模式可以提供比继承更多的灵活性
2. 可以通过一种动态的方式来扩展一个对象的功能，在运行时选择不同的装饰器，从而实现不同的行为。
3. 通过使用不同的具体装饰类以及这些装饰类的排列组合，可以创造出很多不同行为的组合。可以使用多个具体装饰类来装饰同一对象，得到功能更为强大的对象。
4. 具体构件类与具体装饰类可以独立变化，用户可以根据需要增加新的具体构件类和具体装饰类，在使用时再对其进行组合，原有代码无须改变，符合“开闭原则”。
           
###缺点###
1. 会产生很多的小对象，增加了系统的复杂性
2. 这种比继承更加灵活机动的特性，也同时意味着装饰模式比继承更加易于出错，排错也很困难，对于多次装饰的对象，调试时寻找错误可能需要逐级排查，较为烦琐。

###UML结构图###
![DecoratorPattern](/imgs/post/DecoratorPatternUML.png)

###源码###
Component:定义一个对象接口，可以给这些对象动态地添加职责。
```java
public interface Component {

    public void operation();

}
```

ConcreteComponent:定义一个对象，可以给这个对象添加一些职责。
```java
/**
 * 被装饰的对象
 * Created by zhenguo on 11/20/14.
 */
public class ConcreteComponent implements Component {

    @Override
    public void operation() {
        System.out.println("我是被装饰的对象");
    }

}
```

Decorator:拥有一个指向Component对象的引用，并定义一个与 Component接口一致的接口。
```java
public class Decorator implements Component {

    private Component component;

    public Decorator(Component component) {
        this.component = component;
    }

    @Override
    public void operation() {

        if (component != null) {
            component.operation();
        }

    }
}
```

ConcreteDecoratorA:装饰部分A
```java
/**
 * 具体装饰
 * Created by zhenguo on 11/20/14.
 */
public class ConcreteDecoratorA extends Decorator {

    public ConcreteDecoratorA(Component component) {
        super(component);
    }

    @Override
    public void operation() {
        super.operation();
        decorate();
    }

    private void decorate() {
        System.out.println("装饰操作A");
    }
}
```

ConcreteDecoratorB:装饰部分B
```java
/**
 * 具体装饰
 * Created by zhenguo on 11/20/14.
 */
public class ConcreteDecoratorB extends Decorator {

    private String tag = "";

    public ConcreteDecoratorB(Component component) {
        super(component);
        tag = getClass().getSimpleName();
    }

    @Override
    public void operation() {
        super.operation();
        System.out.println("装饰操作B tag = " + tag);
    }
}
```




