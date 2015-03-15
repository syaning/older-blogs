title: Javascript函数
date: 2013-06-23 14:48:40
categories: Javascript
tags:
---
### 一、函数的定义
1. 显式函数，例如：
```javascript
function add(a, b) {
	return a + b;
}
```
2. 使用Function作为对象的构造函数，把参数列表和函数体都作为字符串。可以接受任意多个参数，最后一个为函数体；如果只有一个参数，则为函数体。例如：
```javascript
var add = new Function("a", "b", "return a+b;");
```
3. 匿名构造函数，例如：
```javascript
var add = function(a, b) {
	return a + b;
};
```
这种情况下可以加上默认参数，例如：
```javascript
var result = (function(a, b) {
	return a + b;
})(3,4);
```
而且此时函数可以有另外的名字，例如：
```javascript
var add = function addNum(a, b) {
	return a + b;
};
```

<!-- more -->
### 二、函数特点
1. 因为JavaScript是弱类型的，所以函数定义时不指定参数和返回值类型；
2. 没有重载定义机制。同名函数即使签名不同，后面的定义也将覆盖掉前面的定义；
3. 函数可以嵌套定义。例如：
```javascript
function hypo(a, b) {
	function square(x) {return x * x;}
	return Math.sqrt(square(a) + square(b));
}
```
4. 可以把函数名当作普通变量使用。例如：赋予一个变量、作为数组参数、作为对象属性（此时称作方法）、作为函数参数、作为函数返回值等。

### 三、Function类型
所有函数都是Function类型的实例，用typeof运算符验证时会返回“function”。使用Function构造函数方式允许代码运行时被编译，类似于eval。它有如下属性及方法：
1. prototype属性：预定义的原型对象，一般用于存放所有的实例方法；
2. length属性：函数希望接收的参数个数；
3. call方法和apply方法：用于在指定对象作用域环境下调用函数，相当于修改了函数中this对象的值。两者区别在于接收参数方式不同：
	- 第一个参数都是拟指定的作用域；
	- apply第二个参数以数组形式传入所有参数f.apply(o,[n1,n2,……])；
	- call以序列形式传入所有参数f.call(o,n1,n2,……)；
4. arguments对象：
	- 包含传入函数所有参数的类数组对象；
	- 具有length属性，表示参数的个数；
	- 具有callee属性，指向正在执行的函数：可用于匿名函数调用自身；还具有非标准的caller属性，指向调用当前函数的函数，一般通过arguments.callee.caller来实现调用；
5. this对象：
	- 是关键字，不是变量或者属性名，不能被赋值；
	- 与执行环境和作用域无关，指向函数的调用对象；
	- 与Arguments一起作为函数的隐含参数；
	- 函数调用时this的4种情况：
		（1）直接调用f() this=window
		（2）方法调用o.f() this=o
		（3）call或apply调用f.apply(o,args[]) this=o
		（4）构造函数调用 new f() this为新创建对象