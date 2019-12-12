[下载本章节示例](../ktSeal.html)

```javascript

 // 必须拷贝,且一个页面只需拷贝一次
function ktSeal(filePath) {
    if (getBrowserName() != IE) {
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