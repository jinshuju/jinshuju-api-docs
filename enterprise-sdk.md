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
HTML
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

## 5. 控制表单显示的字段

你可以配置在引用的SDK中不支持的字段列表。配置后的字段将不出现在新建和编辑的表单中。示例如下：

````
gdsdk.ready = function() {
  gdsdk.config({
    fields: {except: ['goods', 'formula']}
  });
};
````

可配置的不支持的字段列表如下：

````
["single_line_text", "paragraph_text", "single_choice", "multiple_choice", "likert", "matrix", "number", "time", "date", "drop_down", "section_break", "page_break", "link", "rating", "cascade_drop_down", "attachment", "form_association", "formula", "mobile", "email", "address", "geo", "phone", "goods"]
````

## 6. 支持JWT登录和静默注册

SDK中支持JWT登录用户和静默注册用户。
JWT中需包含请求登录用户的uid。如请求登录的用户没有注册，则会在金数据系统中静默注册。注册用户用户名为：`User_<JWT中的uid>`邮箱为：`<JWT中的uid>@fake.<Subdomain>.com`；如请求登录的用户已经注册，则会直接登录金数据系统。
示例如下：

````
HTML
<button data-role="gd-sdk" data-jwt="<jwt>" data-form="Dv6jVf">修改</button>
````
````
JavaScript
gdsdk.createForm("<jwt>")
gdsdk.editForm("<form_token>", "<jwt>")
````
