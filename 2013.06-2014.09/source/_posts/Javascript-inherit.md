title: Javascript继承机制
date: 2013-06-26 14:21:42
categories: Javascript
tags:
---
### 一、原型链继承
本质是将子类的原型设为父类的实例，如：
```javascript
function SuperType() {
    this.property = true;
}
 
SuperType.prototype.getSuperValue = function() {
    return this.property;
};
 
function SubType() {
    this.subproperty = false;
}
 
SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function() {
    return this.subproperty;
};
 
var instance = new SubType();
```
对象关系图如下：
![](/2013/06/26/Javascript-inherit/20130623120754609.jpg)

**注意：**
1. JavaScript中所有对象都继承自Object对象类型；
2. 为子类添加子类特有方法要在原型替换语句后，即要先声明子类的原型为父类的实例，然后为子类添加其特有的方法；
3. 可以在子类原型中覆盖父类方法，因为子类原型本来就是父类原型的实例，参考实例和原型关系即可理解；
4. 某种意义上前面讲的实例和原型就是一次继承关系，而这里的父类和子类是多次继承关系。

**存在的问题：**
1. 父类中的实例属性会成为子类的原型属性；
2. 无法在不影响所有子类实例情况下向父类构造函数传参。
<!-- more -->

### 二、借用构造函数继承
也称为伪造对象或经典继承，基本思想是在子类构造函数内部调用父类构造函数，如：
```javascript
function SuperType(name) {
    this.name = name;
}
 
function SubType() {
    SuperType.call(this, "Nicholas");
    this.age = 29;
}
 
var instance = new SubType();
```
**注意：**
1. 本质是在子类实例中创建了父类属性，通过instanceof运算符可以发现实际上子类实例不是父类的实例；
2. 应在子类构造函数一开始调用父类构造函数；
3. 必须使用call的调用方式，否则容易造成无限循环。

**存在的问题：**
1. 父类实例方法无法被复用；
2. 父类原型中的属性和方法对子类不可见，因此无法同时使用构造函数和原型。

### 三、组合继承
也称为伪经典继承，基本思想是用原型链实现对原型属性和方法继承，又同时通过借用构造函数实现对实例属性继承。如：
```javascript
function SuperType(name) {
    this.name = name;
}
 
SuperType.prototype.sayName = function() {
    return this.name;
};
 
function SubType(name, age) {
    SuperType.call(this, name);
    this.age = age;
}
 
SubType.prototype = new SuperType();
SubType.prototype.sayAge = function() {
    return this.age;
};
 
var instance = new SubType("Zakas", 29);
```
此时对象关系图如下：
![](/2013/06/26/Javascript-inherit/20130623120904515.jpg)
**特点：**避免了原型链和构造函数的缺点，融合了优点；是最常用的继承方式。
**存在的问题：**任何情况下都要调用两次父类构造函数。

### 四、原型式继承
基本思想是基于对象的继承，如：
```javascript
function beget(o) {
    function F() {}
    F.prototype = o;
    return new F();
}
```
**特点：**基于已有对象创建新对象，本质上是做了已有对象的浅复制（即不会重新创建引用类型的新实例）。
**存在的问题：**因为是浅复制，所以初始对象中的引用类型属性会被所有副本共享。

### 五、寄生式继承
是对原型式继承的扩展，基本思想是在封装函数中扩展原始对象。如：
```javascript
function createExO(o) {
    var clone = beget(o);
    clone.extends = function() {
        return "more";
    };
    return clone;
}
```
**存在的问题：**createExO定义的所有函数都不能复用。

### 六、寄生组合式继承
解决组合式继承中两次调用构造函数的问题。如：
```javascript
function inheritP(sub, super) {
    var pro = beget(super.prototype);
    pro.constructor = sub;
    sub.prototype = pro;
}
 
function SuperType(name) {
    this.name = name;
}
 
SuperType.prototype.sayName = function() {
    return this.name;
};
 
function SubType(name, age) {
    SuperType.call(this, name);
    this.age = age;
}
 
inheritP(SubType, SuperType);
SubType.prototype.sayAge = function() {
    return this.age;
};
 
var instance = new SubType("Zakas", 29);
```
对象关系如图：
![](/2013/06/26/Javascript-inherit/20130623121006687.jpg)