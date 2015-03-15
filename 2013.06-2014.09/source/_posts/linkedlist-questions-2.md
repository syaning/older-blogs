title: 链表相关习题（二）
date: 2014-08-20 22:45:47
categories: DS&Algorithm
tags:
---
### 1. 求单链表的倒数第k个元素
使用两个指针，第一个指针先走k步，然后第二个指针从头开始走，当第一个指针到达结尾时，第二个指针所在位置即为所求。
```python
def printList(lst):
    temp = lst
    while temp != None:
        print(temp.val, end=' ')
        temp = temp.next
```
<!-- more -->
### 2. 单链表就地逆置
每次向后走一步，把指针逆转方向，其实等同于把后面的元素依次插入到链表头。
```python
def reverseList(lst):
    a, b = None, lst
    while b != None:
        c = b.next
        b.next = a
        a, b = b, c
    return a
```

### 3. 输入链表头，逆向输出链表节点
方法一：可以先将链表逆转，然后输出，但是这样改变了链表的结构；
方法二：遍历链表，将节点压入栈中，然后出栈，但是需要额外的空间开销
方法三：递归，每次遇到一个节点，先打印出之后的节点，代码如下：
```python
def reversePrint(lst):
    if lst == None:
        return
    reversePrint(lst.next)
    print(lst.val, end=' ')
```

### 4. 给定链表的头指针和一个结点指针，在O(1)时间删除该节点
如果该节点是链表尾，则遍历链表，删除节点；否则将该节点的内容替换为下一个节点的内容，并删除该节点的下一个。
```python
def deleteNode(lst, node):
    if node.next == None:
        temp = lst
        while temp.next != node:
            temp = temp.next
        temp.next = None
    else:
        node.val = node.next.val
        node.next = node.next.next
```

### 参考文章
[[分类整理II]微软等100题系列V0.1版：链表面试题集锦](http://blog.csdn.net/v_july_v/article/details/6076139)
[输入一个单向链表，输出该链表中倒数第k个结点](http://blog.chinaunix.net/uid-20618535-id-274044.html)
[题目：输入一个链表的头结点，从尾到头反过来输出每个结点的值](http://blog.csdn.net/zhongjiekangping/article/details/5611704)