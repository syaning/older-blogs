title: Java I/O(六)——一般使用原则
date: 2014-03-17 16:33:56
categories: Java
tags:
---
### 一、按数据来源（去向）分类：
1. 是文件: FileInputStream, FileOutputStream, FileReader, FileWriter
2. 是 byte[]: ByteArrayInputStream, ByteArrayOutputStream
3. 是 Char[]: CharArrayReader, CharArrayWriter
4. 是 String: StringBufferInputStream, StringBufferOuputStream, StringReader, StringWriter
5. 网络数据流: InputStream, OutputStream, Reader, Writer

### 二、按是否格式化输出分：
1. 要格式化输出: PrintStream, PrintWriter

<!-- more -->
### 三、按是否要缓冲分：
1. 要缓冲: BufferedInputStream, BufferedOutputStream, BufferedReader, BufferedWriter

### 四、按数据格式分：
1. 二进制格式(只要不能确定是纯文本的): InputStream, OutputStream 及其所有带 Stream 结束的子类
2. 纯文本格式(含纯英文与汉字或其他编码方式): Reader, Writer 及其所有带 Reader, Writer 的子类

### 五、按输入输出分：
1. 输入: Reader, InputStream 类型的子类
2. 输出: Writer, OutputStream 类型的子类

### 六、特殊需要：
1. 从 Stream 到 Reader, Writer 的转换类: InputStreamReader, OutputStreamWriter
2. 对象输入输出: ObjectInputStream, ObjectOutputStream
3. 进程间通信: PipeInputStream, PipeOutputStream, PipeReader, PipeWriter
4. 合并输入: SequenceInputStream
5. 更特殊的需要: PushbackInputStream, PushbackReader, LineNumberInputStream, LineNumberReader

### 一般使用原则
1. 考虑最原始的数据格式是什么：原则四
2. 是输入还是输出：原则五
3. 是否需要转换流：原则六第 1 点
4. 数据来源(去向)是什么：原则一
5. 是否要缓冲：原则三(特别注明:一定要注意的是 readLine() 是否有定义，有什么比 read, write 更特殊的输入或输出方法)
6. 是否要格式化输出：原则二