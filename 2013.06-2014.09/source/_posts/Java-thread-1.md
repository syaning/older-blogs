title: Java多线程(一)
date: 2014-03-18 16:41:02
categories: Java
tags:
---
### 一、基础概念
#### 1. 线程
线程就是程序中单独顺序的流控制，它本身不能运行，只能用于程序中。（线程是程序内的顺序控制流，只能使用分配给程序的资源和环境）
#### 2. 进程
执行中的程序。程序是静态的概念，进程是动态的概念。一个进程可以包含一个或多个线程（至少一个）。
#### 3. 线程与进程的区别
- 多个进程的内部数据和状态都是完全独立的，而多线程是共享一块内存空间和一组系统资源，有可能互相影响。
- 线程本身的数据通常只有寄存器数据，以及一个程序执行时使用的堆栈，所以线程的切换负担比进程切换的负担要小。
- 多线程程序比多进程程序需要更少的管理费用。
- 进程是重量级的任务，需要分配给它们独立的地址空间，进程间通信是昂贵和受限的，进程间的转换也是很需要花费的。
- 线程是轻量级的，它们共享相同的地址空间并且共同分享同一个进程，线程间的通信是便宜的，线程间的转换也是低成本的。

<!-- more -->
### 二、实现多线程
Java中实现多线程的方法有两种：

1.继承Thread类并且覆盖父类的run方法
```java
public class ThreadDemo extends Thread {
    private String message;
 
    public ThreadDemo(String message) {
        this.message = message;
    }
 
    public void run() {
        for (int i = 0; i < 100; i++)
            System.out.println(this.message);
    }
 
    public static void main(String[] args) {
        new ThreadDemo("A new thread").start();
        new ThreadDemo("Another new thread").start();
    }
}
```
但是这种发发会出现一些问题，例如有些时候一个类已经继承了别的类，那么就无法再继承Thread了；另外这种方法将任务和运行任务的机制混合在一起了。一般情况下，将任务从线程中分离出来是更好地设计，即第二种方法。

2.实现Runnable接口
```java
public class Task implements Runnable {
    private String message;
 
    public Task(String message) {
        this.message = message;
    }
 
    @Override
    public void run() {
        for (int i = 0; i < 100; i++)
            System.out.println(message);
    }
 
    public static void main(String[] args) {
        Task task1 = new Task("A new task");
        Task task2 = new Task("Another new task");
 
        new Thread(task1).start();
        new Thread(task2).start();
    }
}
```
要特别注意的是无论使用哪种方法，启动线程的时候都是调用start方法而不是run方法。

### 三、线程的状态
![](/2014/03/18/Java-thread-1/java_thread_state.jpg)
#### 1.创建状态
当用new操作符创建一个新的线程对象时，该线程处于创建状态。处于创建状态的线程只是一个空的线程对象，系统不为它分配资源。
#### 2.可运行状态
执行线程的start()方法将为线程分配必须的系统资源，安排其运行，并调用run()方法，这样就使得该线程处于可运行状态(runnable)，这一状态并不是运行中状态(running)，因为线程也许实际上并未真正运行。
#### 3.不可运行状态
当发生下列事件时，处于运行状态的线程会转入到不可运行状态：
- 调用了sleep()方法
- 线程调用wait()方法等待特定条件的满足
- 线程输入/输出阻塞

返回可运行状态：
- 处于睡眠状态的线程在指定的时间过去后
- 如果线程在等待某一条件，另一个对象必须通过notify()或notifyAll()方法通知等待线程条件的改变
- 如果线程是因为输入输出阻塞，等待输入输出完成

#### 4.消亡状态
当线程的run()方法执行结束后，该线程自然消亡。

### 四、线程的优先级
1. 优先级是1到10的整数
2. 默认情况下，线程继承其父线程的优先级
3. 可通过setPriority改变优先级，getPriority获取优先级
4. 三个常量：Thread.MIN_PRIORITY(1), Thread.NORMAL_PRIORITY(5, default), Thread.MAX_PRIORITY(10)

通常情况下，低优先级的线程只有在没有高优先级的线程运行时才能运行，当多个线程优先级相同时，采用轮转方式。但是，当出现以下情况时，线程也会停止：
1. 线程体中调用了yield()方法，让出了对CPU的占用权，相当于该线程的时间片用完；
2. 线程体中调用了sleep()方法，使线程进入睡眠状态；
3. 线程由于I/O操作而受阻塞；
4. 另一个更高优先级的线程出现；
5. 在支持时间片的系统中，该线程的时间片用完。

### 参考文章
[多线程Multi-Thread 基础](http://www.cnblogs.com/mengdd/archive/2013/02/16/2913628.html)