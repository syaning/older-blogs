title: Java模式之简单工厂模式
date: 2014-03-14 16:05:43
categories: Java
tags:
---
简单工厂模式又叫静态工厂模式，顾名思义，它是用来实例化目标类的静态类。通常由以下三种角色组成：
1. 工厂类角色：这是本模式的核心，含有一定的商业逻辑和判断逻辑，在java中它往往由一个具体类实现；
2. 抽象产品角色：它一般是具体产品继承的父类或者实现的接口，在java中由接口或者抽象类来实现；
3. 具体产品角色：工厂类所创建的对象就是此角色的实例，在java中由一个具体类实现。

以女娲造人为例，有白种人、黄种人和黑种人。这里女娲造人所使用的工具就相当于是工厂，人类这一统称就是抽象产品，而具体的人种就是具体产品。

<!-- more -->
人类接口：
```java
public interface Human {
    public void laugh();
 
    public void cry();
}
```
白色人种类：
```java
public class WhiteHuman implements Human {
 
    @Override
    public void laugh() {
        System.out.println("White human is laughing.");
    }
 
    @Override
    public void cry() {
        System.out.println("White human is crying.");
    }
 
}
```
黄色人种类：
```java
public class YellowHuman implements Human {
 
    @Override
    public void laugh() {
        System.out.println("Yellow human is laughing.");
    }
 
    @Override
    public void cry() {
        System.out.println("Yellow human is crying.");
    }
 
}
```
黑色人种类：
```java
public class BlackHuman implements Human {
 
    @Override
    public void laugh() {
        System.out.println("Black human is laughing.");
    }
 
    @Override
    public void cry() {
        System.out.println("Black human is crying.");
    }
 
}
```
人类工厂类：
```java
public class HumanFactory {
    public static Human createHuman(Class c) {
        Human human = null;
        try {
            human = (Human) Class.forName(c.getName()).newInstance();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return human;
    }
}
```
**优点：**
工厂类是整个模式的关键，包含了必要的逻辑判断，根据外界给定的信息，决定究竟应该创建哪个具体类的对象。通过使用工厂类，外界可以从直接创建具体产品对象的尴尬局面摆脱出来，仅仅需要负责使用对象就可以了，而不必管这些对象究竟如何创建及如何组织的。明确了各自的职责和权利，有利于整个软件体系结构的优化。

**缺点：**
由于工厂类集中了所有实例的创建逻辑，将全部创建逻辑集中到了一个工厂类中，它所能创建的类只能是事先考虑到的，如果需要添加新的类，则就需要改变工厂类了。当系统中的具体产品类不断增多时候，可能会出现要求工厂类根据不同条件创建不同实例的需求．这种对条件的判断和对具体产品类型的判断交错在一起，很难避免模块功能的蔓延，对系统的维护和扩展非常不利。解决方法是使用工厂方法模式。

基于简单工厂方法的优缺点考虑，该模式仅在比较简单的情况下使用，例如产品类较少，业务逻辑较为简单，产品类型不会扩展的情况下使用。