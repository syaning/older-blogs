title: HTTP 协议
date: 2014-04-01 10:51:22
categories: Network
tags:
---
## 一、HTTP请求
### 1. 请求行
格式：Method Request-URI HTTP-Version CRLF
Method为请求方法，Request-URI为请求地址，HTTP-Version为HTTP协议版本，常用为HTTP/1.1，CRLF表示回车换行。请求方法如下：
**GET：**请求获取Request-URI所标识的资源
**POST：**在Request-URI所标识的资源后附加新的数据
**HEAD：**请求获取由Request-URI所标识的资源的响应消息报头
**PUT：**请求服务器存储一个资源，并用Request-URI作为其标识
**DELETE：**请求服务器删除Request-URI所标识的资源
**TRACE：**请求服务器回送收到的请求信息，主要用于测试或诊断
**CONNECT：**保留将来使用
**OPTIONS：**请求查询服务器的性能，或者查询与资源相关的选项和需求

## 二、HTTP响应
HTTP响应也是由三个部分组成，分别为：状态行、消息报头、响应正文。
### 1. 状态行
格式：HTTP-Version Status-Code Reason-Phrase CRLF
HTTP-Version表示服务器HTTP协议的版本，通常为HTTP/1.1，Status-Code表示服务器发回的响应状态代码，Reason-Phrase表示状态代码的文本描述。状态代码有三位数字组成，第一个数字定义了响应的类别，且有五种可能取值：
**1xx：**指示信息–表示请求已接收，继续处理
**2xx：**成功–表示请求已被成功接收、理解、接受
**3xx：**重定向–要完成请求必须进行更进一步的操作
**4xx：**客户端错误–请求有语法错误或请求无法实现
**5xx：**服务器端错误–服务器未能实现合法的请求
具体的状态码、状态描述及说明参考[wiki](http://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)
<!-- more -->
## 三、报头
HTTP消息报头包括普通报头、请求报头、响应报头、实体报头。每一个报头域格式都是name: value，消息报头域的名字是大小写无关的。
### 1. 普通报头
在普通报头中，有少数报头域用于所有的请求和响应消息，但并不用于被传输的实体，只用于传输的消息。

**Cache-Control：**用于指定缓存指令，缓存指令是单向的（响应中出现的缓存指令在请求中未必会出现），且是独立的（一个消息的缓存指令不会影响另一个消息处理的缓存机制）。
请求时的缓存指令包括：no-cache（用于指示请求或响应消息不能缓存）、no-store、max-age、max-stale、min-fresh、only-if-cached;响应时的缓存指令包括：public、private、no-cache、no-store、no-transform、must-revalidate、proxy-revalidate、max-age、s-maxage.
**Date：**表示消息产生的日期和时间
**Connection：**指定连接的选项。例如keep-alive指定连接是连续，或者指定close选项，通知服务器，在响应完成后，关闭连接。
### 2. 请求报头
请求报头允许客户端向服务器端传递请求的附加信息以及客户端自身的信息。

**Accept：**指定客户端接受哪些类型的信息。如Accept：image/gif，表明客户端希望接受gif图象格式的资源；Accept：text/html，表明客户端希望接受html文本。
**Accept-Charset：**指定客户端接受的字符集。如Accept-Charset:iso-8859-1,gb2312.如果在请求消息中没有设置这个域，缺省是任何字符集都可以接受。
**Accept-Encoding：**类似于Accept，但是它是用于指定可接受的内容编码。如Accept-Encoding:gzip.deflate.如果请求消息中没有设置这个域服务器假定客户端对各种内容编码都可以接受。
**Accept-Language：**类似于Accept，但是它是用于指定一种自然语言。如Accept-Language:zh-cn.如果请求消息中没有设置这个报头域，服务器假定客户端对各种语言都可以接受。
**Authorization：**证明客户端有权查看某个资源。当浏览器访问一个页面时，如果收到服务器的响应代码为401（未授权），可以发送一个包含Authorization请求报头域的请求，要求服务器对其进行验证。
**Host：**（发送请求时，该报头域是必需的）指定被请求资源的Internet主机和端口号，它通常从HTTP URL中提取出来的。例如在浏览器中输入：http://www.example.com/index.html ，浏览器发送的请求消息中，就会包含Host请求报头域Host：www.guet.edu.cn此处使用缺省端口号80，若指定了端口号，则变成Host：www.guet.edu.cn:端口号。
**User-Agent：**我们上网登陆论坛的时候，往往会看到一些欢迎信息，其中列出了你的操作系统的名称和版本，你所使用的浏览器的名称和版本，这往往让很多人感到很神奇，实际上，服务器应用程序就是从User-Agent这个请求报头域中获取到这些信息。User-Agent请求报头域允许客户端将它的操作系统、浏览器和其它属性告诉服务器。不过，这个报头域不是必需的，如果我们自己编写一个浏览器，不使用User-Agent请求报头域，那么服务器端就无法得知我们的信息了。

### 3. 响应报头
响应报头允许服务器传递不能放在状态行中的附加响应信息，以及关于服务器的信息和对Request-URI所标识的资源进行下一步访问的信息。

**Location：**重定向接受者到一个新的位置。Location响应报头域常用在更换域名的时候。
**Server：**包含了服务器用来处理请求的软件信息。与User-Agent请求报头域是相对应的。例如Server：Apache-Coyote/1.1
**WWW-Authenticate：**必须被包含在401（未授权的）响应消息中，客户端收到401响应消息时候，并发送Authorization报头域请求服务器对其进行验证时，服务端响应报头就包含该报头域。

### 4、实体报头
请求和响应消息都可以传送一个实体。一个实体由实体报头域和实体正文组成，但并不是说实体报头域和实体正文要在一起发送，可以只发送实体报头域。实体报头定义了关于实体正文（如：有无实体正文）和请求所标识的资源的元信息。

**Content-Encoding：**用作媒体类型的修饰符，它的值指示了已经被应用到实体正文的附加内容的编码，因而要获得Content-Type报头域中所引用的媒体类型，必须采用相应的解码机制。Content-Encoding这样用于记录文档的压缩方法，如Content-Encoding：gzip
**Content-Language：**描述了资源所用的自然语言。没有设置该域则认为实体内容将提供给所有的语言阅读者。如Content-Language:da
**Content-Length：**指明实体正文的长度，以字节方式存储的十进制数字来表示。
**Content-Type：**指明发送给接收者的实体正文的媒体类型。如Content-Type:text/html;charset=ISO-8859-1
**Last-Modified：**指示资源的最后修改日期和时间。
**Expires：**给出响应过期的日期和时间。为了让代理服务器或浏览器在一段时间以后更新缓存中(再次访问曾访问过的页面时，直接从缓存中加载，缩短响应时间和降低服务器负载)的页面，我们可以使用Expires实体报头域指定页面过期的时间。如Expires：Thu，15 Sep 2013 16:23:12 GMTHTTP1.1的客户端和缓存必须将其他非法的日期格式（包括0）看作已经过期。因此为了让浏览器不要缓存页面，我们也可以利用Expires实体报头域，设置为0，jsp中程序如下：response.setDateHeader(“Expires”,”0″);

### 参考文章
[HTTP协议详解](http://www.cnblogs.com/li0803/archive/2008/11/03/1324746.html)
[HTTP状态码](http://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)