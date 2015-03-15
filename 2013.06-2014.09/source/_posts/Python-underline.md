title: Python中的下划线
date: 2014-04-12 15:32:22
categories: Python
tags:
---
在python编程中，经常会遇到以下划线开头的属性或方法，常见的有以下四种情况：
- xxx，不以下划线开头，表示public
- _xxx，以单个下划线开头，表示private，只有类对象可访问，子类对象不可访问，不可以通过from module import *导入
- __xxx，以双下划线开头，类似于protected，类对象及子类对象可访问
- __xxx__，以双下划线开头和结尾，是系统定义的一些特殊名称

看下面的例子：
```python
class Test(object):
    def public_method(self):
        print('public method')
 
    def __private_methon(self):
        print('private method')
 
    def _protected_method(self):
        print('proteted method')
 
class SubTest(Test):
    pass
 
test = Test()
test.public_method()
test._protected_method()
 
subTest = SubTest()
subTest._protected_method()
```
如果调用 `test.__private_method()` 则会报错。
<!-- more -->
![](/2014/04/12/Python-underline/python-unserline.png)

### 参考文章
[python中那些双下划线开头得函数和变量](http://blog.csdn.net/largetalk/article/details/6990191)
[Python中单/双下划线使用](http://blog.csdn.net/my2010sam/article/details/10949717)
[Python中下划线完全解读](http://blog.163.com/jackylau_v/blog/static/175754040201182113817834/)