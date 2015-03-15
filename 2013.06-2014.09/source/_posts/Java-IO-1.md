title: Java I/O(一)——概述
date: 2014-03-17 16:17:18
categories: Java
tags:
---
Java IO一般包含两个部分：java.io包中堵塞型IO；java.nio包中的非堵塞型IO，通常称为New IO。系统运行的瓶颈一般在于IO操作，一般打开某个IO通道需要大量的时间，同时端口中不一定就有足够的数据，这样read方法就一直等待读取此端口的内容，从而浪费大量的系统资源。此时可能会想到使用java 的多线程技术，但是在当前进程中创建线程也是要花费一定的时间和系统资源的，因此不一定可取。Java New IO的非堵塞技术主要采用了Observer模式，就是有一个具体的观察者监测IO端口，如果有数据进入就会立即通知相应的应用程序。这样我们就避免建立多个线程，同时也避免了read等待的时间。

本篇主要讲述java的堵塞型IO，java.io包中有着众多的类和接口，大概可以分为三部分：1、流式部分，即IO的主体部分；2、非流式部分，主要包含一些辅助流式部分的类，如：File、RandomAccessFile和FileDescriptor等；3、文件读取部分的与安全相关的类，如：SerializablePermission类。流式部分可以概括为：两个对应一个桥梁。两个对应指：字节流(Byte Stream)和字符流(Char Stream)的对应；输入和输出的对应。一个桥梁指：从字节流到字符流的桥梁，对应于输入和输出为 InputStreamReader 和 OutputStreamWriter。

在流的具体类中又可以具体分为介质流和过滤流。介质流(Media Stream 或者称为原始流Raw Stream)主要指一些基本的流，他们主要是从具体的介质上，如：文件、内存缓冲区(Byte 数组、Char 数组、StringBuffer 对象)等，读取数据；过滤流(Filter Stream)主要指所有 FilterInputStream/FilterOutputStream 和 FilterReader/FilterWriter 的子类，主要是对其包装的类进行某些特定的处理，如：缓存等。

### 参考文章
[java中的io系统详解](http://blog.csdn.net/ilibaba/article/details/3955799)
[Java IO](http://m.blog.csdn.net/blog/baobeiSimple/1713797)