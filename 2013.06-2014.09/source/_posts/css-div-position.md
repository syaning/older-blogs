title: css+div定位分析
date: 2014-04-08 15:54:08
categories: CSS
tags:
---
css+div设计中，position属性可以取值：static、fixed、relative和absolute，它们的区别如下：
1. static: 默认值。没有定位，元素出现在正常的流中（忽略 top, bottom, left, right 或者 z-index 声明）
2. fixed: 生成绝对定位的元素，相对于浏览器窗口进行定位。元素的位置通过 “left”, “top”, “right” 以及 “bottom” 属性进行规定。可通过z-index进行层次分级。
3. relative: 生成相对定位的元素，通过top,bottom,left,right的设置相对于其正常位置进行定位。可通过z-index进行层次分级。
4. absolute: 生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。元素的位置通过 “left”, “top”, “right” 以及 “bottom” 属性进行规定。可通过z-index进行层次分级。

<!-- more -->
定位为relative的元素脱离正常的文本流中，但其在文本流中的位置依然存在，如图：
![](/2014/04/08/css-div-position/css_div_1.jpg)
定位为absolute的层脱离正常文本流，但与relative的区别是其在正常流中的位置不在存在，如图：
![](/2014/04/08/css-div-position/css_div_2.jpg)
relative定位的层总是相对于其最近的父元素，无论其父元素是何种定位方式，如图：
![](/2014/04/08/css-div-position/css_div_3.jpg)
图中，红色背景层为relative定位，其直接父元素绿色背景层为默认的static定位。红色背景层的位置为相对绿色背景层top、left为20元素。而如果红色背景层定位为absolute，如图：
![](/2014/04/08/css-div-position/css_div_4.jpg)
可以看到，红色背景层依然定义top:20px；left:20px；但其相对的元素变为定位方式为absolute或relative的黄色背景层。因此，对于absolute定位的层总是相对于其最近的定义为absolute或relative的父层，而这个父层并不一定是其直接父层。如果其父层中都未定义absolute或relative，则其将相对body进行定位，如图：
![](/2014/04/08/css-div-position/css_div_5.jpg)
除top、left、right、bottom定位外，margin属性值的定义也符合上述规则。

### 参考文章
[CSS+DIV定位分析(relative,absolute,static,fixed)](http://blog.163.com/love_heartbreaking/blog/static/124561901201211334714800/)
[css中position属性(absolute|relative|static|fixed)概述及应用](http://www.jb51.net/web/77495.html)