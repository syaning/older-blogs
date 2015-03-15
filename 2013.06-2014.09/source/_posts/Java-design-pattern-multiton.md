title: Java模式之多例模式
date: 2014-03-14 16:03:26
categories: Java
tags:
---
多例模式实际上就是单例模式的推广，多例模式又划分为有上限多例模式和无上限多例模式两种，有上限多例模式中的多例类的实例是有上限的，当这个多例类中的上限数值上等于1时，此时，多例类退化回到了单例类；而对于无上限多例模式中的多例类，它的上限是没有限制的，也就是说它的上限数值是不确定的，在此我们近讨论有上限的多例模式。一个非常典型的例子就是一副麻将里只有两枚骰子。总的来说，多例模式有以下特点：
1. 多例类可以有多个实例；
2. 多例类必须自己创建自己的实例，并管理自己的实例，向外界提供自己的实例。
<!-- more -->
```java
import java.util.ArrayList;
import java.util.List;
import java.util.Random;
 
public class Multition {
    private static final int MAX_NUMBER = 3;
    private static List<Multition> instances = new ArrayList<Multition>();
 
    static {
        for (int i = 0; i < MAX_NUMBER; i++)
            instances.add(new Multition());
    }
 
    private Multition() {
 
    }
 
    public static Multition getInstance() {
        Random random = new Random();
        int index = random.nextInt(MAX_NUMBER);
        return getInstance(index);
    }
 
    public static Multition getInstance(int index) {
        if (index >= 0 && index < MAX_NUMBER)
            return instances.get(index);
        else
            return null;
    }
}
```
在这里，有两个getInstance方法，前者随机返回一个实例，后者返回指定的实例。