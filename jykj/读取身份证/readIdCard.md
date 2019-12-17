[点击下载本页Demo](./read_id_card.html)

> 以上链接地址请通过"右键"，在新的tab页打开下载

```html
<!-- 这行代码需要拷贝 放在页面的head里 -->
<object classid="clsid:96BB8ADA-D348-4414-8892-DC79C8818841" id="GWQ" width="0" height="0"></object>
```

```html
<!-- html标签 -->
<button type="button" onclick="GetIdCardInfo()">读取身份证</button>
```

```html
<script>
    // 获取身份证信息
    function GetIdCardInfo() {
        if (getBrowserName() == "IE") {
            var result = GWQ.DoGWQ_ReadID();
            OnAfterCall(result);
        }
        else {
            let socket = CreateSocket();
            let idCardReq = {};
            idCardReq.type = 4;
            socket.onopen = function () {
                socket.send(JSON.stringify(idCardReq));
            }
        }
    }
    // 获取身份证信息 回调
    function OnAfterGWQ_ReadID(ErrorCode, JsonData) {
        if (ErrorCode == 0) {
            var obj = JSON.parse(JsonData);
            alert(JsonData);
        }
        else if (ErrorCode == -9) {
            alert("取消");
        }
        else {
            alert("失败，返回错误码为" + ErrorCode);
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
</script>
```

```html
 <!-- 这行代码需要拷贝 放在页面的body里 -->
<script language="javascript" for="GWQ" event="OnAfterGWQ_ReadID(ErrorCode,JsonData)" type="text/javascript">
    OnAfterGWQ_ReadID(ErrorCode, JsonData);
</script>
```