title: Java I/O(四)——Reader/Writer
date: 2014-03-17 16:26:25
categories: Java
tags:
---
Reader和Writer属于字符流（文本I/O），区别于Stream的字节流（二进制I/O）。java.io中的Reader和Writer是两个抽象类，下面将详细介绍。
### Reader
![](/2014/03/17/Java-IO-4/java.io_.Reader.gif)
在上面的关系图中可以看出：
1. Reader 是所有的输入字符流的父类，它是一个抽象类。
2. CharReader、StringReader 是两种基本的介质流，它们分别从Char 数组、String中读取数据。PipedReader 是从与其它线程共用的管道中读取数据。
3. BufferedReader 很明显就是一个装饰器，它和其子类负责装饰其它Reader 对象。
4. FilterReader 是所有自定义具体装饰流的父类，其子类PushbackReader 对Reader 对象进行装饰，会增加一个行号。
5. InputStreamReader 是一个连接字节流和字符流的桥梁，它将字节流转变为字符流。FileReader可以说是一个达到此功能、常用的工具类，在其源代码中明显使用了将FileInputStream 转变为Reader 的方法。

<!-- more -->
### Writer
![](/2014/03/17/Java-IO-4/java.io_.Writer.gif)
在上面的关系图中可以看出：
1. Writer 是所有的输出字符流的父类，它是一个抽象类。
2. CharArrayWriter、StringWriter 是两种基本的介质流，它们分别向Char 数组、String 中写入数据。PipedWriter 是向与其它线程共用的管道中写入数据。
3. BufferedWriter 是一个装饰器，为Writer 提供缓冲功能。
4. PrintWriter 和PrintStream 极其类似，功能和使用也非常相似。
5.OutputStreamWriter 是OutputStream 到Writer 转换的桥梁，它的子类FileWriter 其实就是一个实现此功能的具体类。