
# 开发文档

## 目录

- [开发文档](#开发文档)
  - [目录](#目录)
- [开发前必读](#开发前必读)
  - [简介](#简介)
  - [通用说明](#通用说明)
    - [接口使用前提](#接口使用前提)
  - [使用规则](#使用规则)
- [通道编码](#通道编码)
- [订单管理](#订单管理)
  - [统一下单【签名】](#统一下单签名)
    - [接口概述](#接口概述)
    - [签名字符串](#签名字符串)
    - [请求参数](#请求参数)
    - [响应参数](#响应参数)
    - [响应实例](#响应实例)
      - [请求成功](#请求成功)
      - [请求失败](#请求失败)
    - [错误码](#错误码)
  - [查询订单【签名】](#查询订单签名)
    - [接口概述](#接口概述-1)
    - [签名字符串](#签名字符串-1)
    - [请求参数](#请求参数-1)
    - [响应参数](#响应参数-1)
    - [响应实例](#响应实例-1)
      - [请求成功](#请求成功-1)
      - [请求失败](#请求失败-1)
    - [错误码](#错误码-1)
- [异步通知](#异步通知)
  - [参数说明](#参数说明)
  - [签名字符串](#签名字符串-2)
- [更新日志](#更新日志)

# 开发前必读
## 简介
## 通用说明
### 接口使用前提
本平台支持sign模式，请开发之前仔细阅读文档

```
### 签名规则
```
该方式用于直接采用MD5签名的方式
```

#### 签名算法

##### 第一步
设所有发送或者接收到的数据为集合M，将集合M内非空参数值的参数按照参数名ASCII码从小到大排序（字典序），使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串stringA。

特别注意以下重要规则：
- 参数名ASCII码从小到大排序（字典序）；
- 参数名区分大小写；
- 商户传送的sign参数不参与签名，平台主动通知传送的sign参数不参与签名
- 接口可能增加字段，验证签名时必须支持增加的扩展字段

##### 第二步
在stringA最后拼接上key得到stringSignTemp字符串，并对stringSignTemp进行MD5运算，再将得到的字符串所有字符转换为小写，得到sign值。

举例：
假设传送的参数如下：
```
appid： wxd930ea5d5a258f4f
mch_id： 888888
device_info： 876543
body： body
nonce_str： ABCDEFG
```

对参数按照key=value的格式，并按照参数名ASCII字典序排序如下：
```go
stringA="appid=wxd930ea5d5a258f4f&body=body&device_info=876543&mch_id=888888&nonce_str=ABCDEFG";
```

拼接API密钥：
```go
stringSignTemp=stringA + "&key=api_key" //注：api_key为商户平台设置的密钥key
```


## 使用规则
欢迎您使用四方支付（下称 “产品”）。在您正式开通接口服务之前，请您务必审慎阅读、充分理解在使用四方支付接口时应当遵循的规则。您使用四方支付接口服务即视为您已阅读并同意本规则全部内容的约束。

1. 四方支付 API 接口服务为商户提供订单 webhook 接口配置等技术服务，具体服务内容请见 接口文档（下称 “服务”）。

2. 使用限制：每个商户账号的 mch_id 调用开放接口的频率不可超过 20 次 / 秒，否则可能会因高频调用而返回错误。


3. 四方支付特别提醒您应妥善保管您的应用凭证（包括 mch_id 和 secret）。四方支付与您共同负有维护应用凭证安全的责任。四方支付会采取并不断更新技术措施，努力保护您的应用凭证在服务器端的安全。您需要采取特定措施保护您的应用凭证安全。您不得将四方支付的应用凭证以任何方式与第三方共享。因您保管不善可能导致应用凭证被他人使用或共享秘钥引起的损失，责任由您自行承担。

4. 在您怀疑他人在使用您的应用凭证时，您同意立即通知四方支付。如果您当前使用的应用凭证并不是您初始申请开通的或者通过四方支付提供的其他途径获得的，但您却知悉该应用凭证，您不得用该应用凭证进行任何操作，并请您在第一时间通知四方支付。



# 通道编码
| 通道名     | 通道号 |
| ---------- | :----: |
| 支付宝原生 |   4    |



# 订单管理
[返回目录](#目录)
## 统一下单【签名】
### 接口概述
- 功能: 统一下单
- 请求方式: POST
- 请求地址: /v1/api/pay/sign/unifiedorder
### 签名字符串
```
channel_no=%d&client_ip=%s&mch_id=%d&money=%s&notify_url=%s&out_order_no=%s&out_username=%s&param=%s&payer_name=%s&return_url=%s&subject=%s&timestamp=%d&key=%s
```
### 请求参数
| 参数名       | 类型   | 是否必须 | 描述                                                           | 示例值                                  |
| ------------ | ------ | :------: | -------------------------------------------------------------- | --------------------------------------- |
| sign         | string |    是    | 签名                                                           | aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa        |
| mch_id       | int    |    是    | 商户编号                                                       | 1000010                                 |
| channel_no   | string |    是    | 不选择渠道时单独传通道编码，选择渠道时，逗号分割               | 1/ 1,wai1                               |
| subject      | string |    否    | 标题                                                           | subject                                 |
| out_order_no | string |    是    | 商户订单号                                                     | 20150320010101001                       |
| out_username | string |    否    | 商户会员用户名，用于后台展示用                                 | kehu1                                   |
| money        | string |    是    | 金额，单位为元，精确到小数点后两位                             | 1000                                    |
| client_ip    | string |    是    | 客户IP                                                         | 0.0.0.0                                 |
| notify_url   | string |    是    | 异步通知地址，支付成功后将支付成功消息以POST请求发送给这个网址 | http://www.demo.com/recieve_notice.html |
| return_url   | string |    否    | 支付成功后跳转地址                                             | http://www.demo.com/paysucc.html        |
| param        | string |    否    | 透传参数                                                       | xxxxxxxxxxxxxxxxx                       |
| payer_name   | string |    否    | 付款人姓名，传入以后，付款人必须匹配                           | 马化腾                                  |
| timestamp    | int64  |    是    | 发送请求的时间戳,13位带毫秒                                    | 1626863144831                           |

### 响应参数
| 参数名            | 类型   | 描述               |
| ----------------- | ------ | ------------------ |
| mch_id            | int    | 商户编号           |
| order_no          | string | 平台订单号         |
| out_order_no      | string | 商户订单号         |
| money             | string | 订单金额           |
| real_money        | string | 真实的订单金额     |
| pay_url           | string | 支付链接           |
| expired_time      | string | 过期时间           |
| expired_timestamp | int64  | 过期时间时间戳毫秒 |

### 响应实例
#### 请求成功
```json
{
    "code":0,
    "msg":"ok",
    "data":{
        "mch_id":"1000123",
        "order_no":"202204157885214563228",
        "out_order_no":"20150320010101001",
        "money":"1000.00",
        "pay_url":"https://www.demo.com/#/?order_no=202204157885214563228",
        "expired_time":"2022-07-25 20:41:01",
        "expired_timestamp":1658752861000
    },
    "request_id":"ddec96d2165e4f3e8a642057db116983"
}
```

#### 请求失败

```json
{
    "code":2,
    "msg":"InvalidArgument",
    "data":{},
    "request_id":"ddec96d2165e4f3e8a642057db116983"
}
```

### 错误码
| 错误代码 | 错误描述                                   |
| -------- | ------------------------------------------ |
| 1        | 找不到数据                                 |
| 2        | token校验不通过                            |
| 4        | 账号状态异常                               |
| 9        | 插入数据库失败                             |
| 10       | 插入数据库失败                             |
| -1       | money 参数有误                             |
| -2       | channel_no 参数有误                        |
| -3       | out_order_no 参数有误                      |
| -4       | 找不到合适的银行卡，没有配置或者限额已超过 |
| -5       | 未开通对应通道的频道信息                   |



[返回目录](#目录)
## 查询订单【签名】
### 接口概述
- 功能: 订单查询
- 请求方式: POST
- 请求地址: /v1/api/pay/sign/query
### 签名字符串
```
channel_no=2&mch_id=%d&order_no=%s&timestamp=%d&key=%s
``` 
### 请求参数
| 参数名       | 类型   | 是否必须 | 描述                         | 示例值                           |
| ------------ | ------ | :------: | ---------------------------- | -------------------------------- |
| sign         | string |    是    | 签名                         | aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa |
| channel_no   | int    |    是    | 通道号                       | 2                                |
| mch_id       | int    |    是    | 商户编号                     | 1000010                          |
| order_no     | string |    是    | 订单号,与out_order_no二选一  | 2015042321001004720              |
| out_order_no | string |    是    | 商户订单号, 与order_no二选一 | YY2015042321001004720            |
| timestamp    | int    |    是    | 时间戳,13位带毫秒            | 1626863144831                    |

### 响应参数
| 参数名            | 类型   | 描述                                                           |
| ----------------- | ------ | -------------------------------------------------------------- |
| mch_id            | int    | 商户号                                                         |
| order_no          | string | 平台订单号                                                     |
| out_order_no      | string | 商户订单号                                                     |
| state             | int    | 状态	0=未出码,1=交易失败,2=待支付,3=交易成功                   |
| money             | string | 订单金额                                                       |
| notify_succ       | int    | 1=通知成功，0=通知失败                                         |
| expired_time      | string | 过期时间                                                       |
| expired_timestamp | string | 过期时间时间戳毫秒                                             |
| notify_url        | string | 异步通知地址，支付成功后将支付成功消息以POST请求发送给这个网址 |
| pay_url           | string | 支付地址                                                       |

### 响应实例
#### 请求成功
```json
{
    "code":0,
    "msg":"ok",
    "data":{
        "mch_id":"1000122",
        "order_no":"2015042321001004720",
        "out_order_no":"20150320010101001",
        "state":0,
        "money":"1000.00",
        "notify_succ":"1",
        "expired_time":"2022-07-25 20:41:01",
        "expired_timestamp":"1658752861000",
        "notify_url":"https://your.domain.com/",
        "pay_url":"https://www.demo.com/#/?order_no=202204157885214563228",
    },
    "request_id":"ddec96d2165e4f3e8a642057db116983"
}
```

#### 请求失败

```json
{
    "code":1,
    "msg":"InvalidArgument",
    "data":{},
    "request_id":"ddec96d2165e4f3e8a642057db116983"
}
```

### 错误码
| 错误代码 | 错误描述          |
| -------- | ----------------- |
| 1        | 找不到数据        |
| 2        | 参数校验不通过    |
| -1       | order_no 参数有误 |


[返回目录](#目录)
# 异步通知
```
该链接是通过【统一下单API】中提交的参数notify_url设置，如果链接无法访问，商户将无法接收到通知。

通知url必须为直接可访问的url，不能携带参数。示例：notify_url：“https://pay.demo.com/pay.html”

该通知以POST方式请求，编码：UTF8，JSON格式数据流

商户接收到通知后，返回“SUCCESS”字符串，平台将不再通知(SUCCESS 使用大写字母)；如果没有反馈，平台将在10分钟内，通知6次，之后将不再主动发起通知
```
## 参数说明
| 参数名       | 类型   | 描述                                | 示例值              |
| ------------ | ------ | ----------------------------------- | ------------------- |
| mch_id       | int    | 商户编号                            | 1000010             |
| money        | string | 订单金额                            | 1000.00             |
| real_money   | string | 实际金额                            | 1000.00             |
| notify_time  | string | 通知时间                            | 2006-01-02 15:04:05 |
| order_no     | string | 平台订单号                          | 2015042321001004720 |
| out_order_no | string | 商户订单号                          | CZX00901239888173   |
| param        | string | 透传参数                            | xxxxxxxxxxxxxxx     |
| state        | int    | 1未出码,2待支付,3交易成功,4交易失败 | 1                   |
| sign         | string | 签名，详情见签名规则                |                     |

## 签名字符串
```
mch_id=%d&money=%s&notify_time=%s&order_no=%s&out_order_no=%s&param=%s&real_money=%s&state=%d&key=%s
```

# 更新日志
2023-07-31日创建
