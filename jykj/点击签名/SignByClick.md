>一些alter或者console代码，可根据自己的实际情况删减。新建一个空白的html文件，将一下代码拷贝到文件内，即可运行。前提需安装好对应的驱动或服务。

```html
// html标签
<input type="button" value="电子签名(任意位置)" onclick="SignByClick('D:\\ca\\test.pdf', '0,1,450,500|1,1,450,500',2)" />
```

```javascript
<script>
    // 按需拷贝,将Base64保存到指定路径，生产环境上没什么用
    function Base64ToFile(Base64, Filename) {
            if (getBrowserName() == "IE") {
                GWQ.Base64ToFile(Base64, Filename);
            }
            else {
                let socket = CreateSocket(function (event) { });
                let file = {};
                file.type = 16;
                file.Base64 = Base64;
                file.Filename = Filename;
                socket.onopen = function () {
                    socket.send(JSON.stringify(file));
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
    function CreateSocket(fun) {
            if (getBrowserName() != "IE") {
                var webSocket = new WebSocket('ws://localhost:1919');
                // webSocket.onopen = function (event) { console.log("连接成功") };
                webSocket.onerror = function (event) { alert("连接错误"); };
                webSocket.onclose = function (event) { alert("服务不存在或者被关闭"); };
                webSocket.onmessage = function (event) {
                    if (fun && typeof fun == 'function')
                        fun(event)
                };
                return webSocket;
            }
        }
    
    // 必须拷贝,电子签名(任意位置)
    function SignByClick(pdfPath, targetLocation, signOption) {
            GWQ_SetSignOption(signOption);
            if (getBrowserName() == "IE") {
                var result = GWQ.DoGWQ_StartSign(pdfPath, "", targetLocation, "test", 60);
                OnAfterCall(result);
            }
            else {
                let socket = CreateSocket(function (event) {
                    let obj = JSON.parse(event.data);
                    if (obj.type == 3) {
                        if (obj.ret == 0) {
                            Base64ToFile(obj.SignPdfBase64, "D:\\SignPdfBase64.pdf");
                            Base64ToFile(obj.SignNameBase64, "D:\\SignNameBase64.png");
                            Base64ToFile(obj.FingerPrintBase64, "D:\\FingerPrintBase64.png");
                        }
                        else {
                        }
                    }
                });
                let caSignature = {};
                caSignature.type = 3;
                caSignature.PDFPath = pdfPath;
                caSignature.XmlPath = "";
                caSignature.Location = targetLocation;
                caSignature.Retain = "厂商预留字段，暂时无用";
                caSignature.Timeout = 60;
                socket.onopen = function () {
                    socket.send(JSON.stringify(caSignature));
                }
            }
        }
    // 必须拷贝,设置签名选项 0 只签名; 1 只指纹; 2 签名+指纹
    function GWQ_SetSignOption(signOption) {
            if (getBrowserName() == "IE") {
                GWQ.GWQ_SetSignOption(signOption);
            }
            else {
                let socket = CreateSocket();
                let option = {};
                option.type = 65;
                option.SignOption = parseInt(signOption);
                socket.onopen = function () {
                    socket.send(JSON.stringify(option));
                }
            }
        }
    // 必须拷贝,电子签名(任意位置) 回调
    function OnAfterGWQ_StartSign(ErrorCode, SignPdfBase64, SignNameBase64, FingerPrintBase64, MixBase64) {
            if (ErrorCode == 0) {
                GWQ.Base64ToFile(SignPdfBase64, "d:\\ca\\sp_ie.pdf");
                // 不确定人数
                // GWQ.Base64ToFile(JSON.parse(SignNameBase64).SignName0, "d:\\ca\\sn_ie.png");
                // GWQ.Base64ToFile(JSON.parse(FingerPrintBase64).FingerPrint0, "d:\\ca\\fp_ie.png");
                // GWQ.Base64ToFile(JSON.parse(MixBase64).XML0, "d:\\ca\\fp_ie.xml");
                // 确定人数
                // GWQ.Base64ToFile(SignNameBase64, "d:\\ca\\sn_ie.png");
                // GWQ.Base64ToFile(FingerPrintBase64, "d:\\ca\\fp_ie.png");
                alert("电子签名成功");
            }
            else if (ErrorCode == -9)
                alert("电子签名取消");
            else
                alert("失败，返回错误码为" + ErrorCode);
        }
</script>
```

```html
 <script language="javascript" for="GWQ" event="OnAfterGWQ_StartSign(ErrorCode,SignPdfBase64,SignNameBase64,FingerPrintBase64,XML)" type="text/javascript">
     OnAfterGWQ_StartSign(ErrorCode, SignPdfBase64, SignNameBase64, FingerPrintBase64, XML);
 </script>
```