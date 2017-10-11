# 金数据企业版API `org_client_credentials` 授权方式

金数据企业版API使用OAuth 2标准进行用户验证，一般情况下默认使用`Authorization Grant`来获取`Access Token`

参考OAuth 2.0标准：Authorization Grant https://tools.ietf.org/html/rfc6749#section-1.3

`Authorization Grant` 授权方式一般要求用户使用浏览器（或类似方式）访问授权网页，手工登陆后网页重定向，client端获取 `authorization code` 后再请求 `Access Token`。这种方式安全性较高，但是流程较复杂，一般需要人工交互来授权。

为了方便企业用户开发企业内部自用的应用，金数据企业版API另一种授权方式：`org_client_credentials`。使用 `org_client_credentials` 可以直接通过一个 HTTP 请求获取 `access token`。

需要注意的是，`org_client_credentials` 方式获取到的 `access token` 是企业级别的，与个体用户无关。其 `scope` 也是应用全部的 `scope`。这意味着，该 `access token` 可以访问到应用对应企业的所有数据以及应用具备的所有操作权限。


## `org_client_credentials` 授权流程

要发起 `org_client_credentials` 授权流程，发送 `POST`请求到 `https://account.jinshuju.com/oauth/token` 地址

```
POST /oauth/token
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded;charset=UTF-8
grant_type=org_client_credentials
```

__注意，`grant_type` 要设置为 `org_client_credentials`__

Ruby 代码参考如下：

```
require 'rest-client'
require 'json'

client_id = '4ea1b...'
client_secret = 'a2982...'

response = RestClient.post 'https://account.jinshuju.com/oauth/token', {
  grant_type: 'org_client_credentials',
  client_id: client_id,
  client_secret: client_secret
}
```

response 中将包含 `access token` 相关数据：

```
{
  "access_token": "5df8c2f......",
  "token_type": "bearer",
  "expires_in": 7200,
  "scope": "public forms read_entries write_entries form_setting users",
  "created_at": 1507716930
}
```

获取到 `access token` 后，后续流程和 API 使用方法相同。参见文档： https://github.com/jinshuju/jinshuju-api-docs/blob/master/enterprise-api.md#3-使用access-token访问api


