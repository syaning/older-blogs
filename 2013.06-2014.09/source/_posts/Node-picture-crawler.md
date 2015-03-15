title: Node抓取图片
date: 2014-08-18 20:31:09
categories: Node
tags:
---
之前用Python写过一个抓取豆瓣妹子的爬虫，最近在看Node，索性就用Node重新写了一个。代码如下：
```javascript
var http = require("http");
var fs = require("fs");

var baseURL = "http://dbmeizi.com/?p=";
var reg = /http:\/\/pic.dbmeizi.com\/pics\/\d+\/p\d+.jpg/gi;
var baseDir = "dbmeizi/";

function crawImages(startPage, endPage) {
	for (var i = startPage; i <= endPage; i++) {
		var pageURL = baseURL + i;

		http.get(pageURL, function(response) {
			var html = [];

			response.on("data", function(chunk) {
				html.push(chunk);
			});

			response.on("end", function() {
				html = Buffer.concat(html).toString();

				var imgURLs = html.match(reg);
				imgURLs.forEach(getImage);
			});
		});
	}
}

function getImage(imgURL) {
	http.get(imgURL, function(response) {
		var imgName = baseDir + imgURL.substring(37);
		var image = fs.createWriteStream(imgName);

		response.on("data", function(chunk) {
			image.write(chunk);
		});

		response.on("end", function() {
			image.end();
			console.log(imgName);
		});
	});
}

crawImages(0, 10);
```
<!-- more -->
首先我们需要引入`http`和`fs`模块。设定好起始页数和终止页数之后，对于每一页，发出http请求，当`end`事件触发时，即这一页的内容获取完之后，进行正则表达式匹配，获取图片地址，然后对于每一个图片进行下载。