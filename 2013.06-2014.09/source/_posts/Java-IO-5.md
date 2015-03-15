title: Java I/O(五)——对应关系
date: 2014-03-17 16:29:07
categories: Java
tags:
---
### 字节流的输入与输出的对应
![](/2014/03/17/Java-IO-5/java_io_stream.jpg)
上图中蓝色的为主要的对应部分，红色的部分就是不对应部分。绿色的虚线部分代表这些流一般要搭配使用。蓝色的部分是IO字节流的主要组成部分，存在极强的对称关系。关于搭配使用的三对类补充一下：ObjectInputStream/ObjectOutputStream和DataInputStream/DataOutputStream主要是要求写对象/数据和读对象/数据的次序要保持一致，否则轻则不能得到正确的数据，重则抛出异常(一般会如此)；PipedInputStream/PipedOutputStream在创建时一般就一起创建，调用它们的读写方法时会检查对方是否存在。
<!-- more -->
1. LineNumberInputStream 主要完成从流中读取数据时，会得到相应的行号，至于什么时候分行、在哪里分行是由该类主动确定的，并不是在原始中有这样一个行号。在输出部分没有对应的部分，我们完全可以自己建立一个LineNumberOutputStream，在最初写入时会有一个基准的行号，以后每次遇到换行时会在下一行添加一个行号。
2. PushbackInputStream 的功能是查看最后一个字节，不满意就放入缓冲区。主要用在编译器的语法、词法分析部分。输出部分的BufferedOutputStream 几乎实现相近的功能。
3. StringBufferInputStream 已经被Deprecated，本身就不应该出现在InputStream部分，主要因为String应该属于字符流的范围。已经被废弃了，当然输出部分也没有必要需要它了！还允许它存在只是为了保持版本的向下兼容而已。
4. SequenceInputStream 可以认为是一个工具类，将两个或者多个输入流当成一个输入流依次读取。
5. PrintStream也可以认为是一个辅助工具。主要可以向其他输出流，或者FileInputStream写入数据，本身内部实现还是带缓冲的。本质上是对其它流的综合运用的一个工具而已。System.out 就是PrintStream 的实例。

### 字符流的输入与输出的对应
![](/2014/03/17/Java-IO-5/java_io_er.jpg)

### 字节输入流与字符输入流的对应
![](/2014/03/17/Java-IO-5/java_input.jpg)

### 字节输出流与字符输出流的对应
![](/2014/03/17/Java-IO-5/java_output.jpg)