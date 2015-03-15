title: Javascript对象
date: 2013-06-24 14:30:05
categories: Javascript
tags:
---
### 一、对象字面量来定义对象
```javascript
var student = {
    id : "9924077",
    name : "test",
    sclass : [ "web", "java" ],
    test : { x : 1,  y : 1 },
    getId : function() { return this.id; }
};
```
**注意：**
1. {}中属性名值对，名值间用冒号分开，彼此间用逗号分开
2. 如果属性名不是合法标识符，则需用 ” “括住，如”first-name”: “zhu”
3. 值可以是变量，可以是对象，方法可以认为是特殊的属性

**缺点：**
1. 多个对象实例需逐个定义，代码冗长；
2. 多个对象实例不能共享方法，浪费内存；
3. 无法识别对象实例的类型；
4. 无法体现对象实例间关系。
<!-- more -->

二、属性访问与操作
**属性访问（访问不存在的属性值会返回undefined）**
1. 可以使用点表示法，如student.id来访问属性；
2. 可以使用方括号表示法，如student["id"]来访问属性。
3. 区别：前者属性名为标识符，所以应预先明确；后者属性名为字符串，可由程序动态生成。

**属性操作：**
1. 可以通过把值赋予新属性来为对象添加属性，例如：student.room = “301”;
2. 可以通过赋值来修改属性，甚至可改变属性类型；
3. 可以使用for……in语句来遍历属性，如：for(var i in student)；
4. 只能遍历可枚举属性和方法；
5. 会将继承到的属性和方法一并枚举出来；
6. 遍历结果是乱序的，如果希望有序，可先自定义属性名数组；
7. 可以使用delete运算符来删除属性，如：delete student.room；删除后属性不止值被设为undefined，属性也不再被枚举到。

### 三、构造函数创建对象
可以通过构造函数来创建多个同类对象，例如：
```javascript
function Student(id) {
    this.id = id;
    this.getId = function() { return this.id; };
}
var student1 = new Student("9924077");
var student2 = new Student("9924078");
```
**存在的问题：**
1. 多个对象实例不能共享方法，浪费内存；
2. 某个实例所做扩展无法被同类其他实例看到；
3. 构造函数也可以作为普通函数调用，如果在某个对象的作用域中，所有属性和方法被附加到该对象上，则默认会被附加到全局对象上，例如：
```javascript
function Student(id) {
this.testid= id;
this.gettestId = function() { return this.testid; };
}
Student("9924078");
document.write("windon.testid is" + window.gettestId());      //windon.testid is 9924078
```
4. 如果函数中返回对象，新建对象将被赋值为该对象，而不是this对象，类型也就可能有问题 ，例如：
```javascript
function Student(id) {
this.id= id;
this.getId = function() { return this.id; };
return{
name: "9924078",
getName : function() { return this.name; }
};
}
varstudent1 = new Student("9924077");
document.write("student1.id is "+ student1.id);      //student1.id is undefined
document.write("student1.name is" + student1.getName());      //student1.name is 9924078
```

### 四、使用原型创建对象
可以通过给对象的原型赋值方式来扩充对象属性和方法，并解决共享问题。例如：
```javascript
function Student() { }
Student.prototype.id = "9924077";
Student.prototype.getId = function() { return this.id; };
var student1 = new Student();
student1.getId();
```
**特点：**
1. 随时可以通过原型方式来扩充既定类型，并即时影响该类型的所有对象实例；
2. 用构造函数生成的对象才能用原型方式扩充；
3. 可以在对象实例中定义同名属性覆盖原型中定义的属性，如：student1.id = “9924078”。

**存在的问题：**
1. 灵活性不足：无法像构造函数一样通过传参来实现不同实例；
2. 多个对象实例需逐个覆盖原型属性，代码冗长；
3. 多个对象实例共享了同一个引用类型属性，例如：
```javascript
Student.prototype.sclass = [ "web", "ICS" ];
var student1 = new Student();
var student2 = new Student();
student1.sclass.push("DS");
student2.sclass.push("Java");
document.write("student1.sclass is " + student1.sclass + "<br>"); //student1.sclass is web,ICS,DS,Java
document.write("student2.sclass is " + student2.sclass + "<br>"); //student2.sclass is web,ICS,DS,Java
```

### 五、组合使用构造函数和原型创建对象
通过构造函数方式定义各实例不同的属性，通过原型方式定义各实例共享的方法。这样还可通过传参生成不同对象，引用类型共享问题也不存在。如：
```javascript
function Student(id) {  this.id = id;  }
Student.prototype.getId = function() {  return this.id;  };
var student1 = new Student("9924077");
var student2 = new Student("9924078");
```
对此方法改进后的动态原型模式：
```javascript
function Student(id) {
    this.id = id;
    if (typeof this.getId != "function") {
        Student.prototype.getId = function() { return this.id; };
    }
}
var student1 = new Student("9924077");
var student2 = new Student("9924078");
```

###六、深入创建对象
new运算符时做了什么：
1. 创建原型对象，将其constructor属性指向函数对象（该操作只做一次）；
2. 将函数对象的prototype属性指向原型对象（该操作只做一次）；
3. 创建实例对象，将实例对象的_proto_属性指向原型对象，将this指针指向新建实例对象，返回this指针（如果构造函数不显式返回一个其他对象的话）。
![](/2013/06/24/Javascript-Object/20130623120524187.jpg)
关于赋值：
1. 构造函数中使用this赋值时，依据这些语句为实例对象添加属性和方法。
2. 直接对原型赋值时，依据这些语句为原型对象添加属性和方法。

### 七、深入理解原型
在使用原型方法创建对象时，为减少输入和加强封装，可以直接将对象字面量赋予原型，如：
```javascript
function Student() { }
Student.prototype = {
    id : "9924077",
    getId : function() { return this.id; }
};
var student1 = new Student();
student1.getId();
```
此时id是原型的属性。但是存在一些问题：
1. 但此时原型的constructor值丢失，所以需要手动加上：
```javascript
Student.prototype= {
constructor: Student,
id: "9924077",
getId: function() { return this.id; }
};
```
2. 字面量赋值前生成的实例对象与原型间关系被切断，但在所有实例生成前赋值则没有关系。例如：
```javascript
function Student() { }
varstudent1 = new Student();
Student.prototype= {
constructor: Student,
id: "9924077",
getId: function() { return this.id; }
};
student1.getId();// error
```
此时的对象关系图如下：
![](/2013/06/24/Javascript-Object/20130623120556921.jpg)
系统对对象属性和方法访问的判断：
1. 首先访问实例，看实例中是否有该属性；
2. 如果没有，则通过_proto_查看原型中是否有；
3. 如果再没有，查看原型的父类型的原型（涉及继承的原型链的问题）；
4. 只有访问对象时才会查找原型，赋值和删除时不查找原型；
5. 添加同名属性后会覆盖原型中属性，删除实例属性后可能会暴露出原型中同名属性；
6. 字面量方式定义的对象无法使用原型，因为没有使用new运算符的过程。

###八、模拟类方法和类属性
类方法和类属性是可以通过类名访问的，所有实例共享的方法和属性。相当于java中的static变量和方法。Javascript中可以通过构造函数对象实现，如：
```javascript
function Student() { }
Student.TEST = 1;
Student.GetTEST = function() { return Student.TEST; };
```