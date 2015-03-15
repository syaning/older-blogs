title: CSS居中定位
date: 2014-08-04 22:43:13
categories: CSS
tags:
---
在CSS+DIV设计中，经常会遇到组件居中，在不同的情况下可能会需要不同的样式，下面根据不同情况详细进行分析。

### 固定宽度元素水平居中
对于固定宽度的元素，水平居中有以下方式：
#### 1. 通过设定左右margin为auto来居中
```css
.test{
	width: 300px;
	margin: 0 auto;

	height: 30px;
	background: red;
}
```
这种方法尤其要注意需要是定宽，否则无法实现居中。
#### 2. 通过left和margin-left来居中(或者使用right和margin-right)
```css
.test{
	width: 300px;
	left: 50%;
	margin-left: -150px;

	height: 30px;
	background: red;
	position: absolute;
}
```
<!-- more -->
### 非固定宽度元素水平居中
```css
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Examples</title>
<style type="text/css">
.outer{
	position: absolute;
	left: 50%;

	background: red;
}
.inner{
	position: relative;
	right: 50%;

	background: green;
}
</style>
</head>
<body>
	<div class="outer">
		<div class="inner">hello world</div>
	</div>
</body>
</html>
```
这种方法是在需要水平居中的元素外面套了一层div，然后外层div定位到50%的位置，内层div通过位置向左偏移50%即可居中，但此时inner的原始位置依然占用着，因为定位是relative，并未脱离文档流。

### 固定高度垂直居中布局
可以通过绝对布局和margin来实现，例如：
```css
.test {
	width: 300px;
	height: 300px;
	position: absolute;
	top: 50%;
	left: 50%;
	margin-left: -150px;
	margin-top: -150px;
	background: red;
}
```

### 非固定高度垂直居中布局
#### 1. 使用flex布局
```css
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Examples</title>
<style type="text/css">
.outer {
  display: -webkit-flex;
  display: flex;
  height: 300px;

  background: red;
}
.inner {
  margin: auto;

  background: green;
}
</style>
</head>
<body>
	<div class="outer">
		<div class="inner">hello world</div>
	</div>
</body>
</html>
```
应当注意的是，目前flex的兼容性会存在比较多的问题。

### 参考文章
[学习CSS布局-flexbox](http://nodejs.in/csslayoutsite/dev/flexbox.html)