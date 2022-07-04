# SpreadJS_CopyingDataAcrossWorkbooks
在纯前端在线表格中实现跨工作簿数据复制功能
# SpreadJS_CopyingDataAcrossWorkbooks

#### 介绍
在纯前端在线表格中实现跨工作簿数据复制功能

### SpreadJS 示例，跨工作簿数据复制
该示例包括使用 SpreadJS API 的演示脚本，可用于实现跨工作簿数据复制。
有关 SpreadJS API 的更多信息，请参阅[SpreadJS API指南]( https://demo.grapecity.com.cn/spreadjs/help/api/) 和[帮助手册]( https://help.grapecity.com.cn/pages/viewpage.action?pageId=5963808)。




### 运行步骤
1、在开始之前，请确保您已满足以下先决条件：
要运行 SpreadJS，浏览器必须支持 HTML5，客户端导入和导出 Excel 需要 IE10及以上。
请先了解 [SpreadJS 的产品使用环境]( https://www.grapecity.com.cn/developer/spreadjs/selection-guide/product-use-environment)，并申请临时部署授权激活
安装并更新NodeJS和NPM
2、克隆或下载此代码库
3、初始化控件，并运行示例脚本
#### 控件初始化
首先，创建一个新页面，并在页面上输入以下代码：
```
<!DOCTYPE html>
    <html>
    <head>
        <title>SpreadJS HTML Test Page</title>
```
2、在页面中添加对 SpreadJS 的引用。代码如下。需要注意的是，SpreadJS 提供压缩过
```
//（minified）的 JavaScript 文件和和用于调试的文件：
<script src="[Your_Scripts_Path]/gc.spread.sheets.all.xxxx.min.js" type="text/javascript"></script>
```
3、添加 CSS 文件以改变Spread.JS 的外观。默认的CSS文件名为： 
gc.spread.sheets.xxxx.css，里面包含了所有的默认样式。该 CSS 文件将会影响滚动条，筛选框及其子元素，单元格和下方标签栏的样式。引入 CSS 的代码如下：
```
//<link href="[Your_CSS_Path]/gc.spread.sheets.xxxx.css" rel="stylesheet" type="text/css"/>
```
4、添加产品授权，代码为（本地测试可以不添加）：
```
GC.Spread.Sheets.LicenseKey = "xxx";
```
5. 添加控件初始化代码。本例会在一个 id 为 “ss” 的 DOM 元素上初始化 SpreadJS：
```
<script type="text/javascript">
// Add your license
// If run this in local for testing, remove or comment below code
 GC.Spread.Sheets.LicenseKey = "xxx";

// Add your code
 window.onload = function(){
var spread = new GC.Spread.Sheets.Workbook(document.getElementById("ss"),{sheetCount:3});
var sheet = spread.getActiveSheet();
 }
</script>
</head>
<body>
```
6、 创建一个 id 为 “ss” 的元素，SpreadJS 将在该 DOM 中初始化：
```
<div id="ss" style="height: 500px; width: 800px"></div>
</body>
</html>
```
#### 示例代码
```
HTML：
<div id="log"></div>
<p class="title">复制导入内容到指定表单</p>

<div id='ss' class="sample-spreadsheets" style='float:left'></div>
<div id='ss1' class="sample-spreadsheets" style='float:left'></div>
<div class="options-container">
<div class="option-row">
    <div class="input-container">
        <input type="file" id="fileDemo" class="input">
        <div class="group">
            <label >文件密码:
                <input type="password" id="password" placeholder='加密文件需要输入提取密码'>
            </label>
        </div>
        <input type="button" id="loadExcel" value="上传至上方工作簿" class="button">
    </div>
</div>
<div class="option-row">
    <input type="button" id="copy" value="选中区域复制到下方工作簿" class="button" />
</div>
</div>


CSS：
*{
    margin: 0;
    padding: 0;
}

p.title{
    color: red;
    text-align: center;
}

.sample-spreadsheets {
    width: 100%;
    height: 300px;
}

.options-container {
    width: 220px;
    padding: 12px;
    box-sizing: border-box;
    background: #ddd;
    position: absolute;
    top: 60px;
    right: 30px;
    z-index: 20;
}

.input-container {
    width: 100%;
    border: 1px solid #e8e8e8;
    padding: 6px 12px;
}

.input-container .input {
    padding: 10px 12px;
}

.input-container .button {
    padding: 6px 12px;
    width: 160px;
    margin-top: 6px;
    border: none;
    border-radius: 4px;
    background-color: #609b61;
}

.group{
    font-size: 12px;
}

.group input {
    margin: 16px 0;
    padding: 4px 12px;
}

#copy{
    margin:12px;
    padding: 8px 14px;
    border: none;
    border-radius: 4px;
    background-color: #609b61;
}

JavaScript：
var spread = new GC.Spread.Sheets.Workbook(document.getElementById("ss"), {
    sheetCount: 2
});
var spread1 = new GC.Spread.Sheets.Workbook(document.getElementById("ss1"), {
    sheetCount: 2
});
var excelIo = new GC.Spread.Excel.IO();
$("#loadExcel").click(function() {
    var excelFile = document.getElementById("fileDemo").files[0];
    var password = $("#password").val();
    excelIo.open(excelFile, function(json) {
        spread.fromJSON(json);
    }, function(e) {
        alert(e.errorMessage);
        if (e.errorCode === 2 /*noPassword*/ || e.errorCode === 3 /*invalidPassword*/ ) {
            $("#password").select();
        }
    }, {
        password: password
    });
});

$("#copy").click(function() {
    var sheet = spread.getActiveSheet();
    var sheet1 = spread1.getActiveSheet();
    var fromSelections = sheet.getSelections()[0];
    for (var i = fromSelections.row; i < fromSelections.row + fromSelections.rowCount; i++) {
        for (var j = fromSelections.col; j < fromSelections.col + fromSelections.colCount; j++) {
            sheet1.suspendPaint();
            sheet1.setFormatter(i, j, sheet.getFormatter(i, j));
            sheet1.setValue(i, j, sheet.getValue(i, j));
            sheet1.setStyle(i, j, sheet.getActualStyle(i, j));
            sheet1.resumePaint();
        }
    }

});
```
#### 关于 SpreadJS
[SpreadJS]( https://www.grapecity.com.cn/developer/spreadjs) 是一款基于 HTML5 的纯前端表格控件，兼容 450 多种 Excel 公式，具备“高性能、跨平台、与 Excel 高度兼容”的产品特性。使用 SpreadJS，可直接在 Angular、 React、 Vue 等前端框架中实现高效的模板设计、在线编辑和数据绑定等功能，为最终用户提供高度类似 Excel 的使用体验。


