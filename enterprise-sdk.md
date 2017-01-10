# 金数据SDK文档（企业版）

金数据提供SDK可对表单进行创建和编辑操作。

## 1. 向金数据申请初始化SDK

请联系企业版客服申请初始化SDK的脚本。企业版客服将在一个工作日内回复申请。初始化脚本示例如下：

````
<script type='text/javascript'>
  (function (w, d, t, s, n) {
    w[n] = w[n] || function () { (w[n].q = w[n].q || []).push(arguments); };
    var a, x = d.getElementsByTagName(t)[0];
    if (d.getElementById(n)) {return;}
    a = document.createElement(t); a.id = n; a.type = 'text/javascript'; a.async = true; a.src = s;
    x.parentNode.insertBefore(a, x);
  })(window, document, 'script', 'https://www.jinshuju.com/sdk.js?appid=<your_sdk_appid>&v=1.0.0', 'gdsdk');
<script>
````

## 2. 创建新表单

你可以在数据属性中使用`data-role="gd-sdk"`，或直接调用`gdsdk.createForm()`方法来创建一个新表单。示例如下：

````
HTML
<button data-role="gd-sdk">创建新表单</button>
````
````
JavaScript
gdsdk.createForm()
````


## 3. 编辑表单

你可以在数据属性中使用`data-role="gd-sdk"`和`data-form="<form_token>"`，或直接调用`gdsdk.editForm("<form_token>")`方法来创建一个新表单。示例如下：

````
HTML:
<button data-role="gd-sdk" data-form="Dv6jVf">修改</button>
````
````
JavaScript
gdsdk.editForm("<form_token>")
````


## 4. 保存表单

你可以将保存表单按钮的事件在gdsdk.ready中进行绑定。示例如下：

````
gdsdk.ready = function() {
  gdsdk.events.onFormSaved = function(data, sdkWindow) {
    var formJSON = data.form; # saved form json
    sdkWindow.close(); # close sdk window
    # specify your code to handle the event
  };
};
````
