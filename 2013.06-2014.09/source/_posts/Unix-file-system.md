title: Unix文件系统
date: 2014-08-09 00:03:58
categories: Computer System
tags:
---
### 块
Unix文件系统的最底层是物理设备，如磁盘、U盘等，这些物理设备的存储空间被划分为固定大小的单位，叫做块（block），块是文件存储的最小单元，例如一个文件有50K，当blocksize为2K时，需要25个block；当blocksize为4K时，则需要13个block来存储。blocksize需要考虑很多因素，当blocksize过大，那么存储多个小文件时就会浪费很多空间；如果blocksize过小，那么就需要更多的空间来记录block的分配情况，因此blocksize需要折中考虑，现在多数机器的blocksize为4K左右。

下图表示的是文件系统的磁盘布局：
![](/2014/08/09/Unix-file-system/fs-block-layout.png)

 - 引导块（Boot block）占用第0号物理块，不属于文件系统管辖，如果系统中有多个文件系统，只有根文件系统才有引导程序放在引导块中，其余文件系统都不使用引导块。
 - 超级块（Super block）一般位于磁盘的第1块，用来保存文件系统的一些基本信息，例如文件系统大小，位置等。
 - 位图（Bitmap）用来跟踪空闲块，位图中的第i位记录了第i块的使用情况。
 - inode表记录inode信息（参看下文）。
 - 文件块（File block）用来存储文件内容。

 由于一个文件通常需要多个块，因此需要知道每个文件使用了哪些块，因此引入了inode来记录块和文件之间的隶属关系，inode的基本定义为：
 ```
 structure inode
 	integer block_numbers[N]	//组成文件的块号
 	integer size	//文件大小（单位：字节）
 ```
第六版的Unix系统使用这个算法来处理块数不大于8的文件，当文件较大时，inode的前7块为间接块，其中存储块号，第8块为二次间接块。假如blocksize为512B，每个块号为2B，那么每个块可以存储256个块号，因此一个文件最多可以有7x256+1x256x256=67328个块。

<!-- more -->
### 文件名
在实际过程调用中，使用inode很不方便，因此每个inode有一个唯一的inode号与之对应，即一个inode号对应一个inode，这些对应信息存储在inode表里。然而inode号依然不是用户友好的，因此需要给文件命名。文件名隐藏了文件管理的元数据，并且是用户友好的。当新建一个文件时，系统首先分配一个inode，初始化文件元数据，然后在特定目录将文件名和inode号绑定；写文件时，系统为inode分配文件块。

文件的命名系统主要分为目录和普通文件。目录的文件块中存储的是文件名和inode号的对照表，文件的文件块中存储的是文件内容。因此对于inode结构，需要增加一个域来标识它是目录还是普通文件，如下:
```
structure inode
	integer block_numbers[N]	//组成文件的块号
	integer size	//文件大小（单位：字节）
	integer type	//类型：目录或者普通文件
```

### 连接
#### 1. 硬连接（Hard link）
Unix文件系统支持文件别名，即连接。也就是说，我们可以将多个不同的文件名映射到同一个inode，但是在本质上只有一份文件。假如最开始我们创建了一个a.txt的文件，然后又创建了一个硬连接b.txt，那么这两个文件的内容是完全同步的，用户所看到的只是文件的两个名字，在存储的时候，存的是同一份。用代码示例就是：
```java
A a1 = new A();
A a2 = a1;
```
这里我们只是创建了一个对象，但是a1和a2两个指针均指向这个对象。

在删除文件的时候，会解除该文件名与inode号的绑定，当该文件名是此inode号的最后一个绑定时，就将该文件的文件块置于空闲列表中。因此每个inode需要增加一个引用次数来记录该文件被引用的次数，当引用次数为0的时候，就可以将文件块置于空闲列表中。然而引用次数的使用要求不能存在循环引用，因此Unix文件系统禁止对目录建立连接以保证命名网络为有向无环图。假设存在`a`目录，并且执行`LINK("a/b/c","a")`，那么当删除a时，由于a对应的inode号的引用次数大于0（此时c对其有个引用），因此a对应的文件块不会被释放。但此时a目录已经不可访问，相当于命名网络中出现了孤岛，因此a对应的文件块就永远得不到释放。

实际系统中，每个目录都包含一个纸箱自身的连接，用`.`表示，因此目录创建之后，对应的inode的引用计数就是2.同时，每个目录默认包含一个指向父目录的连接，用`..`表示，只有当一个目录中只包含`.`和`..`时，它才可以被删除，从而保证命名网络中不会出现孤岛。

#### 2. 软连接（Symbolic link）
软连接又叫做符号链接，它其实是一种特殊的文件，其文件内容包含着另一文件的位置信息，用代码示例就是：
```java
A a = new A();
B b = new B();
b.link = a;
```
因此当删除原文件后，软连接就会无效，因为此时相当于`b.link=null`了。

### 案例分析
![](/2014/08/09/Unix-file-system/unix-file-system.png)
如图所示，我们来查找文件`/programs/pong.c`
 - 我们先看根inode，发现其文件块为14、37、16等
 - 然后我们看编号为14的块，其中存储的是文件名和inode的对照表，其中有一个programs的文件，其inode号为7
 - 查看编号为7的inode，发现其文件块为23、28、55、51等
 - 查看文件块23，发现其中是文件名和inode的对照表，发现pong.c对应的inode号为9
 - 查看编号为9的inode，发现其文件块为61、44、15等
 - 找到编号为61的块，这里存储的即为文件内容

### 参考书籍
Principles of Computer System Design (Jerome H.Saltzer & M.Frans Kaashoek)