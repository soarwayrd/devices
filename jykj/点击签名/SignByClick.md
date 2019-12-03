>一些alter或者console代码，可根据自己的实际情况删减

```html
<input type="button" value="电子签名(任意位置)" onclick="SignByClick('D:\\ca\\test.pdf', '0,1,450,500|1,1,450,500',2)" />
```

```javascript
 // 电子签名(任意位置)
 function SignByClick(pdfPath, targetLocation, signOption) {
     GWQ_SetSignOption(signOption);
     if (getBrowserName() == "IE") {
         var result = GWQ.DoGWQ_StartSign(pdfPath, "", targetLocation, "test", 60);
         OnAfterCall(result);
     }
     else {
         let socket = CreateSocket();
         let caSignature = {};
         caSignature.type = 3;
         caSignature.PDFPath = pdfPath;
         caSignature.XmlPath = desXmlPath;
         caSignature.Location = targetLocation;
         caSignature.Retain = "厂商预留字段，暂时无用";
         caSignature.Timeout = 60;
         socket.onopen = function () {
             socket.send(JSON.stringify(caSignature));
         }
     }
 }

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
```

```html
 <script language="javascript" for="GWQ" event="OnAfterGWQ_StartSign(ErrorCode,SignPdfBase64,SignNameBase64,FingerPrintBase64,XML)" type="text/javascript">
     OnAfterGWQ_StartSign(ErrorCode, SignPdfBase64, SignNameBase64, FingerPrintBase64, XML);
 </script>
```