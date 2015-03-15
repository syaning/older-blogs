title: Java模式之工厂方法模式
date: 2014-03-14 16:08:38
categories: Java
tags:
---
接着上一篇的简单工厂模式来讲。在简单工厂模式中，工厂类作为核心类，负责产品实例的生产，但是会有这样一个问题，每次增加一个新的产品，都必须要修改工厂类，这样就不利于扩展。因此在工厂方法模式中，使用了一个抽象的工厂来作为核心。下面是它的组成：
1. 抽象工厂角色：这是工厂方法模式的核心，它与应用程序无关。是具体工厂角色必须实现的接口或者必须继承的父类。在java中它由抽象类或者接口来实现。
2. 具体工厂角色：它含有和具体业务逻辑有关的代码。由应用程序调用以创建对应的具体产品的对象。在java中它由具体的类来实现。
3. 抽象产品角色：它是具体产品继承的父类或者是实现的接口。在java中一般有抽象类或者接口来实现。
4. 具体产品角色：具体工厂角色所创建的对象就是此角色的实例。在java中由具体的类来实现。

接着上一篇的例子，Human，WhiteHuman，YellowHuman以及BlackHuman都不变，HumanFactory改成一个接口：
```java
public interface HumanFactory {
    public Human createHuman();
}
```
<!-- more -->
白色人种工厂类：
```java
public class WhiteHumanFactory implements HumanFactory {
    @Override
    public Human createHuman() {
        return new WhiteHuman();
    }
 
}
```
黄色人种工厂类：
```java
public class YellowHumanFactory implements HumanFactory {
    @Override
    public Human createHuman() {
        return new YellowHuman();
    }
 
}
```
黑色人种工厂类：
```java
public class BlackHumanFactory implements HumanFactory {
    @Override
    public Human createHuman() {
        return new BlackHuman();
    }
 
}
```
这样当需要创建人的实例时，只需要创建对应人种的工厂类，然后使用该工厂类来创建相应人种的实例。这样假如现在增加了一个新的人种，例如蓝种人，就不必修改工厂类了，只需要增加一个BlueHuman类和一个BlueHumanFactory就可以了，很好地满足了开闭原则。