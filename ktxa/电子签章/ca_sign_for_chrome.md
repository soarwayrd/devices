[下载本章节示例](../ktSeal.html)

[下载手动签章for_Chrome](./手动盖章(websocket).html)

[下载手动签章for_IE](./手动盖章(ocx).html)

[下载凯特信安手工盖章开发文档](./SecSeal签章控件集成开发手册.doc)

```javascript
 // 必须拷贝
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
 // 必须拷贝
 function ktLoadFile(filePath) {
     control.LoadPDFDocument(filePath);
 }
 // 必须拷贝,且一个页面只需拷贝一次
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
 // 必须拷贝,且一个页面只需拷贝一次
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
 // 必须拷贝,且一个页面只需拷贝一次
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
```