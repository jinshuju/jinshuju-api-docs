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

    Authorization: token OAUTH-TOKEN

例如使用curl

    curl -H "Authorization: token OAUTH-TOKEN" https://api.jinshuju.net/v4/forms

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
[
    {
        "id": "56c28b33c02f6713aa000092",
        "token": "E2FBnj",
        "name": "市场调查表",
        "entries_count": 0,
        "shared": false,
        "description": "这是一个市场调查表",
        "created_at": "2016-02-16T02:36:35.756Z",
        "setting": {
            "icon": "fontello-paper-plane",
            "color": "#659199",
            "open_rule": "open",
            "permission": "public",
            "result_state": "closed",
            "result_url": null,
            "search_state": "closed",
            "search_url": null,
            "push_url": null
        }
    },
    {
        "id": "56b2f86ca3f5206d76000143",
        "token": "TivBsE",
        "name": "小金俱乐部活动报名",
        "entries_count": 0,
        "shared": false,
        "description": "2016年小金俱乐部第一站来到了上海",
        "created_at": "2016-02-04T07:06:20.559Z",
        "setting": {
            "icon": "fontello-pencil",
            "color": "#659199",
            "open_rule": "open",
            "permission": "public",
            "result_state": "closed",
            "result_url": null,
            "search_state": "closed",
            "search_url": null,
            "push_url": null
        }
    }
]

```

### 获取表单详情

需要Scope: `forms`

    GET https://api.jinshuju.net/v4/forms/RygpW3?access_token=...

```json
{
    "id": "56b2f86ca3f5206d76000143",
    "token": "TivBsE",
    "name": "小金俱乐部活动报名",
    "entries_count": 0,
    "shared": false,
    "description": "2016年小金俱乐部第一站来到了上海，这一次我们把上海当成主场。如果你之前遗憾没有参加小金的北京发布会",
    "created_at": "2016-02-04T07:06:20.559Z",
    "fields": [
        {
            "type": "single_line_text",
            "label": "姓名",
            "api_code": "field_1",
            "notes": "",
            "predefined_value": "",
            "private": false,
            "validations": {
                "required": true
            }
        },
        {
            "type": "single_line_text",
            "label": "公司、团队或者组织名称",
            "api_code": "field_16",
            "notes": "",
            "predefined_value": "",
            "private": false,
            "validations": {}
        },
        {
            "type": "single_choice",
            "label": "称呼",
            "api_code": "field_2",
            "notes": "",
            "predefined_value": null,
            "private": false,
            "validations": {
                "required": true
            },
            "choices": [
                {
                    "name": "先生",
                    "value": "2jYk"
                },
                {
                    "name": "女士",
                    "value": "pIjs"
                },
                {
                    "name": "保密",
                    "value": "yofb"
                }
            ],
            "allow_other": false
        },
        {
            "type": "phone",
            "label": "手机",
            "api_code": "field_4",
            "notes": "",
            "predefined_value": "",
            "private": false,
            "validations": {
                "required": true
            }
        },
        {
            "type": "multiple_choice",
            "label": "您所在的行业",
            "api_code": "field_3",
            "notes": "",
            "predefined_value": null,
            "private": false,
            "validations": {
                "required": true
            },
            "choices": [
                {
                    "name": "餐饮",
                    "value": "vXHU"
                },
                {
                    "name": "娱乐",
                    "value": "0yrs"
                },
                {
                    "name": "教育",
                    "value": "zI63"
                },
                {
                    "name": "IT/互联网/计算机",
                    "value": "uN9L"
                },
                {
                    "name": "微信营销",
                    "value": "0tKd"
                },
                {
                    "name": "零售",
                    "value": "1eS0"
                },
                {
                    "name": "旅游",
                    "value": "zFay"
                },
                {
                    "name": "汽车",
                    "value": "iYs5"
                },
                {
                    "name": "金融",
                    "value": "rmIa"
                },
                {
                    "name": "房地产",
                    "value": "KzHg"
                },
                {
                    "name": "电子商务",
                    "value": "yP93"
                },
                {
                    "name": "家居",
                    "value": "51UD"
                },
                {
                    "name": "文化/媒体",
                    "value": "Gdyw"
                },
                {
                    "name": "服饰",
                    "value": "e5dJ"
                },
                {
                    "name": "医疗",
                    "value": "0vZh"
                },
                {
                    "name": "服务行业",
                    "value": "etAf"
                },
                {
                    "name": "学生",
                    "value": "ArtE"
                }
            ],
            "allow_other": true
        },
        {
            "type": "email",
            "label": "邮箱",
            "api_code": "field_17",
            "notes": "",
            "predefined_value": null,
            "private": false,
            "validations": {
                "required": true
            }
        },
        {
            "type": "multiple_choice",
            "label": "此行目的",
            "api_code": "field_5",
            "notes": "<p>我们会与您分享金数据的行业使用情况，也欢迎您来与我们分享自己的使用体会</p>",
            "predefined_value": null,
            "private": false,
            "validations": {},
            "choices": [
                {
                    "name": "听听金数据官方的使用案例分享",
                    "value": "7oLf"
                },
                {
                    "name": "听听其他金数据用户的使用情况",
                    "value": "vQki"
                },
                {
                    "name": "分享我自己使用金数据的情况",
                    "value": "6qHO"
                },
                {
                    "name": "与其他金数据用户交流使用心得",
                    "value": "MCkX"
                }
            ],
            "allow_other": true
        },
        {
            "type": "single_choice",
            "label": "您是否愿意分享一个话题？",
            "api_code": "field_10",
            "notes": "<p>与现场观众分享金数据的使用体验，可能获得意想不到的商机，或者朋友</p>",
            "predefined_value": null,
            "private": false,
            "validations": {},
            "choices": [
                {
                    "name": "是，我想与其他人分享",
                    "value": "1lq3"
                },
                {
                    "name": "不是，我更乐意当听众",
                    "value": "J2RP"
                }
            ],
            "allow_other": false
        },
        {
            "type": "single_line_text",
            "label": "话题名称",
            "api_code": "field_11",
            "notes": "<p>我们与您联系并且通知您是否通过审核（您可能会接到来自金数据的电话）</p>",
            "predefined_value": "",
            "private": false,
            "validations": {
                "required": true
            }
        },
        {
            "type": "paragraph_text",
            "label": "话题简介",
            "api_code": "field_12",
            "notes": "<p>亲爱的，如果你想要在活动上分享，我们希望你的话题是精心准备的，所以请告诉我们话题简介吧。</p>",
            "predefined_value": "",
            "private": false,
            "validations": {
                "required": true
            }
        },
        {
            "type": "paragraph_text",
            "label": "对于这次活动，您有什么想要问我们的？",
            "api_code": "field_6",
            "notes": "",
            "predefined_value": "",
            "private": false,
            "validations": {}
        },
        {
            "type": "drop_down",
            "label": "是否已经联系",
            "api_code": "field_7",
            "notes": "",
            "predefined_value": null,
            "private": true,
            "validations": {},
            "choices": [
                {
                    "name": "是",
                    "value": "SoG1"
                },
                {
                    "name": "否",
                    "value": "iyh7"
                },
                {
                    "name": "没打通",
                    "value": "9m1q"
                }
            ],
            "allow_other": false
        },
        {
            "type": "paragraph_text",
            "label": "联系情况",
            "api_code": "field_8",
            "notes": "",
            "predefined_value": "1. 能否参加\n2. 哪个公司？\n3. 金数据的使用情况",
            "private": true,
            "validations": {}
        },
        {
            "type": "single_choice",
            "label": "签到",
            "api_code": "field_15",
            "notes": "",
            "predefined_value": null,
            "private": true,
            "validations": {},
            "choices": [
                {
                    "name": "是 ",
                    "value": "303X"
                },
                {
                    "name": "否",
                    "value": "2Y8V"
                }
            ],
            "allow_other": false
        },
        {
            "type": "paragraph_text",
            "label": "备注",
            "api_code": "field_18",
            "notes": "",
            "predefined_value": "",
            "private": true,
            "validations": {}
        }
    ],
    "setting": {
        "icon": "fontello-pencil",
        "color": "#659199",
        "open_rule": "open",
        "permission": "public",
        "result_state": "closed",
        "result_url": null,
        "search_state": "closed",
        "search_url": null,
        "push_url": null
    }
}
```

### 获取表单当前状态

需要Scope: `forms`

    GET https://api.jinshuju.net/v4/forms/RygpW3/status?access_token=...

```json
{
    "is_open": true,
    "permission": "public",
    "entries_count": 60
}
```

### 获取多条数据

需要Scope： `read_entries`

    GET https://api.jinshuju.net/v4/forms/RygpW3/entries?access_token=...

```json
[
    {
        "serial_number": 1,
        "field_1": "小金",
        "field_16": "金数据",
        "field_2": "2jYk",
        "field_4": {
            "value": "18629058968",
            "verified": true
        },
        "field_3": [
            "zI63",
            "uN9L"
        ],
        "field_17": "roody@jinshuju.net",
        "field_5": [
            "7oLf",
            "vQki"
        ],
        "field_10": "1lq3",
        "field_11": "小金的应用场景",
        "field_12": "金数据在各行各业的用法",
        "field_6": "金数据有没有一些更加高级的技巧呢？",
        "field_7": "",
        "field_8": "1. 能否参加\n2. 哪个公司？\n3. 金数据的使用情况",
        "field_15": "",
        "field_18": "",
        "creator_name": "o王琰o",
        "updater_name": "",
        "created_at": "2016-02-17T11:40:31.524Z",
        "updated_at": "2016-02-17T11:40:31.524Z",
        "info_remote_ip": "123.139.21.4",
        "info_platform": "Macintosh",
        "info_os": "OS X 10.11.3",
        "info_browser": "Chrome 48.0.2564.109"
    }
]
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
    "field_1": "小金",
    "field_16": "金数据",
    "field_2": "2jYk",
    "field_4": {
        "value": "18629058968",
        "verified": true
    },
    "field_3": [
        "zI63",
        "uN9L"
    ],
    "field_17": "roody@jinshuju.net",
    "field_5": [
        "7oLf",
        "vQki"
    ],
    "field_10": "1lq3",
    "field_11": "小金的应用场景",
    "field_12": "金数据在各行各业的用法",
    "field_6": "金数据有没有一些更加高级的技巧呢？",
    "field_7": "",
    "field_8": "1. 能否参加\n2. 哪个公司？\n3. 金数据的使用情况",
    "field_15": "",
    "field_18": "",
    "creator_name": "o王琰o",
    "updater_name": "",
    "created_at": "2016-02-17T11:40:31.524Z",
    "updated_at": "2016-02-17T11:40:31.524Z",
    "info_remote_ip": "123.139.21.4",
    "info_platform": "Macintosh",
    "info_os": "OS X 10.11.3",
    "info_browser": "Chrome 48.0.2564.109"
}
```

### 获取当前用户基本信息

需要Scope: `public`或者默认

    GET https://api.jinshuju.net/v4/me?access_token=...

```json
{
  "email": "email@mail.com",
  "nickname": "email@mail.com",
  "avatar": "https://dn-jsjpub.qbox.me/av/517aa4fe24290aa13800001395.jpg",
  "paid": false
}
```
