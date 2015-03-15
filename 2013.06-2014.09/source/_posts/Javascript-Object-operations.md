title: Javascript对象和属性操作
date: 2013-06-27 14:15:53
categories: Javascript
tags:
---
### 一、判断对象属于哪个基本类型：typeof运算符
```
typeof x is undefined
typeof true is boolean
typeof 'Jerry' is string
typeof 2 is number
typeof instance is object
typeof null is object
typeof SuperType is function
```

### 二、判断对象是否是某个类型的实例：instanceof运算符
某个实例是其继承原型链上所有原型类型的实例，注意如果是借用构造函数的继承，那么子类的实例不是父类的实例，因为在这种情况下，仅仅是在子类实例中声明了父类的属性，因此事实上没有产生继承关系。

### 三、判断对象是否是某个实例的原型：isPrototypeOf()函数
```javascript
var o = {};
Object.prototype.isPrototypeOf(o); // true
Object.isPrototypeOf(o); // false
o.isPrototypeOf(Object); // false
Fucntion.prototype.isPrototypeOf(Object); // true
```
<!-- more -->
```javascript
SubType.prototype.isPrototypeOf(instance); // true
SuperType.prototype.isPrototypeOf(instance); // true
Object.prototype.isPrototypeOf(instance); // true
SubType.isPrototypeOf(instance); // false
```

### 四、判断某个属性是否从属于某个对象：in运算符
1. 只判断某个对象是否可以使用某个属性，并不辨别该属性是从原型继承来的还是实例自身属性；
2. in运算符的左侧需是字符串或可转为字符串。

###五、判断某个属性是否属于某个对象的实例属性：hasOwnProperty()方法
只判断某个对象是否可以使用某个属性，且该属性属于实例自身属性，不是从原型继承所得。