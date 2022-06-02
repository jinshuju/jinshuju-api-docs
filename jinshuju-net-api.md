# 金数据高级API文档 (jinshuju.net)

**注意高级API限通用型saas平台产品和金数据企业高级版及以上版本使用，且仅支持这两种类型的用户开通**

**本文档适用于您的金数据账号位于 jinshuju.net，如有疑问请联系客服或者销售**

金数据API使用OAuth 2标准进行用户验证。

## 发送请求

所有的URL需要以`https://api.jinshuju.net/v4/`开头。仅支持SSL。目前API版本为`v4`版本。例如，想获得当前用户的基本信息情况：

    curl https://api.jinshuju.net/v4/me?access_token=...

## OAuth 2验证

v4版本的金数据API支持OAuth 2。你可以使用标准的OAuth交互协议进行访问。相关URL如下：

* 认证域: https://account.jinshuju.net
* 接口域: https://api.jinshuju.net/v4

### 1. 转向到金数据申请验证
### 1.1 转向到金数据申请验证

    GET https://account.jinshuju.net/oauth/authorize

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**，注册的金数据应用ID，目前仅对金数据商业合作伙伴开放。
redirect_uri  | string | **必须**，金数据应用的callback URI，当授权完成之后要转向的地址。
response_type | string | **必须**，OAuth 2中必须将其指定为`code`。
scope  | string | 空格隔开的列表。目前支持的scope包括：`public` `profile` `forms` `read_entries` `form_setting`，默认为public。
state | string | 唯一随机的的字符串，用来防止跨站攻击。

### 1.2 转向到金数据申请企业的验证

    GET https://account.jinshuju.net/org_oauth/authorize

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**，注册的金数据应用ID，目前仅对金数据商业合作伙伴开放。
redirect_uri  | string | **必须**，金数据应用的callback URI，当授权完成之后要转向的地址。
response_type | string | **必须**，OAuth 2中必须将其指定为`code`。
scope  | string | 空格隔开的列表。目前支持的scope包括：`public` `profile` `forms` `read_entries` `write_entries` `form_setting` `read_contacts` `users`
state | string | 唯一随机的的字符串，用来防止跨站攻击。

### 2. 获得访问的access token

### 2.1 获取用户访问的access token
用户同意之后，金数据将会转向到你的网站，并带上`code`和之前提供的`state`参数。如果state不匹配，你可以终止这个请求。

拿到code之后，就可以交换access token:

    POST https://account.jinshuju.net/oauth/token

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**，注册的金数据应用ID，目前仅对金数据商业合作伙伴开放。
client_secret  | string | **必须**，金数据应用的secret。
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

### 2.2 获取企业访问的access token

企业同意之后，金数据将会转向到你的网站，并带上`code`和之前提供的`state`参数。如果state不匹配，你可以终止这个请求。

拿到code之后，就可以交换access token:
    POST https://account.jinshuju.net/org_oauth/token

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**，注册的金数据应用ID，目前仅对金数据商业合作伙伴开放。
client_secret  | string | **必须**，金数据应用的secret。
code  | string | **必须**，在1.2中获得的企业code。
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

### 2.3 使用refresh token获得新的access_token
目前access_token有效期为7200秒,当access_token过期时，可以使用refresh_token来获得新的access_token。

#### 刷新用户的access_token:
    POST https://account.jinshuju.net/oauth/token

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**，注册的金数据应用ID，目前仅对金数据商业合作伙伴开放
client_secret  | string | **必须**，金数据应用的secret
refresh_token  | string | **必须**，获取access_token时得到的refresh_token。
grant_type | string | **必须**，指定为 `refresh_token`。

返回的response的形式如下，得到新的access_token 和refresh_token：
````json
{
    "access_token": "0909a26c330883cf2cd44f8926c663ac1d639ed2940d879fb2bf4a62e06ff4a8",
    "token_type": "bearer",
    "expires_in": 7200,
    "refresh_token": "648478764ff94d09d62f78c8fad8c2b7886ee93c59090ececacd6dfe1b648949",
    "scope": "public forms read_entries",
    "created_at": 1455710364
}
````

#### 刷新企业的access_token:

    POST https://account.jinshuju.net/org_oauth/token

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**，注册的金数据应用ID，目前仅对金数据商业合作伙伴开放
client_secret  | string | **必须**，金数据应用的secret
refresh_token  | string | **必须**，在2.2中获取access_token时得到的refresh_token。
grant_type | string | **必须**，指定为 `refresh_token`。

返回的response的形式如下，得到新的access_token 和refresh_token：
````json
{
    "access_token": "0909a26c330883cf2cd44f8926c663ac1d639ed2940d879fb2bf4a62e06ff4a8",
    "token_type": "bearer",
    "expires_in": 7200,
    "refresh_token": "648478764ff94d09d62f78c8fad8c2b7886ee93c59090ececacd6dfe1b648949",
    "scope": "public forms read_entries",
    "created_at": 1455710364
}
````

### 3. 使用access token访问API

    GET https://api.jinshuju.net/v4/forms?access_token=...

你可以把token放在URL中。也可以使用Authorization header如下：

    Authorization: bearer OAUTH-TOKEN

例如使用curl

    curl -H "Authorization: bearer OAUTH-TOKEN" https://api.jinshuju.net/v4/forms


## Redirect URL

`redirect_uri`是必须的。如果你使用[omniauth-jinshuju](https://github.com/jinshuju/omniauth-jinshuju)，就可以使用类似于`https://domain.com/auth/jinshuju/callback`的地址。

## Scopes
Scope定义了资源范围。目前支持八个：`public`、`profile`、`forms`、`read_entries`、`write_entries`、`form_setting`、`read_contacts`、`users`
* public, 获取用户的头像、昵称、邮箱、是否为付费用户等信息（**邮箱、是否付费将会在后面的版本中移除，如需要，请使用profile scope**）
* profile, 获取用户的账户信息，邮箱、是否为付费用户（只读）、自定义域名（只读）
* forms, 获取用户所有表单信息、单个表单详情、表单当前状态（是否开启，填写权限，已收集数据量）
* read_entries, 获取某表单下的数据信息，批量获取或单条获取，并且可基于查询条件获取想要的数据
* write_entries，获取某表单下的单条数据的编辑链接，并且可打开链接编辑数据
* form_setting, 获取、更新表单的设置
* users，获取企业的用户列表
* read_contacts，获取联系人列表和联系人标签列表


## 访问限制
访问API是基于金数据授权的用户来做频率限制的，目前免费用户1,000次/小时，专业版用户10,000次/小时，专业增强版用户20,000次/小时。

HTTP Header中会留下相应的信息。

    X-RateLimit-Limit:1000
    X-RateLimit-Remaining:999

如果用户有特别高的访问频率需求，可以联系我们。

## 分页

当请求返回多个条目时，如表单列表、数据列表时，默认每次(per_page)返回20个条目，可以设定per_page参数来单次获得更多的数据，但目前最多支持50条。

例如：

    GET https://api.jinshuju.net/v4/forms?access_token=...&per_page=50

在每一次请求返回的header里会包含分页信息，如下表所示：

Header Name  | Description
------------- | -----------
X-Total  | 符合条件的总数，例如X-Total:50
X-Count  | 当前请求返回的数量，例如X-Count:20
Link  | 包含上一页(prev)或下一页(next)的访问地址，rel目前仅支持next和prev。

例如获取表单列表时，request header里会返回如下：
```html
Link:<https://api.jinshuju.net/v4/forms?access_token=...&per_page=20&cursor=xxxxx>; rel="prev",
  <https://api.jinshuju.net/v4/forms?access_token=...&per_page=20&cursor=xxxxx>; rel="next"
```

在发出第一次查询请求后，不断的检查返回的Link Header里的next列表，如果存在则直接使用链接去获取，不存在则代表批量获取完成。

链接中的cursor是查询的游标，在访问不同的api时，含义不同。查询表单列表时，代表下一次要取的表单的id；查询数据列表时，代表下一次要取的数据的序号，数据序号是一个递增的整数，由于存在数据删除的情况，所以可能是不连贯的，不建议采用分页数值和序号来拼cursor值。

## API列表

### 获取企业中用户列表

需要Scope: `users`

    get https://api.jinshuju.net/v4/users

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token  | string | **必须**，可使用企业管理员认证的2.1中的个人access token，或使用2.2中的企业access token。

Json Load:
```json
{
  "users": [
    {
      "email": "email@mail.com",
      "nickname": "email@mail.com",
      "avatar": "https://dn-jsjpub.qbox.me/av/517aa4fe24290aa13800001395.jpg",
      "paid": false,
      "uid": "fk3a9EvKTu3KKmTqa2CisQ",
      "openid": "fk3a9EvKTu3KKmTqa2CisQ",
      "role": "admin",
      "status": "active",
      "authentications": [
        {
          "provider": "identity",
          "uid": "uid"
        }
      ]
    }
  ]
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
  "paid": false,
  "uid": "fk3a9EvKTu3KKmTqa2CisQ"
}
```

参数名  | 类型 | 参数说明
------------- | ----------- | ---------
email  | String  | 用户邮箱地址，全局唯一
nickname  |  String  | 用户昵称
avatar  |  String  | 头像地址
paid  |  Boolean  | 是否为付费用户
uid  |  String  |  用户id

### 获取当前用户账户信息

需要Scope: `profile`

    GET https://api.jinshuju.net/v4/profile?access_token=...

```json
{
  "email": "email@mail.com",
  "paid": false,
  "custom_domain": 'forms.example.com'
}
```

参数名  | 类型 | 参数说明
------------- | ----------- | ---------
email  | String  | 用户邮箱地址，全局唯一
paid  |  Boolean  | 是否为付费用户
custom_domain | String | 自定义域名信息，该功能可用时返回用户在个人中心设置的值，否则为空

### 获取表单列表

需要Scope: `forms`

    GET https://api.jinshuju.net/v4/forms?access_token=...

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token  | string | **必须**，可使用2.1中的个人access token，可获取企业成员的的所有表单；或2.2中的企业access token，可获取企业中的所有表单。
openid  | string | **可选**，通过企业的access_token获取用户表单列表时必须填写，通过用户access_token获取用户列表时无需填写。


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
        "updated_at": "2016-02-16T02:37:35.756Z",
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
        "created_at": "2016-02-04T07:09:20.559Z",
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
    "updated_at": "2016-02-04T07:10:20.559Z",
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

### 复制表单

注意：示例中的<2d4iH0>为被复制的表单token

POST https://api.jinshuju.net/v4/forms/2d4iH0/copy

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token  | string | **必须**，可使用2.1中的个人access token，或2.2中的企业access token。
openid  | string | **可选**，获取的用户列表中的openid。使用个人的acces token无需填写；使用企业的access token必须填写。
name  | string | **可选**，复制出来的表单命名。如果不提供或者为空字符串，将使用“[新]”+原表单名作为复制后的表单的名字。

默认情况下，返回的response的形式如下：

```json
{
    "id": "56b2f86ca3f5206d76000143",
    "token": "TivBsE",
    "name": "小金俱乐部活动报名",
    "entries_count": 0,
    "shared": false,
    "description": "2016年小金俱乐部第一站来到了上海，这一次我们把上海当成主场。如果你之前遗憾没有参加小金的北京发布会",
    "created_at": "2016-02-04T07:06:20.559Z",
    "updated_at": "2016-02-04T07:10:20.559Z",
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

    GET https://api.jinshuju.net/v4/forms/RygpW3/entries/<序列号>?access_token=...

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

### 获取单条数据的编辑链接

需要Scope: `write_entries`

    GET https://api.jinshuju.net/v4/forms/RygpW3/entries/<序列号>/edit?access_token=...

Redirect to:

```
https://jinshuju.net/f/RygpW3/e/<token>/edit?&t=...
```

### 获取、更新表单设置

需要Scope: `form_setting`

#### 获取表单设置

    get https://api.jinshuju.net/v4/forms/RygpW3/setting?access_token=...

Json Load:
```json
{
    "icon": "fontello-sound",
    "color": "#afa373",
    "open_rule": "open",
    "permission": "public",
    "result_state": "closed",
    "result_url": null,
    "search_state": "closed",
    "search_url": null,
    "push_url": null,
    "success_redirect_url": "https://baidu.com",
    "success_redirect_fields": [
        "field_2",
        "field_1"
    ]
}
```
#### 更新表单设置

    PUT https://api.jinshuju.net/v4/forms/RygpW3/setting

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token	| string | **必须**,通过oauth认证所得到的access_token
success_redirect_url | string | 提交成功后的跳转网址
success_redirect_fields | string | 提交成功后的跳转网址附加字段参数，以及提交给该字段的信息，最多三个参数，多个参数以空格分隔，如"serial_number x_field_1"，超过三个参数会回应报错信息。参数必须为表单里字段，会自动过滤非表单字段，目前支持：序号、单/多行文本、单选、多选、数字、邮箱、电话、日期以及网址等字段。如果表单中包含商品字段，则还可以附带序号和总价。可参考 https://help.jinshuju.net/articles/redirect-with-params.html
push_url | string | 数据以JSON格式推送的网址，使用请参考https://help.jinshuju.net/articles/http-push
publish_form_embed_js | string | 设置官方应用中心嵌入到金数据发布表单页面的js
publish_form_embed_styles | string | 设置官方应用中心嵌入到金数据发布表单页面的style


#### 为表单添加协作成员

需要Scope: `form_setting`

    POST https://api.jinshuju.net/v4/forms/RygpW3/cooperators?access_token=...

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token    | string | **必须**,可使用2.1中的个人access token，或2.2中的企业access token。
openid | string | **必须**，获取的用户列表中的openid。这里需填写加为协作成员的用户openid。
role | string | **必须**，指定的角色，仅支持 manager, data_maintainer, data_viewer。


#### 为表单变更协作成员角色

需要Scope: `form_setting`

    PUT https://api.jinshuju.net/v4/forms/RygpW3/cooperators/<openid>?access_token=...

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token    | string | **必须**,可使用2.1中的个人access token，或2.2中的企业access token。
role | string | **必须**，指定的角色，仅支持 manager, data_maintainer, data_viewer。

#### 为表单移除协作成员

需要Scope: `form_setting`

    DELETE https://api.jinshuju.net/v4/forms/RygpW3/cooperators/<openid>?access_token=...

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token    | string | **必须**,可使用2.1中的个人access token，或2.2中的企业access token。


#### 删除表单

需要Scope: `forms`

    DELETE https://api.uat.jinshuju.net/v4/forms/YYtYiX?access_token=...

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token | string | **必须**,可使用2.1中的个人access token，或2.2中的企业access token。

### 获取联系人列表

需要Scope: `read_contacts`

    GET https://api.jinshuju.net/v4/contacts?access_token=...

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token  | string | **必须**，可使用2.1中的个人access token，可获取企业成员的的所有联系人
mobile  | string | **可选**，按手机号搜索联系人
email  | string | **可选**，按邮箱搜索联系人
weixin_openid  | string | **可选**，按微信openid搜索联系人
start_created_at | string | **可选**，搜索创建时间大于指定时间点的联系人
end_created_at | string | **可选**，搜索创建时间小于指定时间点的联系人
start_updated_at | string | **可选**，搜索更新时间大于指定时间点的联系人
end_updated_at | string | **可选**，搜索更新时间小于指定时间点的联系人

```json
[
  {
    "created_at": "2022-05-25T01:09:06.284+08:00",
    "updated_at": "2022-05-25T01:09:06.284+08:00",
    "name": "小金",
    "name_pinyin": "XIAOJIN",
    "comment": "",
    "mobiles": [{"value": "152********"}],
    "emails": ["email@mail.com"],
    "addresses": [],
    "weixin_info": {},
    "tags": []
  }
]
```

### 获取联系人标签列表

需要Scope: `read_contacts`

    GET https://api.jinshuju.net/v4/contact_tags?access_token=...

```json
[
  {
    "created_at": "2022-05-25T01:09:06.284+08:00",
    "updated_at": "2022-05-25T01:09:06.284+08:00",
    "name": "小金",
    "contacts_count": 20
  }
]
```
