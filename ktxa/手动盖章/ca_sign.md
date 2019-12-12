

[下载本章节示例](../手动盖章/ktSeal.html)

[下载手动签章for_Chrome](../手动盖章/手动盖章_websocket.html)

[下载手动签章for_IE](../手动盖章/手动盖章_ocx.html)

[下载凯特信安手工盖章开发文档](../手动盖章/SecSeal签章控件集成开发手册.doc)

> 以上链接地址请通过"右键"，在新的tab页打开下载

> 凯特的Ocx如果调用失败的话，可以用管理员权限打开命令行工具，通过regsvr32注册ocx控件即可。一般来说，客户机安装了凯特驱动后就不用手动注册控件了。

```html
<!-- 必须拷贝 -->
<OBJECT id="EdcProxy" classid="clsid:831F5638-D9CD-4A7E-BB16-21606AEA7AE7" name="EdcProxy"></OBJECT>
<OBJECT width="1000" height="500" id="control" classid="clsid:5CAC43BD-DE74-49F9-9FD6-DDA6B5A15A9C"
        codebase="KTSEDocAxEx.ocx"></OBJECT>
```

```javascript
<script>
 // 必须拷贝，获取凯特Ocx控件生成的Edc base64串
 function ktGetEdcBase64() {
     //edc文件输出方式,0默认,1签名,2加密
     control.SetDocSignType(1);
     //保存时不弹出保存成功的提示
     control.DelSaveDlg(1);
     if (control.IsDealUnStamp()) {
         //文件保存在临时目录中，并随机命名
         // control.DealFileExport("D:\123.edc");       
         //对文件进行b64编码
         console.log(control.GetEDCB64());
     }
     else {
         alert("请先盖章后，在点输出！");
     }
 }
 // 必须拷贝，凯特Ocx控件加载pdf文档
 function ktLoadFile(filePath) {
     control.LoadPDFDocument(filePath);
 }
 // 必须拷贝，凯特Ocx控件及WebSocket下调用盖章的方法
 function ktSeal(filePath) {
     if (getBrowserName() != "IE") {
         let socket = createSocket(function (event) {
             var ktData = JSON.parse(event.data);
             console.log(ktData);
         });
         let request = {
             PluginRequestID: 1,
             PluginRequest: "Seal",
             InFile: filePath,
             OutFile: "000",
             PDFCommand: 13,
             SetEditPdf: false,
             SetSealToolVis: false,
             SetToolButtonVis: -2,
             SetDocSignType: 1,
             GetEDCB64: true,
             AutoCloseDocument: true,
             PutIsOpenVerifyCrl: false,
             PutIsPrint: true,
             PutIsExportBlack: true,
             PutIsCento: true
         };
         socket.onopen = function () {
             socket.send(JSON.stringify(request));
         }
     }
     else {
         if (control.IsDealStamp()) {
             control.DealStamp();
         }
         else {
             alert("不能进行盖章,请先导入PDF文件！");
         }
     }
 }
 // 必须拷贝,判断当前浏览器
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
 // 必须拷贝,创建一个WebSocket主要用于非IE系列浏览器
 function createSocket(fun) {
     if (getBrowserName() != "IE") {
         var webSocket = new WebSocket("ws://127.0.0.1:31213/");
         webSocket.onerror = function (event) { alert("连接错误"); };
         webSocket.onclose = function (event) { alert("服务不存在或者被关闭"); };
         webSocket.onmessage = function (event) {
             debugger
             if (fun && typeof fun == 'function')
                 fun(event)
         };
         return webSocket;
     }
 }
 </script>
```