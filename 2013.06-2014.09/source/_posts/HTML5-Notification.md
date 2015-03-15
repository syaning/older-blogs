title: HTML5之Notification
date: 2014-02-28 12:10:25
categories: HTML5
tags:
---
HTML5中的桌面提醒（Web Notifications）能够让用户得到实时的消息提醒，在网页多窗口聊天的时候，这一功能将极大的方便用户。之前浏览器是一个严格的沙盒工作模式，这种模式把浏览器和桌面的通信隔离开，但是Notification的出现，可以跨越沙盒通过桌面向用户发出浏览器的通知。先来看示例代码与效果：
```html
<!DOCTYPE html>  
<html>  
<head>  
<meta charset="UTF-8" />  
<title>Desktop Notification</title>  
</head>  
<body>  
  <input type="button" value="show notification" onClick="showPermission();">  
  <script>
    function showPermission() {
      if (!("Notification" in window)) {
        alert("This browser does not support desktop notification");
      } else {
        Notification.requestPermission(function(permission) {
          // This allows to use Notification.permission with Chrome/Safari
          if (Notification.permission !== permission) {
            Notification.permission = permission;
          }
 
          if (permission === "granted") {
            var notification = new Notification("notification title", {
              dir: "auto",
              lang: "hi",
              tag: "testTag",
              icon: "http://www.html5party.com/bbs/static/image/common/logo.png",
              body: "notification body"
            });
          } else {
            alert("notification failed.");
          }
        })
      }
    }  
  </script>
</body>
</html>
```
<!-- more -->
![](/2014/02/28/HTML5-Notification/2014-02-28_161103.png)
首先是先检测浏览器是否支持notification，如果支持的话，Notification.requestPermission()用于向用户请求获得消息提醒的权限，返回值为”granted”（同意），”default”（默认，无动作），”denied”（拒绝）。若权限为同意，则构造Notification对象通知用户。至于Notification在这里不再赘述，详情请参阅推荐阅读。

### 参考文章
[Using Web Notifications](https://developer.mozilla.org/en-US/docs/WebAPI/Using_Web_Notifications)
[Notification](https://developer.mozilla.org/en-US/docs/Web/API/Notification)