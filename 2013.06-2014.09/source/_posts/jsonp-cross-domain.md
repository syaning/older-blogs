title: jsonp跨域访问
date: 2014-05-20 14:03:32
categories: Javascript
tags:
---
在介绍jsonp之前，需要先了解一下[同源策略](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)。同源指的是主机、端口和协议都相同。同源策略阻止从一个源加载的文档或脚本获取或设置另一个源加载的文档的属性。下面是一个简单示例：
```javascript
$(function() {
    $.ajax({
        url: 'http://weather.yahooapis.com/forecastrss',
        type: 'GET',
        dataType: 'xml',
        data: {
            w: '2151849',
            u: 'c'
        },
        success: function() {
            console.log('success');
        },
        error: function() {
            console.log('fail');
        }
    });
});
```
这里是向Yahoo天气发送一个ajax请求来获取上海的天气数据，由于同源策略，会提示如下错误：
> XMLHttpRequest cannot load http://weather.yahooapis.com/forecastrss?w=2151849&u=c. No ‘Access-Control-Allow-Origin’ header is present on the requested resource. Origin ‘null’ is therefore not allowed access.

<!-- more -->
而jsonp就是用来解决跨域调用的问题的。首先我们会发现，诸如script、img等标签，当其src属性值与当前域不同源时，依然可以正常使用，也就是说，它们是可以跨域的。但是假如server端将数据封装到js格式的文件中，供client调用，那么我们就可以在client端获取到server端的数据了。由于json文件被js天然支持，并且是一种轻量级的数据格式，很适合在这种情况下使用。所以一般的思路就是服务器端动态生成json文件供客户端调用，于是逐渐形成了jsonp协议，该协议要求客户端向服务器端传第一个callback参数，服务器端会将json格式的数据作为该参数表示的函数的参数，从而返回一段js代码，再由客户端执行这段代码，从而获取到服务器端的数据。

jQuery对jsonp实现了很好的封装，下面是一个简单示例：
```javascript
var baseURL = 'http://lab.syaningv.com/seather/';
$.ajax({
        type : 'GET',
        url : baseURL + 'forecast.jsp',
        data : {
            cityID : param.cityID
        },
        dataType : 'jsonp',
        jsonp : 'callback',
        jsonpCallback : 'forecastHandler',
        success : function(data) {
            showForecast(data, param);
        }
    });
```
服务器端代码为：
```javascript
try{
    String callback = request.getParameter("callback");
    String cityID = request.getParameter("cityID");
    String str = new YahooForecastor(cityID).forecast();
    response.getWriter().print(callback + "(" + str + ")");
    response.getWriter().close();
}catch(Exception e){
    response.sendRedirect("index.html");
    return;
}
```
这里实现了一个基于Yahoo天气的js插件，服务器端使用jsp，获取到天气xml数据并进行解析，将需要的数据通过 new YahooForecastor(cityID).forecast() 封装成json格式，然后返回动态生成的js代码。这里并没有看到forecastHandler的函数定义，是因为jQuery会负责生成该函数，即success所绑定的函数。

### 参考文章
[JavaScript 的同源策略](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)
[说说JSON和JSONP，也许你会豁然开朗，含jQuery用例](http://www.cnblogs.com/dowinning/archive/2012/04/19/json-jsonp-jquery.html)
[jsonp详解](http://www.cnblogs.com/yuzhongwusan/archive/2012/12/11/2812849.html)