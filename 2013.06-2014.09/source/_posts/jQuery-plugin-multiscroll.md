title: jQuery插件multiscroll研究
date: 2014-09-14 13:00:32
categories: Javascript
tags:
---
最近发现了一个不错的多页面滚动插件 [multiscroll](https://github.com/alvarotrigo/multiscroll.js)，之前jQuery也没少用，但是都是一些常用的功能，对jQuery的理解并不算深刻，而且对于jQuery插件的开发也没有涉及过。因此借助这个插件，顺便了解一下jQuery的一些未曾接触过的API函数。

通过看源码可以得知整个multiscroll函数接受一个options的参数，然后是将options扩展到默认的参数选项，代码如下：
```javascript
options = $.extend({
	'verticalCentered' : true,
	'scrollingSpeed': 700,
	'easing': 'easeInQuart',
	'menu': false,
	// ... ...
}, options);
```
这里先来了解一下`$.extend`函数，可参考[官方API](http://api.jquery.com/jQuery.extend/)，因此这里第一个参数是定义的默认选项，传入的参数options是用户定义的选项，所以就是用用户的选项覆盖掉默认的选项。

<!-- more -->

接下来是一些变量的定义：
```javascript
var scrollDelay = 600;

var isTouch = (('ontouchstart' in window) || (navigator.msMaxTouchPoints > 0));
var numberSections = $('.ms-left').find('.ms-section').length;
var isMoving = false;
var nav;
var windowHeight = $(window).height();
```

然后是添加鼠标滚轮事件和触摸事件，其中鼠标滚轮事件如下：
```javascript
/**
* Adds the auto scrolling action for the mouse wheel and tackpad.
* After this function is called, the mousewheel and trackpad movements will scroll through sections
*/
function addMouseWheelHandler(){
	if (document.addEventListener) {
		document.addEventListener("mousewheel", MouseWheelHandler, false); //IE9, Chrome, Safari, Oper
		document.addEventListener("wheel", MouseWheelHandler, false); //Firefox
	} else {
		document.attachEvent("onmousewheel", MouseWheelHandler); //IE 6/7/8
	}
}

/**
 * Detecting mousewheel scrolling
 *
 * http://blogs.sitepointstatic.com/examples/tech/mouse-wheel/index.html
 * http://www.sitepoint.com/html5-javascript-mouse-wheel/
 */
function MouseWheelHandler(e) {
	// cross-browser wheel delta
	e = window.event || e;
	var delta = Math.max(-1, Math.min(1, (e.wheelDelta || -e.deltaY || -e.detail)));

	if (!isMoving) { //if theres any #

		//scrolling down?
		if (delta < 0) {
			$.fn.multiscroll.moveSectionDown();
		}

		//scrolling up?
		else {
			$.fn.multiscroll.moveSectionUp();
		}
	}


	return false;
}
```
源码注释里已经有了参考链接，由于e.detail是Firefox支持的，且当滚轮向下滚动时返回正值，因此需要加上负号。使用Math.min取保当滚轮向上滚动时返回1，向下滚动时返回一个负值，外面再加一层Math.max从而保证滚轮向上滚动时delta为1，滚轮向下滚动时delta值为-1，接下来根据delta值得正负从而向上或者向下滚动页面。

触摸事件如下：
```javascript
var touchStartY = 0;
var touchStartX = 0;
var touchEndY = 0;
var touchEndX = 0;

/**
 * Adds the possibility to auto scroll through sections on touch devices.
 */
function addTouchHandler() {
	if (isTouch) {
		$(document).off('touchstart MSPointerDown').on('touchstart MSPointerDown', touchStartHandler);
		$(document).off('touchmove MSPointerMove').on('touchmove MSPointerMove', touchMoveHandler);
	}
}

/**
 * Handler to get he coordinates of the starting touch
 */
function touchStartHandler(event) {
	var e = event.originalEvent;
	var touchEvents = getEventsPage(e);
	touchStartY = touchEvents['y'];
	touchStartX = touchEvents['x'];
}

/* Detecting touch events
 
 * As we are changing the top property of the page on scrolling, we can not use the traditional way to detect it.
 * This way, the touchstart and the touch moves shows an small difference between them which is the
 * used one to determine the direction.
 */
function touchMoveHandler(event) {
	var e = event.originalEvent;

	//preventing the easing on iOS devices
	event.preventDefault();

	var activeSection = $('.ms-left .ms-section.active');

	if (!isMoving) { //if theres any #
		var touchEvents = getEventsPage(e);
		touchEndY = touchEvents['y'];
		touchEndX = touchEvents['x'];


		//is the movement greater than the minimum resistance to scroll?
		if (Math.abs(touchStartY - touchEndY) > ($(window).height() / 100 * options.touchSensitivity)) {

			if (touchStartY > touchEndY) {
				$.fn.multiscroll.moveSectionDown();

			} else if (touchEndY > touchStartY) {
				$.fn.multiscroll.moveSectionUp();
			}
		}
	}
}
```
这里主要就是获取到touch事件的起始位置，然后当事件的移动距离大于敏感距离的话，根据起始位置的相对大小进行页面的上下移动。

然后是检测3d效果支持，这里注释里写的比较明白，不做赘述。接下来便是一些样式上的调整。然后便是：
```javascript
$('.ms-left .ms-section, .ms-right .ms-section').each(function() {
	var sectionIndex = $(this).index();

	if (options.paddingTop || options.paddingBottom) {
		$(this).css('padding', options.paddingTop + ' 0 ' + options.paddingBottom + ' 0');
	}

	if (typeof options.sectionsColor[sectionIndex] !== 'undefined') {
		$(this).css('background-color', options.sectionsColor[sectionIndex]);
	}

	if (typeof options.anchors[sectionIndex] !== 'undefined') {
		$(this).attr('data-anchor', options.anchors[sectionIndex]);
	}

	if (options.verticalCentered) {
		addTableClass($(this));
	}

	//only for the left panel
	if ($(this).closest('.ms-left').length && options.navigation) {
		var link = '';
		if (options.anchors.length) {
			link = options.anchors[sectionIndex];
		}
		var tooltip = options.navigationTooltips[sectionIndex];
		if (typeof tooltip === 'undefined') {
			tooltip = '';
		}
		if (options.navigation) {
			nav.find('ul').append('<li data-tooltip="' + tooltip + '"><a href="#' + link + '"><span></span></a></li>');
		}
	}
});
```
这里对于每一个左右ms-section元素设置padding、background-color以及data-anchor属性。然后添加导航列表。接下来将右侧页面顺序倒置：
```javascript
//inverting the right panel
$('.ms-right').html( $('.ms-right').find('.ms-section').get().reverse());
```
这样使得左右页面是错位的，滚动的时候左侧页面自下向上滚动，而右侧页面自上向下滚动，于是刚刚好能拼合在一起。接下来的是整个页面的一个位置初始化，初始化active的section等。然后就是一些事件的添加，例如hashchange、keydown、点击导航等。然后便是移动页面的函数了。