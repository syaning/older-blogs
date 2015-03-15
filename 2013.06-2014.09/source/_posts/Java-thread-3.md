title: Java多线程(三)
date: 2014-03-18 16:51:31
categories: Java
tags:
---
### 七、线程池
在面向对象编程中，创建和销毁对象是很费时间的，因为创建一个对象要获取内存资源或者其它更多资源。在Java中更是如此，虚拟机将试图跟踪每一个对象，以便能够在对象销毁后进行垃圾回收。所以提高服务程序效率的一个手段就是尽可能减少创建和销毁对象的次数，特别是一些很耗资源的对象创建和销毁。如何利用已有对象来服务就是一个需要解决的关键问题，其实这就是一些”池化资源”技术产生的原因。比如线程池、数据库连接池正是遵循这一思想而产生的。

线程池的作用就是限制系统中执行线程的数量。根据系统的环境情况，可以自动或手动设置线程数量，达到运行的最佳效果；少了浪费了系统资源，多了造成系统拥挤效率不高。用线程池控制线程数量，其他线程排队等候。一个任务执行完毕，再从队列中取最前面的任务开始执行。若队列中没有等待任务，线程池的这一资源处于等待。当一个新任务需要运行时，如果线程池中有等待的工作线程，就可以开始运行了；否则进入等待队列。

使用线程池，减少了创建和销毁线程的次数，每个工作线程都可以被重复利用，可执行多个任务。可以根据系统的承受能力，调整线程池中工作线线程的数目，防止因为消耗过多的内存而导致服务器负荷过重。

<!-- more -->
Java里面线程池的顶级接口是Executor，但是严格意义上讲Executor并不是一个线程池，而只是一个执行线程的工具。真正的线程池接口是ExecutorService，下面是整体的结构图：
![](/2014/03/18/Java-thread-3/java_thread_pool.jpg)
- ExecutorService: 真正的线程池接口
- ScheduledExecutorService: 能和Timer/TimerTask类似，解决那些需要任务重复执行的问题
- ThreadPoolExecutor: ExecutorService的默认实现
- ScheduledThreadPoolExecutor: 继承ThreadPoolExecutor的ScheduledExecutorService接口实现，周期性任务调度的类实现

另外Java还提供了一个Executors类，提供了许多创建线程池的静态方法。
- newFixedThreadPool：创建固定大小的线程池。每次提交一个任务就创建一个线程，直到线程达到线程池的最大大小。线程池的大小一旦达到最大值就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程。
- newCachedThreadPool：创建一个可缓存的线程池。如果线程池的大小超过了处理任务所需要的线程，那么就会回收部分空闲（60秒不执行任务）的线程，当任务数增加时，此线程池又可以智能的添加新线程来处理任务。此线程池不会对线程池大小做限制，线程池大小完全依赖于操作系统（或者说JVM）能够创建的最大线程大小。
- newScheduledThreadPool：创建一个大小无限的线程池。此线程池支持定时以及周期性执行任务的需求。
- newSingleThreadExecutor：创建一个单线程的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。此线程池保证所有任务的执行顺序按照任务的提交顺序执行。
- newSingleThreadScheduledExecutor：创建一个单线程的线程池。此线程池支持定时以及周期性执行任务的需求。

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
 
public class ExecutorDemo {
    public static void main(String[] args){
        ExecutorService executor=Executors.newFixedThreadPool(3);
 
        executor.execute(new PrintChar('a', 100));
        executor.execute(new PrintChar('b', 100));
        executor.execute(new PrintChar('c', 100));
 
        executor.shutdown();
    }
 
}
 
class PrintChar implements Runnable {
    private char chatToPrint;
    private int times;
 
    public PrintChar(char charToPrint, int times) {
        this.chatToPrint = charToPrint;
        this.times = times;
    }
 
    @Override
    public void run() {
        for (int i = 0; i < times; i++)
            System.out.print(chatToPrint);
    }
 
}
```
shutdown方法关闭executor，但允许其中的任务完成，一旦关闭就不再接受新的任务。另外一个方法是shutdownNow，即使线程池中有未完成的线程也立即关闭，返回未完成的任务列表。可以通过isShutdown来获取关闭状态。另外isTerminated方法可以判断任务是否终止，如果所有任务都被终止则返回true.

### 参考文章
[Java 线程池学习](http://www.cnblogs.com/jersey/archive/2011/03/30/2000231.html)
[java自带线程池和队列详细讲解](http://blog.csdn.net/sd0902/article/details/8395677)