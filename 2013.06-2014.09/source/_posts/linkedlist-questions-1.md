title: 链表相关习题（一）
date: 2014-08-19 20:09:06
categories: DS&Algorithm
tags:
---
### 一、判断链表是否有环
在开始之前，我们先来定义链表节点的结构，并且不封装节点成为链表，因此我们在之后的所有操作中都是直接操作节点。
```python
class Node(object):
    def __init__(self, val):
        self.val = val
        self.next = None

n1 = Node(1)
n2 = Node(2)
n3 = Node(3)
n4 = Node('a')
n5 = Node('b')
n6 = Node('c')
n7 = Node(10)
n8 = Node(11)
```
<!-- more -->
#### 1. 判断链表中是否存在环
使用追赶的方法，声明两个指针slow和fast，从链表头开始，每次分别前进1步和2步。如果存在环，那么两者将会相遇，否则fast会遇到链表结尾退出。程序实现如下：
```python
lsta = n1
n1.next = n2
n2.next = n3
n3.next = n4
n4.next = n5
n5.next = n6
n6.next = n3

lstb = n7
n7.next = n8

def hasLoop(lst):
    slow, fast = lst, lst
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break
    return not(fast == None or fast.next == None)

print(hasLoop(lsta))  # True
print(hasLoop(lstb))  # False
```
这里我们构建了lsta和lstb两条链表，其中lsta中有环，lstb中无环。

也可以通过对链表中每一个元素计算哈希值，如果出现重复的哈希值，则说明有重复元素，即链表中包含环。代码如下：
```python
def hasLoop(lst):
    hashTable = {}
    temp = lst
    while temp:
        hashVal = str(hash(temp))
        if hashTable.get(hashVal):
            return True
        else:
            hashTable[hashVal] = temp
        temp = temp.next
    
    return False

print(hasLoop(lsta))  # True
print(hasLoop(lstb))  # False
```

#### 2. 求链表中环的长度
在确定了链表中有环后，我们让slow和fast继续前进，当它们再次相遇时，slow绕环走了1圈，fast绕环走了2圈，据此即可求出环的长度。示例代码如下：
```python
def loopLength(lst):
    slow, fast = lst, lst
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break
    
    if fast == None or fast.next == None:
        return 0
    
    loopLen = 1
    slow = slow.next
    fast = fast.next.next
    while slow != fast:
        slow = slow.next
        fast = fast.next.next
        loopLen += 1
    
    return loopLen

print(loopLength(lsta))  # 4
```

#### 3. 求环的入口点
假设非环的部分长度为a，环的长度为b，slow和fast相遇时从环的起始点走了x，用s表示slow走过的路程，则`s=a+nb+x`，`2s=a+mb+x`，且m=n+2k，两式相减，即可得到`s=(m-n)b`，所以`(m-n)b=a+nb+x`，于是`a+x=(m-2n)b`。当slow和fast在x处相遇时，此时让fast从x处开始走a步（每次1步），即可到达环的入口点；同时令slow从链表头开始走a步，也刚好到达环的入口点。（事实上，由于之前slow和fast是第一次相遇，因此n=0）
```python
def getLoopEntry(lst):
    slow, fast = lst, lst
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break
        
    if fast == None or fast.next == None:
        return None

    slow = lst
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    return slow

print(getLoopEntry(lsta).val)  # 3
```

#### 4. 求有环链表的长度
2中求出了环的长度，3中可以得到非环部分的长度，相加即为环的长度。

#### 参考文章
[如何判断链表中是否有环](http://blog.csdn.net/thefutureisour/article/details/8174313)
[链表中是否有环及环的起点 cycle detect 及确定环的长度](http://blog.csdn.net/thestoryofsnow/article/details/6822576)

### 二、判断链表是否相交
#### 1. 两个链表均无环
两个无环链表相交，则这两个链表的最后一个元素必然相同，通过这个判断即可知道链表是否相交，代码如下：
```python
lsta = n1
n1.next = n2
n2.next = n3
n3.next = n4

lstb = n5
n5.next = n6
n6.next = n3

def cross(lst1, lst2):
    tail1, tail2 = lst1, lst2
    
    while tail1.next != None:
        tail1 = tail1.next
    while tail2.next != None:
        tail2 = tail2.next
    
    return tail1 == tail2

print(cross(lsta, lstb))  # True
```
也可以将第一个链表的尾端与第二个链表的头相连，然后判断第一个链表中是否有环，如果有环，则两个链表相交，代码如下：
```python
def cross(lst1, lst2):
    tail1 = lst1
    
    while tail1.next != None:
        tail1 = tail1.next
    tail1.next = lst2
    
    return hasLoop(lst1)

print(cross(lsta, lstb))  # True
```

#### 2. 不知道是否有环
首先判断两个链表是否有环，如果都无环，则回到问题1；如果一个有环一个无环，则它们必定不相交；考虑两个链表均有环的情况，如果两个链表都有环，则环必定是两个链表共有的，因此首先找出第一个链表的环入口，如果该入口在第二个链表上，则两个链表相交，否则不相交。

#### 3. 找出两个链表的相交点
如果两个链表都无环，则使用问题1的第二种方法人为构环，回到求有环链表的环入口点问题；如果两个链都有环，则首先求出两个链表的长度La和Lb,它们的差值为`L=|La-Lb|`，然后让长的链表先走L，然后同时开始走，当他们相遇时即为交点。



#### 参考文章
[判断链表是否有环及两链表是否相交](http://blog.csdn.net/kevinzhangyang/article/details/6637503)
[编程之美：判断两链表是否相交](http://blog.csdn.net/liuxialong/article/details/6556096)
