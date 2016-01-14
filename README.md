# 金数据API文档

金数据API使用OAuth 2兼容的API请求方式。

## 发送请求

所有的URL需要以`https://api.jinshuju.net/v4/`开头。仅支持SSL。目前API版本为`v4`版本。例如，想获得当前用户的基本信息情况：

    curl https://api.jinshuju.net/v4/me?access_token=...

## OAuth 2验证

v4版本的金数据API支持OAuth 2。你可以使用标准的OAuth交互协议进行访问。相关URL如下：

```
oauth授权地址: https://api.jinshuju.net/oauth/authorize
获取Token地址: https://api.jinshuju.net/oauth/token
```

### 1. 转向到金数据申请验证

    GET https://github.com/login/oauth/authorize
  
参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**。你注册的金数据应用ID。目前并未开放
redirect_uri  | string | 你应用的callback URI。当授权完成之后要转向的地址
scope  | string | 空格隔开的列表。目前支持的scope包括：`public`, `forms`
state | string | 唯一随机的的字符串。这是用来防止跨站共计的。

### 2. 金数据转向到你的地址



