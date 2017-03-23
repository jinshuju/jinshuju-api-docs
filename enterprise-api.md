# 金数据API文档 (企业版)

金数据API使用OAuth 2标准进行用户验证。


## 发送请求

所有的URL需要以`https://api.jinshuju.com/v4/`开头。仅支持SSL。目前API版本为`v4`版本。例如，想获得当前用户的基本信息情况：

    curl https://api.jinshuju.com/v4/me?access_token=...

## OAuth 2验证

v4版本的金数据API支持OAuth 2。你可以使用标准的OAuth交互协议进行访问。相关URL如下：

* 认证域: `https://account.jinshuju.com`
* 接口域: `https://api.jinshuju.com/v4`

### 1. 转向到金数据申请验证

### 1.1 转向到金数据申请企业中用户的验证

    GET https://account.jinshuju.com/oauth/authorize

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**，注册的金数据应用ID，目前仅对金数据商业合作伙伴开放。
redirect_uri  | string | **必须**，金数据应用的callback URI，当授权完成之后要转向的地址。
response_type | string | **必须**，OAuth 2中必须将其指定为`code`。
scope  | string | 空格隔开的列表。目前支持的scope包括：`public` `profile` `forms` `read_entries` `form_setting`，默认为public。
state | string | 唯一随机的的字符串，用来防止跨站攻击。

### 1.2 转向到金数据申请企业的验证

    GET https://account.jinshuju.com/org_oauth/authorize

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**，注册的金数据应用ID，目前仅对金数据商业合作伙伴开放。
redirect_uri  | string | **必须**，金数据应用的callback URI，当授权完成之后要转向的地址。
response_type | string | **必须**，OAuth 2中必须将其指定为`code`。
scope  | string | 空格隔开的列表。目前支持的scope包括：`public` `profile` `forms` `read_entries` `form_setting` `users`，默认为public。
state | string | 唯一随机的的字符串，用来防止跨站攻击。

### 2. 获得访问的access token

###2.1 获取用户访问的access token

用户同意之后，金数据将会转向到你的网站，并带上`code`和之前提供的`state`参数。如果state不匹配，你可以终止这个请求。

拿到code之后，就可以交换access token:

    POST https://account.jinshuju.com/oauth/token

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**，注册的金数据应用ID，目前仅对金数据商业合作伙伴开放。
client_secret  | string | **必须**，金数据应用的secret。
code  | string | **必须**，在1.1中获得的用户code。
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


###2.2 获取企业访问的access token

企业同意之后，金数据将会转向到你的网站，并带上`code`和之前提供的`state`参数。如果state不匹配，你可以终止这个请求。

拿到code之后，就可以交换access token:

    POST https://account.jinshuju.com/org_oauth/token

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

    POST https://account.jinshuju.com/oauth/token

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
client_id  | string | **必须**，注册的金数据应用ID，目前仅对金数据商业合作伙伴开放
client_secret  | string | **必须**，金数据应用的secret
refresh_token  | string | **必须**，在2.1中获取access_token时得到的refresh_token。
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

    POST https://account.jinshuju.com/org_oauth/token

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

    GET https://api.jinshuju.com/v4/forms?access_token=...

你可以把token放在URL中。也可以使用Authorization header如下：

    Authorization: bearer OAUTH-TOKEN

例如使用curl

    curl -H "Authorization: bearer OAUTH-TOKEN" https://api.jinshuju.com/v4/forms


## Redirect URL

`redirect_uri`是必须的。如果你使用[omniauth-jinshuju](https://github.com/jinshuju/omniauth-jinshuju)，就可以使用类似于`https://domain.com/auth/jinshuju/callback`的地址。

## Scopes

Scope定义了资源范围。目前支持六个：`public`、`profile`、`forms`、`read_entries`、`form_setting`、`users`
* public, 获取用户的头像、昵称、邮箱、是否为付费用户等信息（**邮箱、是否付费将会在后面的版本中移除，如需要，请使用profile scope**）
* profile, 获取用户的账户信息，邮箱、是否为付费用户（只读）、自定义域名（只读）
* forms, 获取用户所有表单信息、单个表单详情、表单当前状态（是否开启，填写权限，已收集数据量）
* read_entries, 获取某表单下的数据信息，批量获取或单条获取，并且可基于查询条件获取想要的数据
* form_setting, 获取、更新表单的设置
* users， 获取企业中的用户列表


## 访问限制
访问API是基于金数据授权的用户来做频率限制的，目前企业基础版每个企业500次/小时，协作版每个企业1000次/小时，高级版每个企业2000次/小时，商业合作版每个企业5000次/小时，如需更多可联系金数据扩充。

HTTP Header中会留下相应的信息。

    X-RateLimit-Limit:120
    X-RateLimit-Remaining:119


## 分页

当请求返回多个条目时，如表单列表、数据列表时，默认每次(per_page)返回20个条目，可以设定per_page参数来单次获得更多的数据，但目前最多支持50条。

例如：

    GET https://api.jinshuju.com/v4/forms?access_token=...&per_page=50

在每一次请求返回的header里会包含分页信息，如下表所示：

Header Name  | Description
------------- | -----------
X-Total  | 符合条件的总数，例如X-Total:50
X-Count  | 当前请求返回的数量，例如X-Count:20
Link  | 包含上一页(prev)或下一页(next)的访问地址，rel目前仅支持next和prev。

例如获取表单列表时，request header里会返回如下：
```html
Link:<https://api.jinshuju.com/v4/forms?access_token=...&per_page=20&cursor=xxxxx>; rel="prev",
  <https://api.jinshuju.com/v4/forms?access_token=...&per_page=20&cursor=xxxxx>; rel="next"
```

在发出第一次查询请求后，不断的检查返回的Link Header里的next列表，如果存在则直接使用链接去获取，不存在则代表批量获取完成。

链接中的cursor是查询的游标，在访问不同的api时，含义不同。查询表单列表时，代表下一次要取的表单的id；查询数据列表时，代表下一次要取的数据的序号，数据序号是一个递增的整数，由于存在数据删除的情况，所以可能是不连贯的，不建议采用分页数值和序号来拼cursor值。


### 获取企业中用户列表

需要Scope: `users`

    get https://api.jinshuju.com/v4/users
    
参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token  | string | **必须**，必须使用2.2中的企业access token。
provider  | string | **可选**，企业的subdomain。
uid  | string | **可选**，使用SDK静默注册的用户在用户的profile中记录的uid。

Json Load:
```json
{
    "openid": "033ae0b9-ed61-5587-900a-5c95961914ee",
    "name": "User_liudalu0002",
    "email": "liudalu0002@fake.xitian.com",
    "mobile": null,
    "role": "teamworker",
    "status": "active",
    "avatar": null,
    "forms_count": 2,
    "entries_count": 0,
    "authentications": [
      {
        "provider": "xitian",
        "uid": "liudalu0002"
},
```

### 获取当前用户信息

需要Scope: `public`

    get https://api.jinshuju.com/v4/me
    
参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token  | string | **必须**，可使用2.1中的个人access token；或2.2中的企业access token。
openid  | string | **可选**，金数据中用户的唯一标识。如使用2.1中的个人access token，无需携带；如使用2.2中的企业access token，必须携带。

Json Load:
```json
{
    "openid": "5af4563b-4146-58a9-a2c0-9c41c488333b",
    "name": "张三",
    "email": "zhangsan@jinshuju.com",
    "mobile": "18000000001",
    "role": "admin",
    "status": "active",
    "avatar": null,
    "forms_count": 45,
    "entries_count": 21396
},
```

### 获取当前企业信息

需要Scope: `profile`

    get https://api.jinshuju.com/v4/profile
    
参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token  | string | **必须**，可使用2.1中的个人access token；或2.2中的企业access token。

Json Load:
```json
{
  "email": "creator@jinshuju.com",
  "organization_name": "金数据",
  "organization_subdomain": "jinshuju",
  "paid": true,
  "custom_domain": "http://com-uat.tunnel.mobi"
}
```

### 注册用户

需要Scope: `users`

    post https://api.jinshuju.com/v4/users
    
参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token  | string | **必须**，必须使用2.2中的企业access token。
uid  | string | **可选**，使用SDK静默注册的用户在用户的profile中记录的uid。

Json Load:
```json
{
    "openid": "033ae0b9-ed61-5587-900a-5c95961914ee",
    "name": "User_liudalu0002",
    "email": "liudalu0002@fake.xitian.com",
    "mobile": null,
    "role": "teamworker",
    "status": "active",
    "avatar": null,
    "forms_count": 2,
    "entries_count": 0,
    "authentications": [
      {
        "provider": "xitian",
        "uid": "liudalu0002"
},
```



### 获取表单列表

需要Scope: `forms`

    GET https://api.jinshuju.com/v4/forms

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token  | string | **必须**，可使用2.1中的个人access token，可获取企业成员的的所有表单；或2.2中的企业access token，可获取企业中的所有表单。
openid  | string | **可选**，通过企业的access_token获取用户表单列表时必须填写，通过用户access_token获取用户列表时无需填写。
source  | string | **可选**，created可获取所有当前用户是表单创建者的表单列表；managed可获取所有当前用户是表单创建者和表单管理员的表单列表。

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

    GET https://api.jinshuju.com/v4/forms/RygpW3?access_token=...

```json     
{
  "id": "58d0821a2084c548c9c76938",
  "token": "iIAVew",
  "name": "包含所有字段的表单",
  "entries_count": 3,
  "shared": false,
  "description": null,
  "creator_name": "增长天王",
  "creator_openid": "5af4563b-4146-58a9-a2c0-9c41c488333b",
  "created_at": "2017-03-21T01:30:02.618Z",
  "updated_at": "2017-03-22T10:22:41.050Z",
  "fields": [
    {
      "type": "page_break",
      "label": null,
      "api_code": "field_1",
      "notes": ""
    },
    {
      "type": "formula",
      "label": "计算字段",
      "api_code": "field_29",
      "notes": "",
      "validations": {},
      "private": false,
      "formula": "field_10",
      "display_as_percentage": false
    },
    {
      "type": "single_line_text",
      "label": "单行文字",
      "api_code": "field_2",
      "notes": "",
      "validations": {},
      "predefined_value": null,
      "private": false
    },
    {
      "type": "paragraph_text",
      "label": "多行文字",
      "api_code": "field_3",
      "notes": "",
      "validations": {},
      "predefined_value": null,
      "private": false
    },
    {
      "type": "single_choice",
      "label": "单项选择",
      "api_code": "field_4",
      "notes": "",
      "validations": {},
      "private": false,
      "choices": [
        {
          "name": "选项",
          "value": "EtdU",
          "hidden": false
        },
        {
          "name": "选项",
          "value": "Z47n",
          "hidden": false
        },
        {
          "name": "选项",
          "value": "eldU",
          "hidden": false
        }
      ],
      "allow_other": false
    },
    {
      "type": "multiple_choice",
      "label": "多项选择",
      "api_code": "field_5",
      "notes": "",
      "validations": {},
      "private": false,
      "choices": [
        {
          "name": "选项",
          "value": "9WG4",
          "hidden": false
        },
        {
          "name": "选项",
          "value": "86rJ",
          "hidden": false
        },
        {
          "name": "选项",
          "value": "L4NO",
          "hidden": false
        }
      ],
      "allow_other": false
    },
    {
      "type": "single_choice",
      "label": "图片单选",
      "api_code": "field_6",
      "notes": "",
      "validations": {},
      "private": false,
      "choices": [
        {
          "name": "1-Cfl_B1ALQS7VQGj_iSiyPA",
          "value": "hd0C",
          "hidden": false,
          "image_url": "https://dn-jintest.qbox.me/ic/20161026140743_599030@iclarge"
        },
        {
          "name": "1-BJ1Jami58oxr5artYGaqDw",
          "value": "RWcw",
          "hidden": false,
          "image_url": "https://dn-jintest.qbox.me/ic/20161026140743_d3cd8d@iclarge"
        },
        {
          "name": "1-CbGEoJYT-DVnXx0_w-iEeg",
          "value": "25gu",
          "hidden": false,
          "image_url": "https://dn-jintest.qbox.me/ic/20161026140743_9f23fe@iclarge"
        }
      ]
    },
    {
      "type": "multiple_choice",
      "label": "图片多选",
      "api_code": "field_7",
      "notes": "",
      "validations": {},
      "private": false,
      "choices": [
        {
          "name": "1-DjFewMYwOJTfcAoDr8wgug",
          "value": "gb8K",
          "hidden": false,
          "image_url": "https://dn-jintest.qbox.me/ic/20161026140753_f10da1@iclarge"
        },
        {
          "name": "1-EcIXQpX2CoV36BiD8fPq9w",
          "value": "4cF4",
          "hidden": false,
          "image_url": "https://dn-jintest.qbox.me/ic/20161026140753_38905f@iclarge"
        },
        {
          "name": "1-G6gfugR9At7OlSj6YxIOKw",
          "value": "RP8o",
          "hidden": false,
          "image_url": "https://dn-jintest.qbox.me/ic/20161026140753_f7e716@iclarge"
        }
      ]
    },
    {
      "type": "likert",
      "label": "矩阵单选",
      "api_code": "field_8",
      "notes": "",
      "validations": {},
      "private": false,
      "choices": [
        {
          "name": "选项",
          "value": "OrdJ"
        },
        {
          "name": "选项",
          "value": "lIP4"
        },
        {
          "name": "选项",
          "value": "MSwM"
        }
      ],
      "statements": [
        {
          "name": "题目",
          "value": "vAfq"
        },
        {
          "name": "题目",
          "value": "owYy"
        },
        {
          "name": "题目",
          "value": "dl9C"
        }
      ]
    },
    {
      "type": "matrix",
      "label": "矩阵填空",
      "api_code": "field_9",
      "notes": "",
      "validations": {},
      "private": false,
      "statements": [
        {
          "name": "题目",
          "value": "lNIw"
        },
        {
          "name": "题目",
          "value": "KvbO"
        },
        {
          "name": "题目",
          "value": "dZIP"
        }
      ],
      "dimensions": [
        {
          "name": "项目",
          "value": "vWra"
        },
        {
          "name": "项目",
          "value": "SKOh"
        },
        {
          "name": "项目",
          "value": "1A1g"
        }
      ]
    },
    {
      "type": "number",
      "label": "数字",
      "api_code": "field_10",
      "notes": "",
      "validations": {},
      "predefined_value": null,
      "private": false,
      "display_as_percentage": false
    },
    {
      "type": "time",
      "label": "时间",
      "api_code": "field_11",
      "notes": "",
      "validations": {},
      "predefined_value": {},
      "private": false
    },
    {
      "type": "date",
      "label": "日期",
      "api_code": "field_12",
      "notes": "",
      "validations": {},
      "predefined_value": null,
      "private": false
    },
    {
      "type": "drop_down",
      "label": "下拉框",
      "api_code": "field_13",
      "notes": "",
      "validations": {},
      "private": false,
      "choices": [
        {
          "name": "选项",
          "value": "DdPZ",
          "hidden": false
        },
        {
          "name": "选项",
          "value": "WHHp",
          "hidden": false
        },
        {
          "name": "选项",
          "value": "nQs8",
          "hidden": false
        }
      ],
      "allow_other": false
    },
    {
      "type": "section_break",
      "label": "描述",
      "api_code": "field_14",
      "notes": "请在右侧面板添加段落说明信息"
    },
    {
      "type": "page_break",
      "label": null,
      "api_code": "field_15",
      "notes": ""
    },
    {
      "type": "link",
      "label": "网址",
      "api_code": "field_16",
      "notes": "填写示例：http://jinshuju.com 或 https://jinshuju.com",
      "validations": {},
      "predefined_value": null,
      "private": false
    },
    {
      "type": "rating",
      "label": "评分",
      "api_code": "field_17",
      "notes": "",
      "validations": {},
      "private": false,
      "rating_type": "star",
      "rating_max": 3
    },
    {
      "type": "cascade_drop_down",
      "label": "二级下拉框",
      "api_code": "field_18",
      "notes": "",
      "validations": {},
      "private": false,
      "choices": [
        {
          "name": "选项1",
          "value": "0TX9",
          "sub_choices": [
            {
              "name": "二级选项1",
              "value": "ecv0"
            },
            {
              "name": "二级选项2",
              "value": "SUKk"
            }
          ]
        },
        {
          "name": "选项2",
          "value": "dwpt",
          "sub_choices": [
            {
              "name": "二级选项1",
              "value": "R15F"
            },
            {
              "name": "二级选项2",
              "value": "k346"
            }
          ]
        }
      ]
    },
    {
      "type": "attachment",
      "label": "附件",
      "api_code": "field_19",
      "notes": "",
      "validations": {},
      "private": false,
      "max_file_quantity": 1,
      "media_type": {
        "type": "unlimited",
        "value": null
      }
    },
    {
      "type": "form_association",
      "label": "表单关联",
      "api_code": "field_20",
      "notes": "",
      "validations": {},
      "private": false,
      "associated_form_token": "ntZv4v",
      "associated_field_api_code": "serial_number"
    },
    {
      "type": "single_line_text",
      "label": "姓名",
      "api_code": "field_21",
      "notes": "",
      "validations": {},
      "predefined_value": null,
      "private": false
    },
    {
      "type": "mobile",
      "label": "手机",
      "api_code": "field_22",
      "notes": "",
      "validations": {},
      "predefined_value": null,
      "private": false
    },
    {
      "type": "email",
      "label": "邮箱",
      "api_code": "field_23",
      "notes": "",
      "validations": {},
      "private": false
    },
    {
      "type": "address",
      "label": "地址",
      "api_code": "field_24",
      "notes": "",
      "validations": {},
      "predefined_value": {},
      "private": false
    },
    {
      "type": "geo",
      "label": "地理位置",
      "api_code": "field_25",
      "notes": "",
      "validations": {},
      "private": false
    },
    {
      "type": "phone",
      "label": "电话",
      "api_code": "field_26",
      "notes": "",
      "validations": {},
      "predefined_value": null,
      "private": false
    },
    {
      "type": "goods",
      "label": "配图商品",
      "api_code": "field_27",
      "notes": "",
      "validations": {},
      "private": false,
      "with_image": true,
      "goods_items": [
        {
          "name": "1-EcIXQpX2CoV36BiD8fPq9w",
          "price": 0,
          "description": "",
          "api_code": "k6Bw",
          "inventory": null,
          "hidden": false,
          "predefined_value": {
            "number": null
          }
        },
        {
          "name": "1-G6gfugR9At7OlSj6YxIOKw",
          "price": 0,
          "description": "",
          "api_code": "mfXz",
          "inventory": null,
          "hidden": false,
          "predefined_value": {
            "number": null
          }
        },
        {
          "name": "1-DjFewMYwOJTfcAoDr8wgug",
          "price": 0,
          "description": "",
          "api_code": "7QWj",
          "inventory": null,
          "hidden": false,
          "predefined_value": {
            "number": null
          }
        }
      ]
    },
    {
      "type": "goods",
      "label": "无图商品",
      "api_code": "field_28",
      "notes": "",
      "validations": {},
      "private": false,
      "with_image": false,
      "goods_items": [
        {
          "name": "商品一",
          "price": 0,
          "description": "",
          "api_code": "jQaM",
          "inventory": null,
          "hidden": false,
          "predefined_value": {
            "number": null
          }
        },
        {
          "name": "商品二",
          "price": 0,
          "description": "",
          "api_code": "Ba3h",
          "inventory": null,
          "hidden": false,
          "predefined_value": {
            "number": null
          }
        },
        {
          "name": "商品三",
          "price": 0,
          "description": "",
          "api_code": "cpLm",
          "inventory": null,
          "hidden": false,
          "predefined_value": {
            "number": null
          }
        }
      ]
    }
  ],
  "setting": {
    "icon": "form-icon-photo",
    "color": "#46B372",
    "open_rule": "open",
    "permission": "public",
    "gen_code_enabled": false,
    "result_state": "closed",
    "result_url": null,
    "search_state": "closed",
    "search_url": null,
    "push_url": null,
    "success_redirect_url": "https://www.XXX.com",
    "success_redirect_fields": [
      "serial_number"
    ]
  }
}
```

### 复制表单

注意：示例中的<2d4iH0>为被复制的表单token

    POST https://api.jinshuju.com/v4/forms/2d4iH0/copy
### 复制表单

参数

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token  | string | **必须**，可使用2.1中的个人access token，或2.2中的企业access token。
openid  | string | **可选**，获取的用户列表中的openid。使用个人的acces token无需填写；使用企业的access token必须填写。
name  | string | **可选**，复制出来的表单命名。如果不提供或者为空字符串，将使用“[新]”+原表单名作为复制后的表单的名字。 

默认情况下，返回的response的形式如下：

````json
{
  "id": "58512d8159601539b83e75fa",
  "token": "AyEpBI",
  "name": "[新]学习小组第一期话题投票",
  "entries_count": 0,
  "shared": false,
  "description": "这是一个大家都可以加入或旁听的学习小组，",
  "created_at": "2016-12-14T11:31:14.255Z",
  "updated_at": "2016-12-14T11:31:15.325Z",
  "fields": [
    {
      "type": "single_line_text",
      "label": "你的大名",
      "api_code": "field_1",
      "notes": "",
      "validations": {},
      "predefined_value": null,
      "private": false
    },
    {
      "type": "multiple_choice",
      "label": "请选择你喜欢的话题",
      "api_code": "field_2",
      "notes": "",
      "validations": {},
      "private": false,
      "choices": [
        {
          "name": "如何理解产品经理这个角色",
          "value": "gRpI",
          "hidden": false
        },
        {
          "name": "如何提高碎片化阅读的效率",
          "value": "gmIV",
          "hidden": false
        },
        {
          "name": "利用Excel进行数据分析的技巧",
          "value": "qTXj",
          "hidden": false
        },
        {
          "name": "如何快速阅读一本书",
          "value": "OASQ",
          "hidden": false
        },
        {
          "name": "QA进阶。相信我，你们都不需要入门",
          "value": "H2YM",
          "hidden": false
        },
        {
          "name": "《无价》读书心得分享",
          "value": "Ud2N",
          "hidden": false
        },
        {
          "name": "设计中关于字体的二三事",
          "value": "V2nl",
          "hidden": false
        },
        {
          "name": "金数据产品运行的基本原理。",
          "value": "BxvN",
          "hidden": false
        },
        {
          "name": "金数据设计的基本原则和思考",
          "value": "rQAm",
          "hidden": false
        }
      ],
      "allow_other": false
    }
  ],
  "setting": {
    "icon": "form-icon-chart",
    "color": "#BB87AF",
    "open_rule": "open",
    "permission": "public",
    "result_state": "closed",
    "result_url": null,
    "search_state": "closed",
    "search_url": null,
    "push_url": null,
    "success_redirect_url": null,
    "success_redirect_fields": []
  }
}
````

### 获取表单当前状态

需要Scope: `forms`

    GET https://api.jinshuju.com/v4/forms/RygpW3/status?access_token=...

```json
{
    "is_open": true,
    "permission": "public",
    "entries_count": 60
}
```

### 获取多条数据

需要Scope： `read_entries`

    GET https://api.jinshuju.com/v4/forms/RygpW3/entries?access_token=...

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
        "field_17": "roody@jinshuju.com",
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
`http://api.jinshuju.com/v4/forms/aJSON8/entries?access_token=<token>&field_1=xxx`
*注：获取表单结构中暂无商品字段的item信息*
*注：全匹配查询，不支持模糊查询*

* 同一字段的多个条件取并集查询
`http://api.jinshuju.com/v4/forms/aJSON8/entries?access_token=<token>&field_1[]=xxx&field_1[]=yyy`

* 多个字段取交集查询
`http://api.jinshuju.com/v4/forms/aJSON8/entries?access_token=<token>&field_1=xxx&field_2=yyy`

* 矩阵单选查询
`http://api.jinshuju.com/v4/forms/aJSON8/entries?access_token=<token>&field_1[<题目code>][]=<选项code>`

* 二级下拉框查询
`http://api.jinshuju.com/v4/forms/aJSON8/entries?access_token=<token>&field_1[<一级选项code>]=<二级选项code>`

* 微信省市查询
`http://api.jinshuju.com/v4/forms/aJSON8/entries?access_token=<token>&x_field_weixin_province_city[陕西]=西安`

* 数据提交时间查询
指定日期：`http://api.jinshuju.com/v4/forms/aJSON8/entries?access_token=<token>&created_at=2016-1-22`
某一日期之后：`http://api.jinshuju.com/v4/forms/aJSON8/entries?access_token=<token>&created_at[start]=2016-1-22`
某一日期之前：`http://api.jinshuju.com/v4/forms/aJSON8/entries?access_token=<token>&created_at[end]=2016-1-22`
某个日期区间：`http://api.jinshuju.com/v4/forms/aJSON8/entries?access_token=<token>&created_at[start]=2015-12-15&created_at[end]=2016-1-22`

### 获取单条数据

需要Scope: `read_entries`

    GET https://api.jinshuju.com/v4/forms/RygpW3/entries/<序列号>?access_token=...

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
    "field_17": "roody@jinshuju.com",
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
### 获取、更新表单设置

需要Scope: `form_setting`

#### 获取表单设置

    get https://api.jinshuju.com/v4/forms/RygpW3/setting?access_token=...

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

    put https://api.jinshuju.com/v4/forms/RygpW3/setting

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token    | string | **必须**,可使用2.1中的个人access token，或2.2中的企业access token。
success_redirect_url | string | 提交成功后的跳转网址
success_redirect_fields | string | 提交成功后的跳转网址附加字段参数，以及提交给该字段的信息，最多三个参数，多个参数以空格分隔，如"serial_number x_field_1"，超过三个参数会回应报错信息。参数必须为表单里字段，会自动过滤非表单字段，目前支持：序号、单/多行文本、单选、多选、数字、邮箱、电话、日期以及网址等字段。如果表单中包含商品字段，则还可以附带序号和总价。可参考 https://help.jinshuju.net/articles/redirect-with-params.html
push_url | string | 数据以JSON格式推送的网址，使用请参考https://help.jinshuju.net/articles/http-push




#### 为表单添加协作成员
    
需要Scope: `forms`

    POST https://api.jinshuju.com/v4/forms/RygpW3/cooperators?access_token=...

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token    | string | **必须**,可使用2.1中的个人access token，或2.2中的企业access token。
openid | string | **必须**，获取的用户列表中的openid。这里需填写加为协作成员的用户openid。
role | string | **必须**，指定的角色，仅支持 manager, data_maintainer, data_viewer。




#### 为表单变更协作成员角色
    
需要Scope: `forms`

    PUT https://api.jinshuju.com/v4/forms/RygpW3/cooperators/<openid>?access_token=...

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token    | string | **必须**,可使用2.1中的个人access token，或2.2中的企业access token。
role | string | **必须**，指定的角色，仅支持 manager, data_maintainer, data_viewer。






#### 为表单移除协作成员
    
需要Scope: `forms`

    DELETE https://api.jinshuju.com/v4/forms/RygpW3/cooperators/<openid>?access_token=...

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token    | string | **必须**,可使用2.1中的个人access token，或2.2中的企业access token。






#### 删除表单
    
需要Scope: `forms`

    DELETE https://api.uat.jinshuju.com/v4/forms/YYtYiX?access_token=...

参数名称  | 类型  | 备注
------------- | ------------- | -----------
access_token    | string | **必须**,可使用2.1中的个人access token，或2.2中的企业access token。




