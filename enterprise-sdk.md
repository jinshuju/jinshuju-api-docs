# 金数据SDK文档（企业版）

金数据提供SDK可对表单进行创建和编辑操作。

## SDK应用场景列举

### 1 将金数据创建和编辑表单功能无缝接入企业当前系统
如果企业已有Web站点，可将金数据SDK嵌入到企业页面中。 通过在你的企业系统中点击按钮等事件触发新建表单或编辑已在金数据中已存在的表单。SDK提供的新建和编辑表单页面中不会显示金数据的账号信息和退出金数据登录按钮，并且新建的表单以及表单产生的数据依然存储在金数据中，你可以随时使用。


### 2 静默登录和注册金数据系统
如果企业中已拥有用户体系，金数据可以根据SDK请求中携带的企业用户ID自动登陆系统，静默登陆后可直接进行创建和编辑表单的操作。如果企业用户ID并未在金数据中注册，系统会静默注册金数据账号，然后直接创建和编辑表单。


### 3 灵活控制SDK可使用的字段范围
企业中也可以灵活控制SDK中创建和编辑表单可使用的字段范围。金数据企业版提供了28个表单字段。当企业调用SDK时，可定义SDK中不可使用的字段，让不同的企业用户可以有不同的字段权限。



## SDK使用指南

### 1. 向金数据申请初始化SDK

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

### 2. 创建新表单

你可以在数据属性中使用`data-role="gd-sdk"`，或直接调用`gdsdk.createForm()`方法来创建一个新表单。示例如下：

```html
<button data-role="gd-sdk">创建新表单</button>
```
  
```javascript
gdsdk.createForm()
```


### 3. 编辑表单

你可以在数据属性中使用`data-role="gd-sdk"`和`data-form="<form_token>"`，或直接调用`gdsdk.editForm("<form_token>")`方法来编辑表单。示例如下：

```html
<button data-role="gd-sdk" data-form="Dv6jVf">修改</button>
```
```javascript
gdsdk.editForm("<form_token>")
```


### 4. 保存表单

你可以将保存表单按钮的事件在gdsdk.ready中进行绑定。示例如下：

```javascript
gdsdk.ready = function() {
  gdsdk.events.onFormSaved = function(data, sdkWindow) {
    var formJSON = data.form; // saved form json
    sdkWindow.close(); // close sdk window
    // specify your code to handle the event
  };
};
```

### 5. 控制表单显示和隐藏的字段

你可以配置在引用的SDK中显示和隐藏的字段列表。配置后的字段将不出现在新建和编辑的表单中。如only和except同时使用，系统会取两者交集显示在SDK中。示例如下：

```javascript
gdsdk.ready = function() {
  gdsdk.config({
    fields: {only: ['single_line_text', 'goods', 'cascade_drop_down'],
             except: ['single_line_text', 'paragraph_text', 'single_choice']}
 });
};
```

可配置的字段列表如下：

```json
["single_line_text", "paragraph_text", "single_choice", "multiple_choice", 
"likert", "matrix", "number", "time", "date", "drop_down", "section_break", 
"page_break", "link", "rating", "cascade_drop_down", "attachment", 
"form_association", "formula", "mobile", "email", "address", 
"geo", "phone", "goods", "table"]
```

### 6. 控制SDK打开窗口在浏览器新tab中还是新窗口

你可以配置在引用的SDK中打开窗口在浏览器新tab中还是新窗口。示例如下：

```javascript
gdsdk.ready = function() {
  gdsdk.config({
      open: 'tab'
  });
};
```

可配置的打开窗口方式如下：

```
'tab', 'window'
```

### 7. 支持JWT登录和静默注册

SDK中支持JWT登录用户和静默注册用户。
JWT中需包含请求登录用户的uid。如请求登录的用户没有注册，则会在金数据系统中静默注册。注册用户用户名为：`User_<JWT中的uid>`邮箱为：`<JWT中的uid>@fake.<Subdomain>.com`；如请求登录的用户已经注册，则会直接登录金数据系统。
示例如下：

```html
<button data-role="gd-sdk" data-jwt="<jwt>" data-form="Dv6jVf">修改</button>
```
```javascript
gdsdk.createForm("<jwt>")
gdsdk.editForm("<form_token>", "<jwt>")
```

### 8. 支持JWT退出登录

SDK中支持JWT退出登录用户。JWT中需包含请求退出登录用户的uid，并且可以选择填写退出登录之后的回调函数。
示例如下：

```javascript
gdsdk.logout("<jwt>", function() {
  location.href = '/home';
})
```


