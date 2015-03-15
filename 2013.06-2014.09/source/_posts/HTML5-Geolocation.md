title: HTML5之Geolocation
date: 2014-02-26 11:41:30
categories: HTML5
tags:
---
HTML5提供了Geolocation API，用来确定和分享地理位置，API返回经度和纬度坐标——这是企业可用来在这一坐标附近的区域提供服务的信息，这类服务通常被称作基于位置的服务(location-based service, LBS)。事实上，对于用户地理位置的探测在互联网上早已经有了很多案例，其中一个最常见的应用就是当在地址栏输入google.com并按下回车键的时候，google并没有把我们带向刚才输入的地址，而是转向了google在我们所处地区的页面，比如大陆和香港用户会到达google.com.hk.

Geolocation API基于navigator这一全局对象的一个新属性：navigator.geolocation，navigator对象提供了一些关于访问者的浏览器和系统的有用信息。Geolocation可以确定使用了IP地址、基于web的数据库、无线网络连接，以及三角定位或是GPS技术的访问者的经度和纬度。应该要注意的一点是，由Geolocation提供的信息的准确性会基于获取信息的手段而发生变化。偶然情况下，在一些位置上，你有可能不能获得明确的地理位置读数或是一点数据都接收不到。脚本可使用navigator.geolocation对象来确定与用户的宿主设备相关的位置信息，在检索到位置信息之后，一个位置对象就会被创建出来，并使用这些数据做填充。navigator.geolocation对象有三个方法：
- getCurrentPosition()
- watchPosition()
- clearWatch()
<!-- more -->
### 1. 获取当前地理位置getCurrentPosition()
当获得用户的许可过后，我们就可以通过 geolocation 下的 getCurrentPosition 方法来获取用户的信息，这是geolocation一个非常重要的方法。getCurrentPosition()方法检索用户的当前位置，但只检索一次。当该方法被脚本调用时，方法以异步的方式来尝试获取宿主设备的当前位置。该方法最多可以有三个参数：
- geolocationSuccess：带回当前位置的回调（必需的）
- geolocationError. 有错误发生时使用的回调（可选的）
- geolocationOptions. 地理位置选项（可选的）

navigator.geolocation.getCurrentPositon()方法使用一个Position对象作为参数来把宿主设备的当前位置返回给geolocationSuccess这一回调，如果有错误发生的话，geolocationError回调会使用一个PositionError对象来做调用。至于geolocationOptions，有三个可选属性：enableHighAccuracy（true启用高精确度，默认false）、timeout（超时时段，即位置应该被返回的最长等待时间）和maximumAge（最大的时间数，缓存的位置在这一时间段内可被使用）。示例代码如下：
```html
<!DOCTYPE HTML>
<html>
<head>
<meta charset="utf-8">
<title>document</title>
<script type="text/javascript" src="http://api.map.baidu.com/api?v=1.3"></script>
<script type="text/javascript">
    function getLocation() {
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(showMap, handleError, {
                enableHighAccuracy: true,
                maximumAge: 1000
            });
        } else {
            alert("Your browser does not support HTML5 geolocation API!");
        }
    }
 
    function showMap(value) {
        var longitude = value.coords.longitude;
        var latitude = value.coords.latitude;
        var map = new BMap.Map("map");
        var point = new BMap.Point(longitude, latitude);
        map.centerAndZoom(point, 15);
        var marker = new BMap.Marker(new BMap.Point(longitude, latitude));
        map.addOverlay(marker); 
    }
 
    function handleError(value) {
        switch (value.code) {
            case value.PERMISSION_DENIED:
                alert("Geolocation service is denied.");
                break;
            case value.POSITION_UNAVAILABLE:
                alert("Unknown location.");
                break;
            case value.TIMEOUT:
                alert("Time out.");
                break;
            case value.UNKNOWN_ERROR:
                alert("Unknown error.");
                break;
        }
    }
 
    function init() {
        getLocation();
    }
 
    window.onload = init;
</script>
</head>
<body>
    <div id="map" style="width:600px;height:400px;"></div>
</body>
</html>
```
效果如图：
![](/2014/02/26/HTML5-Geolocation/2014-02-27_215819.png)
### 2. 持续位置追踪watchPosition()
对于使用移动设备的用户来说，位置并不是固定的，watchPosition是一个专门用来处理这一情况的方法，watchPosition被调用后，浏览器会跟踪设备的位置，每一次位置的变化，watchPosition 中的代码都将会被执行。watchPosition()方法定期轮询用户的位置，查看用户的位置是否发生改变。其最多可带三个参数，与getCurrentPosition()类似。当watchPosition被调用时，其异步地启动一个查看过程，这一过程涉及了一个新的Position对象的获取和一个watchID的创建。如果这一获取操作是成功的，则相关的使用一个Position对象作为参数的geolocationSuccess就会被调用。在失败时涉及的操作则是使用一个非空的geolocationError参数来调用该方法，watchPosition方法使用一个PositionError对象作为参数来生成geolocationError。当设备的位置发生改变时，一个合适的带有新的Position对象的回调就会被调用。
### 3. 清除位置追踪clearWatch()
clearWatch()方法终止正在进行的watchPosition()，该方法只能带一个参数。在调用时，其找到之前已经开始了的watchID参数并立即停止它。调用方法为navigator.geolocation.clearWatch(watchID) 。
### 4. Geolocation数据：Position对象
Geolocation API返回一个地理上的Position对象，该对象有两个属性：timestamp和coords。timestamp属性表示地理位置数据的创建时间，coords属性又包含七个属性：
- coords.latitude：估计纬度
- coords.longitude：估计经度
- coords.altitude：估计高度
- coords.accuracy：所提供的以米为单位的经度和纬度估计的精确度
- coords.altitudeAccuracy：所提供的以米为单位的高度估计的精确度
- coords.heading： 宿主设备当前移动的角度方向，相对于正北方向顺时针计算
- coords.speed：以米每秒为单位的设备的当前对地速度

### 参考文章
[Using geolocation](https://developer.mozilla.org/en-US/docs/WebAPI/Using_geolocation)