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
client_id  | string | **必须**，注册的金数据应用ID，目前并未开放注册。
redirect_uri  | string | **必须**，金数据应用的callback URI，当授权完成之后要转向的地址。
response_type | string | **必须**，OAuth 2中必须将其指定为`code`。
scope  | string | 空格隔开的列表。目前支持的scope包括：`public` `forms` `read_entries`，默认为public。
state | string | 唯一随机的的字符串，用来防止跨站攻击。

### 2. 金数据转向到你的地址

用户同意之后，金数据将会转向到你的网站，并带上`code`和之前提供的`state`参数。如果state不匹配，你可以终止这个请求。

拿到code之后，就可以交换access token:

    POST https://account.jinshuju.net/oauth/token

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**，注册的金数据应用ID，目前并未开放注册。
client_secret  | string | **必须**，金数据应用的secret，目前并未开放注册。
code  | string | **必须**，在第一步获得的code。
redirect_uri  | string | **必须**，金数据应用的callback URI，当授权完成之后要转向的地址。
grant_type | string | **必须**，指定为 `authorization_code`。 
state | string | 在第一步使用的唯一随机的的字符串。

默认情况下，返回的response的形式如下：

````json
{
    "access_token": "2994eec8c8b19c2a2103ae2a335dc781220bb701d4c2c7d1b4cc7c629353f8a4",
    "token_type": "bearer",
    "expires_in": 7200,
    "refresh_token": "a563ed398b919388bc2e87b29f8d3b6e42a1195cdc1d9e36c6e9bcaa153bc6d3",
    "scope": "public forms read_entries",
    "created_at": 1455680792
}
````

### 3. 使用access token访问API

    GET https://api.jinshuju.net/v4/forms?access_token=...

你可以把token放在URL中。也可以使用Authorization header如下：

    Authorization: bearer OAUTH-TOKEN

例如使用curl

    curl -H "Authorization: bearer OAUTH-TOKEN" https://api.jinshuju.net/v4/forms

目前access_token有效期为7200秒。

## Redirect URL

`redirect_uri`是必须的。如果你使用[omniauth-jinshuju](https://github.com/jinshuju/omniauth-jinshuju)，就可以使用类似于`https://domain.com/auth/jinshuju/callback`的地址。

## Scopes

Scope定义了资源范围。目前支持三个：`public`、`forms`、`read_entries`
* public, 获取用户的头像、昵称、邮箱、是否为付费用户等信息
* forms, 获取用户所有表单信息、单个表单详情、表单当前状态（是否开启，填写权限，已收集数据量）
* read_entries, 获取某表单下的数据信息，批量获取或单条获取，并且可基于查询条件获取想要的数据。

## Rate Limit

每个用户每小时调用120次。HTTP Header中会留下相应的信息。

    X-RateLimit-Limit:120
    X-RateLimit-Remaining:119

目前这个数值不可更改。

## API列表

### 获取表单列表

需要Scope: `forms`

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

需要Scope: `forms`

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

需要Scope: `forms`

    GET https://api.jinshuju.net/v4/forms/RygpW3/status?access_token=...

```json
{
  "is_open": true,
  "permission": "public",
  "entries_count": 1
}
```

### 获取多条数据

需要Scope： `read_entries`

    GET https://api.jinshuju.net/v4/forms/RygpW3/entries?access_token=...

```json
    {
    "entries": [
        {
            "serial_number": 53,
            "field_2": "小金",
            "field_3": "A公司",
            "field_4": "FqFO",
            "field_5": {
                "value": "13555555555"
            },
            "field_6": "餐饮，教育",
            "field_7": "xiaojin@jinshuju.net",
            "field_8": "wWW3",
            "field_9": "W2B0",
            "created_at": "2016-01-29T03:38:20.032Z",
            "updated_at": "2016-01-29T03:38:20.032Z",
        },
        {
            "serial_number": 52,
            "field_2": "小银",
            "field_3": "B公司",
            "field_4": "QW8U",
            "field_5": {
                "value": "13666666666"
            },
            "field_6": "教育，IT/互联网/计算机",
            "field_7": "xiaoyin@jinshuju.net",
            "field_8": "",
            "field_9": "W2B0",
            "creator_name": "user@mail.com",
            "updater_name": "user@gmail.com",
            "created_at": "2016-01-29T02:27:49.285Z",
            "updated_at": "2016-01-29T02:57:17.691Z",
            "info_remote_ip": "180.168.0.0",
            "info_platform": "Macintosh",
            "info_os": "OS X 10.11.2",
            "info_browser": "Chrome 47.0.2526.106"
        },
        ...
        {
            "serial_number": 34,
            "field_2": "小铜",
            "field_3": "本地搜索",
            "field_4": "FqFO",
            "field_5": {
                "value": "13777777777"
            },
            "field_6": "电子商务",
            "field_7": "xiaotong@jinshuju.net",
            "field_8": "DXls",
            "field_9": "NvUZ",
            "creator_name": "user@mail.com",
            "updater_name": "user@gmail.com",
            "created_at": "2016-01-29T02:27:49.285Z",
            "updated_at": "2016-01-29T02:57:17.691Z",
            "info_remote_ip": "180.168.0.0",
            "info_platform": "Macintosh",
            "info_os": "OS X 10.11.2",
            "info_browser": "Chrome 47.0.2526.106"
        }
    ]
}
```

#### 分页    

获取表单数据时，默认单次获取的数据量(per_page)为20，会从最后提交的数据(cursor)开始往前获取，可参考下面参数来调整获取数据的数量和位置。

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token  | string | **必须**。通过oauth认证所得到的access_token
per_page  | integer | 单次获取的数据量，默认20，最多支持到50
cursor | integer | 每次获取数据的游标点，以第cursor条数据往前获取，默认是数据总量。

例如从第40条数据往前获取30条的数据：

    Get https://api.jinshuju.net/v4/forms/RygpW3/entries?access_token=...&per_page=30&cursor=40    

批量获取数据信息时，在每一次请求的header里会有自定义的头部信息来指明本次获取的状态，如下表所示。

Header Name  | Description
------------- | -----------
X-Total  | 符合条件的所有数据总数，例如X-Total:50
X-Count  | 当前请求返回的数据数量，例如X-Count:20

上一页下一页的信息会放置到Link Header里，例如
```html
Link:<https://api.jinshuju.net/v4/forms/aJSON8/entries?access_token=...&cursor=2082>; rel="prev", 
  <https://api.jinshuju.net/v4/forms/aJSON8/entries?access_token=...&cursor=2061>; rel="next"
```

目前支持两种rel值：

Rel  | Description
----------- | ---------------
next  | 下一页的列表访问地址
prev  | 上一页的列表访问地址  

### 数据查询

查询数据，支持与现有数据列表查询类似的接口

* 文本、单选、多选、下拉框、评分、商品、序号(serial_number)、扩展属性(x_field_1)
`http://api.jinshuju.net/v4/forms/aJSON8/entries?access_token=<token>&field_1=xxx`
*注：获取表单结构中暂无商品字段的item信息*
*注：全匹配查询，不支持模糊查询*

* 同一字段的多个条件取并集查询
`http://api.jinshuju.net/v4/forms/aJSON8/entries?access_token=<token>&field_1[]=xxx&field_1[]=yyy`

* 多个字段取交集查询
`http://api.jinshuju.net/v4/forms/aJSON8/entries?access_token=<token>&field_1=xxx&field_2=yyy`

* 矩阵单选查询
`http://api.jinshuju.net/v4/forms/aJSON8/entries?access_token=<token>&field_1[<题目code>][]=<选项code>`

* 二级下拉框查询
`http://api.jinshuju.net/v4/forms/aJSON8/entries?access_token=<token>&field_1[<一级选项code>]=<二级选项code>`

* 微信省市查询
`http://api.jinshuju.net/v4/forms/aJSON8/entries?access_token=<token>&x_field_weixin_province_city[陕西]=西安`

* 数据提交时间查询
指定日期：`http://api.jinshuju.net/v4/forms/aJSON8/entries?access_token=<token>&created_at=2016-1-22`
某一日期之后：`http://api.jinshuju.net/v4/forms/aJSON8/entries?access_token=<token>&created_at[start]=2016-1-22`
某一日期之前：`http://api.jinshuju.net/v4/forms/aJSON8/entries?access_token=<token>&created_at[end]=2016-1-22`
某个日期区间：`http://api.jinshuju.net/v4/forms/aJSON8/entries?access_token=<token>&created_at[start]=2015-12-15&created_at[end]=2016-1-22`

### 获取单条数据

需要Scope: `read_entries`

    POST https://api.jinshuju.net/v4/forms/RygpW3/entries/<序列号>?access_token=...

JSON Load:

```json
{
  "serial_number": 1,
  "total_price": 0,
  "field_2": "姓名",
  "field_3": "email@domain.com",
  "field_4": {
    "value": "18899029392"
    },
  "field_5": "020-99887727",
  "field_6": {
    "province": "天津市",
    "city": "天津市",
    "district": "北辰区",
    "street": "天津地址"
    },
  "field_7": {
    "latitude": "31.233293",
    "longitude": "121.39189",
    "address": "上海市普陀区长征镇天地软件园"
    },
  "field_8": "bGKQ",
  "field_9": [
    "ER0Q",
    "LJHY"
    ],
  "field_10": "TKs8",
  "field_11": [
    {
      "statement": "YoJ2",
      "choice": "zQZ9"
    },
    {
      "statement": "NUPN",
      "choice": "rz8G"
    },
    {
      "statement": "iCSI",
      "choice": "rz8G"
    }
    ],
  "field_12": [
    {
      "statement": "JKzL",
      "dimensions": {
        "7u4Z": "矩阵1_1",
        "mvA5": "矩阵1_2",
        "1Nrk": "矩阵1_3"
      }
    },
    {
      "statement": "341l",
      "dimensions": {
        "7u4Z": "矩阵2_1",
        "mvA5": "矩阵2_2",
        "1Nrk": "矩阵2_3"
        }
      },
    {
      "statement": "E7xN",
      "dimensions": {
      "7u4Z": "矩阵3_1",
      "mvA5": "矩阵3_2",
      "1Nrk": "矩阵3_3"
      }
    }
    ],
  "field_13": 123232,
  "field_14": {
    "hour": 1,
    "minute": 3
    },
  "field_15": "2016-01-16",
  "field_16": "k9gK",
  "field_19": "https://jinshuju.net",
  "field_20": 2,
  "field_21": {
    "level_1": "b8Kk",
    "level_2": "tuD9"
    },
  "field_22": [
    {
      "name": "1_22_pricing.pdf",
      "url": "https://fileurl"
    }
    ],
    "field_23": [
    {
      "item": "56977c853eec76796f000001",
      "number": 1
    }
    ],
    "field_24": [
    {
      "item": "56977c973eec76796a000049",
      "number": 2
    },
    {
      "item": "56977c973eec76796a00004a",
      "number": 2
    },
    {
      "item": "56977c973eec76796a00004b",
      "number": 3
    }
  ],
  "creator_name": "user@mail.com",
  "updater_name": "user@gmail.com",
  "created_at": "2016-01-15T02:27:49.285Z",
  "updated_at": "2016-01-15T02:57:17.691Z",
  "info_remote_ip": "180.168.0.0",
  "info_platform": "Macintosh",
  "info_os": "OS X 10.11.2",
  "info_browser": "Chrome 47.0.2526.106"
}
```

### 获取当前用户基本信息

需要Scope: `public`或者默认

    GET https://api.jinshuju.net/v4/me?access_token=...

```json
{
  "email": "email@mail.com",
  "nickname": "email@mail.com",
  "avatar": null,
  "paid": false
}
```
