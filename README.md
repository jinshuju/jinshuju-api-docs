# 金数据API文档

金数据API使用OAuth 2标准进行用户验证。


## 发送请求

所有的URL需要以`https://api.jinshuju.net/v4/`开头。仅支持SSL。目前API版本为`v4`版本。例如，想获得当前用户的基本信息情况：

    curl https://api.jinshuju.net/v4/me?access_token=...

## OAuth 2验证

v4版本的金数据API支持OAuth 2。你可以使用标准的OAuth交互协议进行访问。相关URL如下：

* 认证域: https://account.jinshuju.net
* 接口域: https://api.jinshuju.net/v4

### 1. 转向到金数据申请验证

    GET https://account.jinshuju.net/oauth/authorize
  
参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**。你注册的金数据应用ID。目前并未开放
redirect_uri  | string | **必须**。你应用的callback URI。当授权完成之后要转向的地址
scope  | string | 空格隔开的列表。目前支持的scope包括：`public`, `forms`
state | string | 唯一随机的的字符串。这是用来防止跨站共计的。

### 2. 金数据转向到你的地址

用户同意之后，金数据将会转向到你的网站，并带上`code`和之前提供的`state`参数。如果state不匹配，你可以终止这个请求。

拿到code之后，就可以交换access token: 

    POST https://account.jinshuju.net/oauth/access_token
    
参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**。你注册的金数据应用ID。目前并未开放
client_secret  | string | **必须**。你注册的金数据应用的secret。目前并未开放。
code  | string | **必须**。在第一步获得的code
redirect_uri  | string | **必须**。你应用的callback URI。当授权完成之后要转向的地址
state | string | 在第一步获得的唯一随机的的字符串

### Response

默认情况下，返回的response的形式如下：

    access_token=e72e16c7e42f292c6912e7710c838347ae178b4a&scope=user%2Cgist&token_type=bearer
    
### 3. 使用access token访问API

    GET https://api.jinshuju.net/v4/forms?access_token=...

你可以把token放在URL中。也可以使用Authorization header如下：

    Authorization: token OAUTH-TOKEN
  
例如使用curl
    
    curl -H "Authorization: token OAUTH-TOKEN" https://api.jinshuju.net/v4/forms
    
目前access_token有效期为7200秒。
    
## Redirect URL

`redirect_uri`是必须的。如果你使用[omniauth-jinshuju](https://github.com/jinshuju/omniauth-jinshuju)，就可以使用类似于`https://domain.com/auth/jinshuju/callback`的地址。

## Scopes

Scope定义了资源范围。目前支持两个：`public`和`forms`。

## Rate Limit

每个用户每小时调用120次。HTTP Header中会留下相应的信息。

    X-RateLimit-Limit:120
    X-RateLimit-Remaining:119
    
目前这个数值不可更改。

## API列表

### 获取表单列表

    GET https://api.jinshuju.net/v4/forms?access_token=...
    
```json
{
    "forms": [
        {
            "created_at": "2016-01-14T12:52:32.644Z",
            "description": null,
            "entries_count": 0,
            "id": "56979a103eec767979000000",
            "name": "文件夹内的表单",
            "setting": {
                "app_notification_enabled": true,
                "color": "#afa373",
                "icon": "fontello-chart-outline",
                "open_rule": "open",
                "permission": "password",
                "push_url": null,
                "result_state": "closed",
                "result_url": null,
                "search_state": "closed",
                "search_url": null
            },
            "shared": false,
            "token": "RygpW3"
        },
        {
            "created_at": "2016-01-14T10:46:47.168Z",
            "description": "表单介绍在这里。\n\n表单介绍在这里。",
            "entries_count": 1,
            "id": "56977c973eec76796a000008",
            "name": "第二个表单(很多字)",
            "setting": {
                "app_notification_enabled": true,
                "color": "#8d7ea8",
                "icon": "fontello-pencil",
                "open_rule": "open",
                "permission": "public",
                "push_url": null,
                "result_state": "closed",
                "result_url": null,
                "search_state": "closed",
                "search_url": null
            },
            "shared": false,
            "token": "0HYzQO"
        },
        {
            "created_at": "2015-11-19T02:18:12.044Z",
            "description": null,
            "entries_count": 0,
            "id": "564d31643eec76528e000002",
            "name": "\u6a21\u72481",
            "setting": {
                "app_notification_enabled": true,
                "color": "#49afcb",
                "icon": "fontello-graduation-cap",
                "open_rule": "open",
                "permission": "public",
                "push_url": null,
                "result_state": "closed",
                "result_url": null,
                "search_state": "closed",
                "search_url": null
            },
            "shared": false,
            "token": "isE6zh"
        }
    ],
    "meta": {
        "per_page": 20,
        "total": 3
    }
}

```

### 获取表单详情

    GET https://api.jinshuju.net/v4/forms/RygpW3?access_token=...

```json
{
    "created_at": "2016-01-14T10:46:47.168Z",
    "description": "表单描述",
    "entries_count": 1,
    "fields": [
        {
            "api_code": "field_1",
            "label": null,
            "notes": "",
            "type": "page_break"
        },
        {
            "api_code": "field_2",
            "label": "\u59d3\u540d",
            "notes": "",
            "predefined_value": null,
            "private": false,
            "type": "single_line_text",
            "validations": {}
        },
        
        ...
 
    ],
    "id": "56977c973eec76796a000008",
    "name": "表单名称",
    "setting": {
        "app_notification_enabled": true,
        "color": "#8d7ea8",
        "icon": "fontello-pencil",
        "open_rule": "open",
        "permission": "public",
        "push_url": null,
        "result_state": "closed",
        "result_url": null,
        "search_state": "closed",
        "search_url": null
    },
    "shared": false,
    "token": "0HYzQO"
}

```

### 获取表单当前状态

    GET https://api.jinshuju.net/v4/forms/RygpW3/status

```json
{
  "is_open": true,
  "permission": "public",
  "entries_count": 1
}
```

### 获取单条数据

    POST https://api.jinshuju.net/v4/forms/RygpW3/entries/<序列号>?access_code=...
    
JSON Load:

```json
{
  "field1": "对应的数据结果"
  ...
}
```