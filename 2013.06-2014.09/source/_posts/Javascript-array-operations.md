title: Javascript数组操作
date: 2014-07-11 13:58:16
categories: Javascript
tags:
---
### 1. forEach遍历数组
```javascript
var arr = [1, 2, 3, 4, 5, 6];
arr.forEach(function(x) {
    console.log(x);
});
```

### 2. filter过滤数组
```javascript
var arr = [1, 2, 3, 4, 5, 6];
var newArr = arr.filter(function(x) {
    return x % 2 == 0;
});
console.log(newArr);
```

### 3. map对数组每一项执行函数，结果作为新数组返回
```javascript
var arr = [1, 2, 3, 4, 5, 6];
var newArr = arr.map(function(x) {
    return x * x;
});
console.log(newArr);
```
<!-- more -->
### 4. every检查是否数组的每一项都符合条件
```javascript
var arr = [1, 2, 3, 4, 5, 6];
var result = arr.every(function(x) {
    return x > 0;
});
console.log(result);
```

### 5. some检查数组中是否存在符合条件的元素
```javascript
var arr = [1, 2, 3, 4, 5, 6];
var result = arr.some(function(x) {
    return x > 5;
});
console.log(result);
```

### 6. indexOf和lastIndexOf

### 7. reduce
```javascript
var matrix = [
    [1, 2],
    [3, 4],
    [5, 6]
];
var flatten = matrix.reduce(function(previous, current) {
    return previous.concat(current);
});
console.log(flatten);
```

### 8. reduceRight
```javascript
var data = [1, 2, 3, 4];
var specialDiff = data.reduceRight(function(previous, current, index) {
    if (index == 0) {
        return previous + current;
    }
    return previous - current;
});
console.log(specialDiff);
```

### 参考文章
[avaScript Array “Extras” in Detail](http://dev.opera.com/articles/javascript-array-extras-in-detail/)