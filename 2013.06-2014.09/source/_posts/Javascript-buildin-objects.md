title: javascript内建对象
date: 2013-06-29 14:10:13
categories: Javascript
tags:
---
### 一、Object类型
JavaScript中所有对象都继承自Object类型，对象字面量属于Object类型。Object类型拥有一些基本的属性和方法：
1. constructor属性：指向初始化对象的构造函数；
2. toString方法：对象向字符串转换时调用的方法；
3. valueOf方法：对象向基本数据类型转换时调用的方法，未必转换为数字；
4. hasOwnProperty方法和isProprotypeOf方法。

### 二、基本包装类型Boolean、Number和String
1. 可以通过new运算符生成包装类型的实例；
2. typeof运算结果为object；
3. 有相应的属性和方法可以调用；
4. 一般不建议使用Boolean和Number实例；
5. 系统会隐含地进行String基本类型和包装类型的转换，所以总体而言一般没有必要特别注意基本类型和包装类型。

<!-- more -->

### 三、Array类型
1. 所有数组都是Array类型实例，可以使用new关键字方式创建数组。
2. 数组的操作有：
	(1)push、pop方法：数组尾部操作；
	(2)shift、unshift方法：数组头部操作；
	(3)splice方法：数组中部操作：
		a. 删除：splice（起始项，删除数）；
		b. 插入：splice（起始项，0，插入项1，……）；
		c. 替换：splice（起始项，删除数，替换项1，……）；
		d. 返回值是被删除的数组；
	(4)join方法：把数组串成字符串；
	(5)split方法：把字符串分割为数组；
	(6)reverse方法：数组倒置；
	(7)sort方法：数组排序，传入比较函数为参数；
	(8)concat方法：数组串接；
	(9)slice方法：获取子数组。

### 四、Global对象及Math对象
1. Math对象为数学公式和信息提供了属性和方法。
2. 不属于其他对象的属性和方法都属于Global对象，所有全局变量和函数都是Global对象的属性和函数。
3. eval方法：相当于完整的JavaScript解释器，是最强大的函数，谨慎使用。