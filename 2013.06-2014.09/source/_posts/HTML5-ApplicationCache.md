title: HTML5之ApplicationCache
date: 2014-03-01 12:22:04
categories: HTML5
tags:
---
HTML5 作为新一代的 HTML 标准，引入了应用程序缓存，增加了对离线功能的支持。这意味着web应用可进行缓存，并可在没有因特网连接时进行访问。ApplicationCache是一种简单的机制，它允许开发人员可以指定哪些文件应该被缓存给浏览器，并提供给离线用户。

在开发支持离线的 Web 应用程序时，开发者通常需要使用以下三个方面的功能：
1. 离线资源缓存：需要一种方式来指明应用程序离线工作时所需的资源文件。这样，浏览器才能在在线状态时，把这些文件缓存到本地。此后，当用户离线访问应用程序时，这些资源文件会自动加载，从而让用户正常使用。HTML5 中，通过
cache manifest 文件指明需要缓存的资源，并支持自动和手动两种缓存更新方式。
2. 在线状态检测：开发者需要知道浏览器是否在线，这样才能够针对在线或离线的状态，做出对应的处理。在 HTML5 中，提供了两种检测当前网络是否在线的方式。
3. 本地数据存储：离线时，需要能够把数据存储到本地，以便在线时同步到服务器上。为了满足不同的存储需求，HTML5 提供了 DOM Storage 和 Web SQL Database 两种存储机制。前者提供了易用的 key/value 对存储方式，而后者提供了基本的关系数据库存储功能。
<!-- more -->

### 离线资源缓存
为了能够让用户在离线状态下继续访问 Web 应用，开发者需要提供一个 cache manifest 文件。这个文件中列出了所有需要在离线状态下使用的资源，浏览器会把这些资源缓存到本地。
```html
<!-- clock.html -->
<!DOCTYPE HTML>
<html manifest="clock.manifest">
<head>
    <title>Clock</title>
    <script src="clock.js"></script>
    <link rel="stylesheet" href="clock.css">
</head>
<body>
    <p>The time is: <output id="clock"></output></p>
</body>
</html>
```

```css
/* clock.css */
output { font: 2em sans-serif; }
```

```javascript
/* clock.js */
setTimeout(function() {
    document.getElementById('clock').value = new Date();
}, 1000);
```

```
#clock.manifest
CACHE MANIFEST
clock.html
clock.css
clock.js
```
因为clock.html的body标签有了manifest属性，而manifest文件声明了需要缓存的文件，因此当离线访问clock.html时，依然可以正常显示。若没有声明manifest，则无法访问，因为此时浏览器并未缓存所需要的文件。

manifest文件的书写应当遵循以下格式：
1. 首行必须是 CACHE MANIFEST；
2. 其后，每一行列出一个需要缓存的资源文件名；
3. 可根据需要列出在线访问的白名单。白名单中的所有资源不会被缓存，在使用时将直接在线访问。声明白名单使用 NETWORK:标识符；
4. 如果在白名单后还要补充需要缓存的资源，可以使用 CACHE:标识符；
5. 如果要声明某 URI 不能访问时的替补 URI，可以使用 FALLBACK:标识符。其后的每一行包含两个 URI，当第一个 URI 不可访问时，浏览器将尝试使用第二个 URI；
6. 注释要另起一行，以 # 号开头。

例如：
```
CACHE MANIFEST
# 上一行是必须书写的。
 
images/sound-icon.png
images/background.png 
 
NETWORK:
comm.cgi
 
CACHE:
style/default.css 
 
FALLBACK:
/files/projects /projects
```

### 更新缓存
应用程序可以等待浏览器自动更新缓存，也可以使用 Javascript 接口手动触发更新。
1. 自动更新: 浏览器除了在第一次访问 Web 应用时缓存资源外，只会在 cache manifest 文件本身发生变化时更新缓存。而 cache manifest 中的资源文件发生变化并不会触发更新。
2. 手动更新: 开发者也可以使用 window.applicationCache 的接口更新缓存。方法是检测 window.applicationCache.status 的值，如果是 UPDATEREADY，那么可以调用 window.applicationCache.update() 更新缓存。如：

```javascript
if (window.applicationCache.status == window.applicationCache.UPDATEREADY) {
    window.applicationCache.update();
}
```

### 在线状态检测
HTML5 提供了两种检测是否在线的方式：navigator.online 和 online/offline 事件。
1. navigator.onLine: navigator.onLine 属性表示当前是否在线。如果为 true, 表示在线；如果为 false, 表示离线。当网络状态发生变化时，navigator.onLine 的值也随之变化。开发者可以通过读取它的值获取网络状态。
2. online/offline 事件: 当开发离线应用时，通过 navigator.onLine 获取网络状态通常是不够的。开发者还需要在网络状态发生变化时立刻得到通知，因此 HTML5 还提供了 online/offline 事件。当在线 / 离线状态切换时，online/offline 事件将触发在 body 元素上，并且沿着 document.body、document 和 window 的顺序冒泡。因此，开发者可以通过监听它们的 online/offline 事件来获悉网络状态。

### 参考文章
[使用 HTML5 开发离线应用](http://www.ibm.com/developerworks/cn/web/1011_guozb_html5off/)
[离线Web应用API](http://www.html5party.com/t/appcache-html5party.html)