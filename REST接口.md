<!-- TOC -->

- [基础信息](#基础信息)
  - [服务地址](#服务地址)
  - [常见错误码](#常见错误码)
- [签名认证](#签名认证)
  - [签名说明](#签名说明)
- [用户划转和充值和提币接口](#用户划转和充值和提币接口)
  - [用户万向划转](#用户万向划转)
  - [查询用户万向划转历史](#查询用户万向划转历史)
  - [获取充值历史(支持多网络)](#获取充值历史(支持多网络))
  - [获取提币历史 (支持多网络)](#获取提币历史(支持多网络))
- [鉴权服务](#鉴权服务)
  - [生成ListenKey](#生成-Listen-Key)
  - [延长ListenKey有效期](#延长-Listen-Key-有效期)
  - [关闭ListenKey](#关闭-Listen-Key)

<!-- TOC -->

# 基础信息
## 服务地址

https://open-api.bingx.com

HTTP状态码200表示成功响应，并可能包含内容。如果响应含有内容，则将显示在相应的返回内容里面。

## 服务申请

目前API内测中，申请页面即将开放，请耐心等待。如有其他需求，欢迎联系客服。

## 常见错误码

**常见HTTP错误码**

* 4XX 错误码用于指示错误的请求内容、行为、格式

* 5XX 错误码用于指示服务侧的问题

**常见业务错误码**

* 100001 - 签名验证失败

* 100202 - 余额不足

* 100400 - 参数错误

* 100440 - 下单价格跟市场市场价格偏离太远

* 100500 - 服务器内部错误

* 100503 - 服务器繁忙

**注意**:
* 如果失败，response body 带有错误描述信息

* 每个接口都有可能抛出异常

# 签名认证
请求需要鉴权的接口必须包含以下信息：

* 请求头带上 `X-BX-APIKEY` 传递 `apiKey`。
* 请求参数带上 `signature` 使用签名算法得出的签名。
* 请求参数带上 `timestamp` 作为请求的时间戳，单位是毫秒。服务器收到请求时会判断请求中的时间戳，如果是5000毫秒之前发出的，则请求会被认为无效。这个时间空窗值可以通过发送可选参数`recvWindow`来定义。

## 签名说明
`signature` 请求参数使用 **HMAC SHA256** 方法加密而得到的。

**例如：对于下单请求参数进行签名**
- 接口参数:
```
quoteOrderQty = 20
side = BUY
symbol = ETH-USDT
timestamp = 1649404670162
type = MARKET
```
- api信息:
```
apiKey = Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU
secretKey = UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI
```
- 参数通过`query string`发送示例
```
1. 对接口参数进行拼接: quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET
2. 对拼接好的参数字符串使用secretKey生成签名: 428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827
   echo -n "quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET" | openssl dgst -sha256 -hmac "UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI" -hex
3. 发送请求: curl -H 'X-BX-APIKEY: Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU' 'https://open-api.bingx.com/openApi/spot/v1/trade/order?quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET&signature=428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827'
```
- 参数通过`request body`发送示例
```
1. 对接口参数进行拼接: quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET
2. 对拼接好的参数字符串使用secretKey生成签名: 428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827
   echo -n "quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET" | openssl dgst -sha256 -hmac "UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI" -hex
3. 发送请求: curl -H 'X-BX-APIKEY: Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU' -X POST 'https://open-api.bingx.com/openApi/spot/v1/trade/order' -d 'quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET&signature=428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827'
```
- 参数通过`query string`和`request body`发送示例
```
queryString: quoteOrderQty=20&side=BUY&symbol=ETHUSDT
requestBody: timestamp=1649404670162&type=MARKET



1. 对接口参数进行拼接: quoteOrderQty=20&side=BUY&symbol=ETHUSDTtimestamp=1649404670162&type=MARKET
2. 对拼接好的参数字符串使用secretKey生成签名: 94e0b4925060a615e1e372d4c929015d4b59d3c89067dc0beeafcfb33a6d8d10
   echo -n "quoteOrderQty=20&side=BUY&symbol=ETHUSDTtimestamp=1649404670162&type=MARKET" | openssl dgst -sha256 -hmac "UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI" -hex
3. 发送请求: curl -H 'X-BX-APIKEY: Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU' -X POST 'https://open-api.bingx.com/openApi/spot/v1/trade/order?quoteOrderQty=20&side=BUY&symbol=ETHUSDT' -d 'timestamp=1649404670162&type=MARKET&signature=94e0b4925060a615e1e372d4c929015d4b59d3c89067dc0beeafcfb33a6d8d10'
```

# 权限接口

## 查询用户API Key权限

**接口**
```
    GET /openApi/v1/account/apiRestrictions
```

**参数**

| 参数名          | 类型     | 是否必填 | 备注                |
| ------         | ------  |------|-------------------|
| timestamp         | LONG  | 是    | 当前时间戳  例如1658748648396         |

CURL

```
curl --location --request GET 'https://open-api.bingx.com/openApi/v1/account/apiRestrictions?timestamp=1670215150028&signature=ecc819d72515095039b7b383310f718584af4cf70106b57609bc59473185c9a3'
```
**响应**

| 参数名                | 类型      | 备注   |
| ------               |---------|------|    
| ipRestrict              | Boolean | 是否限制ip访问 |
| createTime              | Long    | 创建时间 |
| permitsUniversalTransfer              | Boolean  | 授权该密钥可用于专用的万向划转接口 |
| enableReading              | Boolean  | 是否能读取 |
| enableFutures              | Boolean  | 合约交易权限 |
| enableSpotAndMarginTrading              | Boolean  | 现货权限 |



```
{
    "ipRestrict":false,
    "createTime":1671262117327,
    "permitsUniversalTransfer":false,
    "enableReading":true,
    "enableFutures":false,
    "enableSpotAndMarginTrading":false
}
```

# 用户划转和充值和提币接口


## 用户万向划转

**接口**
```
    GET /openApi/api/v3/get/asset/transfer
```

**参数**

| 参数名          | 类型     | 是否必填 | 备注                |
| ------         | ------  |------|-------------------|    
| type         | ENUM  | 是    | 划转类型              |
| asset         | STRING  | 是    | 币的名称 例如USDT       |
| amount         | DECIMAL  | 是    | 交易金额              |
| recvWindow         | LONG  | 否    | 执行窗口时间，不能大于 60000 |
| timestamp         | LONG  | 是    | 当前时间戳  例如1658748648396         |

```
   
      目前支持的type划转类型:
     
      FUND_SFUTURES，资金账户->标准合约
     
      SFUTURES_FUND，标准合约->资金账户
     
      FUND_PFUTURES，资金账户->专业合约
     
      PFUTURES_FUND，专业合约->资金账户
     
      SFUTURES_PFUTURES，标准合约->专业合约
     
      PFUTURES_SFUTURES，专业合约->标准合约
   
```

CURL

```
curl --location --request GET 'https://open-api.bingx.com/openApi/api/v3/get/asset/transfer?type=FUND_PFUTURES&asset=USDT&amount=100&timestamp=1670215150028&signature=ecc819d72515095039b7b383310f718584af4cf70106b57609bc59473185c9a3'
```

**响应**

| 参数名                | 类型     | 备注   |
| ------               | ------  |------|    
| tranId              | LONG  | 交易ID |


```
{
    "tranId":13526853623
}
```



## 查询用户万向划转历史

**接口**
```
    GET /openApi/api/v3/asset/transfer
```

**参数**

| 参数名          | 类型   | 是否必填 | 备注                   |
| ------         |------|------|----------------------|    
| type         | ENUM | 是    | 划转类型                 |
| startTime         | LONG | 否    | 开始时间 1658748648396   |
| endTime         | LONG | 否    | 结束时间   1658748648396 |
| current         | Int | 否    | 当前页 默认1              |
| size         | Int  | 否    | 页数量大小 默认10 不能超过100   |
| recvWindow         | LONG | 否    | 执行窗口时间，不能大于 60000    |
| timestamp         | LONG | 是    | 当前时间戳 1658748648396  |

```
   
      目前支持的type划转类型:
     
      FUND_SFUTURES，资金账户->标准合约
     
      SFUTURES_FUND，标准合约->资金账户
     
      FUND_PFUTURES，资金账户->专业合约
     
      PFUTURES_FUND，专业合约->资金账户
     
      SFUTURES_PFUTURES，标准合约->专业合约
     
      PFUTURES_SFUTURES，专业合约->标准合约
   
```
**响应**

| 参数名                | 类型      | 备注        |
| ------               |---------|-----------|    
| total              | LONG    | 总数        |
| rows              | Array   | 数据Array   |
| asset              | String  | 币的名称      |
| amount              | DECIMAL | 币的金额      |
| type              | ENUM    | 划转类型      |
| status              | String  | CONFIRMED |
| tranId              | LONG    | 交易id      |
| timestamp              | LONG    | 划转的时间戳    |



```
{
    "total":3,
    "rows":[
        {
            "asset":"USDT",
            "amount":"-100.00000000000000000000",
            "type":"FUND_SFUTURES",
            "status":"CONFIRMED",
            "tranId":1067594500957016069,
            "timestamp":1658388859000
        },
        {
            "asset":"USDT",
            "amount":"-33.00000000000000000000",
            "type":"FUND_SFUTURES",
            "status":"CONFIRMED",
            "tranId":1069064111112065025,
            "timestamp":1658739241000
        },
        {
            "asset":"USDT",
            "amount":"-100.00000000000000000000",
            "type":"FUND_SFUTURES",
            "status":"CONFIRMED",
            "tranId":1069099446076444674,
            "timestamp":1658747666000
        }
    ]
}
```



## 获取充值历史(支持多网络)

**接口**
```
    GET /openApi/api/v3/capital/deposit/hisrec
```

**参数**

| 参数名          | 类型     | 是否必填 | 备注                                                       |
| ------         |--------|-----|----------------------------------------------------------|    
| coin         | String | 否   | 币的名称                                                     |
| status         | Int    |否    | 状态(0:pending,6: credited but cannot withdraw, 1:success) |
| startTime         | LONG   | 否   | 开始时间   1658748648396                                     |
| endTime         | LONG   | 否   | 结束时间   1658748648396                                     |
| offset         | Int    | 否   | 偏移 默认0                                                   |
| limit         | Int    | 否   | 页数量大小 默认1000 不能超过1000                                    |
| recvWindow         | LONG   | 否   | 执行窗口时间，不能大于 60000                                        |
| timestamp         | LONG   | 是   | 当前时间戳 1658748648396                                      |

**响应**

| 参数名                | 类型      | 备注       |
| ------               |---------|----------|    
| amount              | DECIMAL | 充值金额     |
| coin              | String  | 币名称      |
| network              | String  | 充值网络     |
| status              | Int     | 状态 状态 0-已确认-10-待确认(审核中) 20-已申请区块 30已审核通过 40审核不通过 50已汇出 60充值初步确认(最终确认变为0) 70审核不通过已退回资产      |
| address              | String  | 充值地址     |
| addressTag              | String  | 备注       |
| txId              | LONG    | 交易id     |
| insertTime              | LONG    | 交易时间     |
| transferType              | LONG    | 交易类型0=充值 |
| unlockConfirm              | LONG    | 解锁需要的网络确认次数   |
| confirmTimes              | LONG    | 网络确认次数   |


```
[
    {
        "amount":"0.00999800",
        "coin":"PAXG",
        "network":"ETH",
        "status":1,
        "address":"0x788cabe9236ce061e5a892e1a59395a81fc8d62c",
        "addressTag":"",
        "txId":"0xaad4654a3234aa6118af9b4b335f5ae81c360b2394721c019b5d1e75328b09f3",
        "insertTime":1599621997000,
        "transferType":0,
        "unlockConfirm":"12/12", // 解锁需要的网络确认次数
        "confirmTimes":"12/12"
    },
    {
        "amount":"0.50000000",
        "coin":"IOTA",
        "network":"IOTA",
        "status":1,
        "address":"SIZ9VLMHWATXKV99LH99CIGFJFUMLEHGWVZVNNZXRJJVWBPHYWPPBOSDORZ9EQSHCZAMPVAPGFYQAUUV9DROOXJLNW",
        "addressTag":"",
        "txId":"ESBFVQUTPIWQNJSPXFNHNYHSQNTGKRVKPRABQWTAXCDWOAKDKYWPTVG9BGXNVNKTLEJGESAVXIKIZ9999",
        "insertTime":1599620082000,
        "transferType":0,
        "unlockConfirm":"1/12",
        "confirmTimes":"1/1"
    }
]
```


## 获取提币历史(支持多网络)

**接口**
```
    GET /openApi/api/v3/capital/withdraw/history
```

**参数**

| 参数名          | 类型     | 是否必填 | 备注                                                    |
| ------         |--------|-----|-------------------------------------------------------|    
| coin         | String | 否   | 币的名称                                                  |
| withdrawOrderId         | String | 否   | 自定义ID, 如果没有则不返回该字段                                    |
| status         | Int    |否    | 状态 (0:已发送确认Email, 2:等待确认 3:被拒绝 4:处理中 5:提现交易失败 6 提现完成) |
| startTime         | LONG   | 否   | 开始时间   1658748648396                                  |
| endTime         | LONG   | 否   | 结束时间   1658748648396                                  |
| offset         | Int    | 否   | 偏移 默认0                                                |
| limit         | Int    | 否   | 页数量大小 默认1000 不能超过1000                                 |
| recvWindow         | LONG   | 否   | 执行窗口时间，不能大于 60000                                     |
| timestamp         | LONG   | 是   | 当前时间戳 1658748648396                                   |

**响应**

| 参数名           | 类型      | 备注                 |
|---------------|---------|--------------------|    
| address       | String  | 地址                 |
| amount        | DECIMAL | 提现转出金额             |
| applyTime       | Date    | 充值时间               |
| coin        | String  | 币名称                |
| id       | String  | 该笔提现的id            |
| withdrawOrderId    | String  | 自定义ID, 如果没有则不返回该字段 |
| network          | String  | 提现网络               |
| transferType    | Int     | 交易类型1=提现           |
| status  | Int     | 状态 状态 0-已确认-10-待确认(审核中) 20-已申请区块 30已审核通过 40审核不通过 50已汇出 60充值初步确认(最终确认变为0) 70审核不通过已退回资产                |
| transactionFee | String  | 手续费                |
| confirmNo  | Int     | 提现确认次数             |
| info  | String  | 提币失败原因             |
| txId  | String  | 提现交易id             |



```
[
    {
        "address": "0x94df8b352de7f46f64b01d3666bf6e936e44ce60",
        "amount": "8.91000000",   // 提现转出金额
        "applyTime": "2019-10-12 11:12:02",  
        "coin": "USDT",
        "id": "b6ae22b3aa844210a7041aee7589627c",  // 该笔提现的id
        "withdrawOrderId": "WITHDRAWtest123", // 自定义ID, 如果没有则不返回该字段
        "network": "ETH",
        "transferType": 0 
        "status": 6,
        "transactionFee": "0.004", // 手续费
        "confirmNo":3,  // 提现确认数
        "info": "The address is not valid. Please confirm with the recipient",  // 提币失败原因
        "txId": "0xb5ef8c13b968a406cc62a93a8bd80f9e9a906ef1b3fcf20a2e48573c17659268"   // 提现交易id
    },
    {
        "address": "1FZdVHtiBqMrWdjPyRPULCUceZPJ2WLCsB",
        "amount": "0.00150000",
        "applyTime": "2019-09-24 12:43:45",
        "coin": "BTC",
        "id": "156ec387f49b41df8724fa744fa82719",
        "network": "BTC",
        "transferType": 0, 
        "status": 6,
        "transactionFee": "0.004",
        "confirmNo": 2,
        "info": "",
        "txId": "60fd9007ebfddc753455f95fafa808c4302c836e4d1eebc5a132c36c1d8ac354"
    }
]
```

# 鉴权服务

websocket接口是 `wss://open-api-ws.bingx.com/market`

订阅账户数据流的stream名称为 `/market?listenKey=`
```
wss://open-api-ws.bingx.com/market?listenKey=a8ea75681542e66f1a50a1616dd06ed77dab61baa0c296bca03a9b13ee5f2dd7
```

listenKey 获取方式如下：

## 生成 Listen Key

listen key的有效时间为1小时

**接口**
```
    POST /openApi/user/auth/userDataStream
```

CURL

```
curl -X POST 'https://open-api.bingx.com/openApi/user/auth/userDataStream' --header "X-BX-APIKEY:g6ikQYpMiWLecMQ39DUivd4ENem9ygzAim63xUPFhRtCFBUDNLajRoZNiubPemKT"

```

**请求头参数**

| 参数名          | 类型     | 是否必填 | 备注         |
| ------         | ------  | ------  |------------|    
| X-BX-APIKEY    | string  | 是      | 请求的API KEY |


**响应**

| 参数名                | 类型     | 备注  |
| ------               |--------|-----|    
| listenKey               | string | 返回的 |


```
{"listenKey":"a8ea75681542e66f1a50a1616dd06ed77dab61baa0c296bca03a9b13ee5f2dd7"}
```


## 延长 Listen Key 有效期

有效期延长至本次调用后60分钟,建议每30分钟发送一个 ping 。

**接口**
```
    PUT /openApi/user/auth/userDataStream
```

```
curl -i -X PUT 'https://open-api.bingx.com/openApi/user/auth/userDataStream?listenKey=d84d39fe78762b39e202ba204bf3f7ebed43bbe7a481299779cb53479ea9677d'
```

**请求参数**

| 参数名          | 类型     | 是否必填 | 备注         |
| ------         | ------  | ------  |------------|    
| listenKey   | string  | 是      | 返回的listenKey |


**响应**

```
http status 200 成功
http status 204 没有请求参数
http status 404 没有这个listenKey
```

## 关闭 Listen Key

关闭用户数据流。

**接口**
```
    DELETE /openApi/user/auth/userDataStream
```

```
curl -i -X DELETE 'https://open-api.bingx.com/openApi/user/auth/userDataStream?listenKey=d84d39fe78762b39e202ba204bf3f7ebed43bbe7a481299779cb53479ea9677d'
```

**请求参数**

| 参数名          | 类型     | 是否必填 | 备注         |
| ------         | ------  | ------  |------------|    
| listenKey   | string  | 是      | 请求的API KEY |


**响应**

```
http status 200 成功
http status 204 没有请求参数
http status 404 没有这个listenKey
```
