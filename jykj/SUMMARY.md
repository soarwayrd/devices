[点击签名](./点击签名/SignByClick.md)



> 以下的代码，在使用捷宇设备的过程中必须拷贝，一个页面有使用到捷宇设备，只需要拷贝一次。

```javascript
        // 必须拷贝
 function getBrowserName() {
     let userAgent = navigator.userAgent;
     if (userAgent.indexOf("Opera") > -1)
         return "Opera"
     else if (userAgent.indexOf("Firefox") > -1)
         return "FF";
     else if (userAgent.indexOf("Edge") > -1)
         return "Edge";
     else if (userAgent.indexOf("Chrome") > -1)
         return "Chrome";
     else if (userAgent.indexOf("Safari") > -1)
         return "Safari";
     else
         return "IE";
 }


 // 必须拷贝
 function CreateSocket() {
     if (getBrowserName() != "IE") {
         var webSocket = new WebSocket('ws://localhost:1919');
         // webSocket.onopen = function (event) { console.log("连接成功") };
         webSocket.onerror = function (event) { alert("连接错误"); };
         webSocket.onclose = function (event) { alert("服务不存在或者被关闭"); };
         webSocket.onmessage = function (event) { console.log(event) };
         return webSocket;
     }
 }
```