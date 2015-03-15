title: "Java模式之单例模式"
date: 2014-03-14 11:12:55
categories: Java
tags:
---
Java单例模式是一种常见的设计模式，该模式有以下三个特点：
1. 单例类只能有一个实例；
2. 单例类必须自己自己创建自己的唯一实例；
3. 单例类必须给所有其他对象提供这一实例。

常用的单例模式设计有三种：懒汉式单例、饿汉式单例、登记式单例。
### 1. 懒汉式单例
```java
public class Singleton {
    private static Singleton singleton;
 
    private Singleton() {
 
    }
 
    public static Singleton getInstance() {
        if (singleton == null)
            singleton = new Singleton();
        return singleton;
    }
}
```
<!-- more -->
所谓懒汉式，就是等到需要的时候再创建实例。但是这样有个缺点，就是可能会创建出不止一个的实例。例如在一个B/S 项目中，每个HTTP Request请求到J2EE的容器上后都创建了一个线程，每个线程都要创建同一个单例对象，此时假如有A和B两个线程，当A线程请求内存创建实例的时候，B线程的请求到达，此时检测带实例为null，因为此时A还未创建完成，因此此时就会出问题。为了避免这种情况，使用饿汉式单例要好一些。或者使用一下方式来避免这种情况：
```java
public class Singleton {
    private static volatile Singleton singleton;
 
    private Singleton() {
 
    }
 
    public static Singleton getInstance() {
        if (singleton == null)
            synchronized (Singleton.class) {
                if (singleton == null)
                    singleton = new Singleton();
            }
        return singleton;
    }
}
```
### 2. 饿汉式单例
```java
public class Singleton {
    private static Singleton singleton = new Singleton();
 
    private Singleton() {
 
    }
 
    public static Singleton getInstance() {
        return singleton;
    }
}
```
这种方法是在一开始就创建了实例，以后直接使用就可以了。
### 3. 登记式单例
```java
import java.util.HashMap;
import java.util.Map;
 
public class Singleton {
    private static Map<String, Singleton> map=new HashMap<String,Singleton>();
 
    static{
        Singleton singleton=new Singleton();
        map.put(singleton.getClass().getName(), singleton);
    }
 
    protected Singleton() {
 
    }
 
    public static Singleton getInstance(String name) {
        if(name==null)name=Singleton.class.getName();
        if(map.get(name)==null){
            try {
                map.put(name, (Singleton)Class.forName(name).newInstance());
            } catch (InstantiationException e) {
                e.printStackTrace();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            }
        }
        return map.get(name);
    }
}
```
登记式单例的最大特点是可以被继承，但是没觉得有多大用处，不如饿汉式方便易用。