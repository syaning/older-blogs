title: Java模式之抽象工厂模式
date: 2014-03-15 16:11:03
categories: Java
tags:
---
上一篇我们使用了工厂方法模式，使系统很好地满足了开闭原则，增强了系统的扩展性，但是我们忽视了一点，就是人类有性别之分，无论什么颜色的人种，都有男女之分。在这里，我们要引出一个产品族的概念，抽象的定义是：位于不同产品等级结构中，功能相关联的产品组成的家族。在我们的例子中，男人就是一个产品族，女人是另外一个产品族。抽象工厂模式的用意为：给客户端提供一个接口，可以创建多个产品族中的产品对象。使用抽象工厂模式要满足以下条件：
1. 系统中有多个产品族，而系统一次只可能消费其中一族产品；
2. 同属于同一个产品族的产品一起使用时。

抽象工厂模式的各个角色如下（和工厂方法模式基本相同）：
1. 抽象工厂角色：这是工厂方法模式的核心，它与应用程序无关。是具体工厂角色必须实现的接口或者必须继承的父类。在java中它由抽象类或者接口来实现。
2. 具体工厂角色：它含有和具体业务逻辑有关的代码。由应用程序调用以创建对应的具体产品的对象。在java中它由具体的类来实现。
3. 抽象产品角色：它是具体产品继承的父类或者是实现的接口。在java中一般有抽象类或者接口来实现。
4. 具体产品角色：具体工厂角色所创建的对象就是此角色的实例。在java中由具体的类来实现。

<!-- more -->
人类接口：
```java
public interface Human {
    public void laugh();
 
    public void cry();
 
    public void gender();
}
```
黄色人种抽象类：
```java
public abstract class AbstractYellowHuman implements Human {
 
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
黄色人种男人类：
```java
public class YellowMaleHuman extends AbstractYellowHuman {
    @Override
    public void gender() {
        System.out.println("Male yellow human.");
    }
 
}
```
黄色人种女人类：
```java
public class YellowFemaleHuman extends AbstractYellowHuman {
    @Override
    public void gender() {
        System.out.println("Female yellow human.");
    }
 
}
```
其他两色人种的代码实现与此类似，不再赘述。

人类工厂接口：
```java
public interface HumanFactory {
    public Human createWhiteHuman();
 
    public Human createYellowHuman();
 
    public Human createBlackHuman();
}
```
男人工厂类：
```java
public class MaleHumanFactory implements HumanFactory {
 
    @Override
    public Human createWhiteHuman() {
        return new WhiteMaleHuman();
    }
 
    @Override
    public Human createYellowHuman() {
        return new YellowMaleHuman();
    }
 
    @Override
    public Human createBlackHuman() {
        return new BlackMaleHuman();
    }
 
}
```
女人工厂类：
```java
public class FemaleHumanFactory implements HumanFactory {
 
    @Override
    public Human createWhiteHuman() {
        return new WhiteFemaleHuman();
    }
 
    @Override
    public Human createYellowHuman() {
        return new YellowFemaleHuman();
    }
 
    @Override
    public Human createBlackHuman() {
        return new BlackFemaleHuman();
    }
 
}

### 特点：
封装性好，只需要调用工厂类就可以生产产品，而不必关注产品实现的细节。另外就是横向扩展容易，纵向扩展困难。怎么理解呢，就是假如要增加一个双性人，只需要增加产品类和工厂就可以了，但是假如说要增加一个蓝色人种，就必须得修改工厂类的代码。还有一个非常大的有点，高内聚，低耦合，在一个较大的项目组，产品是由一批人定义开发的，但是提供其他成员访问的时候，只有工厂方法和产品的接口，也就是说只需要提供Product Interface 和Concrete Factory 就可以产生自己需要的对象和方法。

### 使用场景：
当一个对象族（或是一组没有任何关系的对象）都有相同的约束，则可以使用抽象工厂模式。例如在软件产品开发过程中，涉及到不同操作系统的时候，都可以考虑使用抽象工厂模式，例如一个应用，需要在三个不同平台上运行：Windows、Linux、MacOS上运行，通过抽象工厂模式屏蔽掉操作系统对应用的影响，三个不同操作系统上的软件功能、应用逻辑、UI都应该是非常类似，唯一不同的是调用不同的工厂方法，由不同的产品类去处理与操作系统交互的信息。