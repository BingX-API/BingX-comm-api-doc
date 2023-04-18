<!-- TOC -->
* [Basic Information](#basic-information)
  * [Service Address](#service-address)
  * [Service Application](#service-application)
  * [Common Error Codes](#common-error-codes)
* [Signature Authentication](#signature-authentication)
  * [Signature Description](#signature-description)
* [permission interface](#permission-interface)
  * [Query user API Key permissions](#Query-user-API-Key-permissions)
* [User Universal Transfer Interface](#user-universal-transfer-interface)
  * [User Universal Transfer](#user-universal-transfer)
  * [Query User Universal Transfer History (USER_DATA)](#query-user-universal-transfer-history--userdata-)
  * [Deposit History(supporting network)](#deposit-history--supporting-network-)
  * [Withdraw History (supporting network)](#withdraw-history--supporting-network-)
* [Authentication Interface](#authentication-interface)
  * [generate Listen Key](#generate-listen-key)
  * [extend Listen Key Validity period](#extend-listen-key-validity-period)
  * [delete Listen Key](#delete-listen-key)
<!-- TOC -->n
## Service Address


https://open-api.bingx.com

HTTP 200 status code indicates a successful response. The response body might contain a message which will be displayed accordingly.

## Service Application

The API is currently in internal testing, and the application page will be opened soon, please be patient. If you have other needs, please contact customer service.

## Common Error Codes

**Common HTTP Error Codes**

* 4XX error codes are used to indicate wrong request content, behavior, format

* 5XX error codes are used to indicate problems on the server's side

**Common Business Error Codes**

* 100001 - Signature authentication failed

* 100202 - Insufficient balance

* 100400 - Invalid parameter

* 100440 - Order price deviates greatly from the market price

* 100500 - Internal server error

* 100503 - Server busy

**Notes**:
* If it fails, there will be an error description included in the response body

* Each interface may throw an exception

# Signature Authentication
Interfaces that require authentication must contain following information:

* Pass `apiKey` with the `X-BX-APIKEY` in the request header.
* Request parameters include `signature` that is computed based on signature algorithm.
* Request parameters include `timestamp` as the timestamp of the request，the unit is millisecond. When the server receives the request, it will judge the timestamp in the request. The request will be considered invalid if it was sent 5000 milliseconds ago. This window time value can be defined by sending the optional parameter `recvWindow`.

## Signature Description
`signature` is created by using **HMAC SHA256** algorithm to encrypt the request parameters.

**Example: Sign the following request**
- API parameters:
```
quoteOrderQty = 20
side = BUY
symbol = ETH-USDT
timestamp = 1649404670162
type = MARKET
```
- API information:
```
apiKey = Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU
secretKey = UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI
```
- Parameters are sent via `query string` example
```
1. Assemble API parameters: quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET
2. Use secretKey to generate a signature from the assembled parameter string: 428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827
   echo -n "quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET" | openssl dgst -sha256 -hmac "UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI" -hex
3. Send request: curl -H 'X-BX-APIKEY: Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU' 'https://open-api.bingx.com/openApi/spot/v1/trade/order?quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET&signature=428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827'
```
- Parameters are sent via `request body` example
```
1. Assemble API parameters: quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET
2. Use secretKey to generate a signature from the assembled parameter string: 428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827
   echo -n "quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET" | openssl dgst -sha256 -hmac "UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI" -hex
3. Send request: curl -H 'X-BX-APIKEY: Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU' -X POST 'https://open-api.bingx.com/openApi/spot/v1/trade/order' -d 'quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET&signature=428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827'
```
- Parameters are sent via `query string` and `request body` example
```
queryString: quoteOrderQty=20&side=BUY&symbol=ETHUSDT
requestBody: timestamp=1649404670162&type=MARKET

1. Assemble API parameters: quoteOrderQty=20&side=BUY&symbol=ETHUSDTtimestamp=1649404670162&type=MARKET
2. Use secretKey to generate a signature from the assembled parameter string: 94e0b4925060a615e1e372d4c929015d4b59d3c89067dc0beeafcfb33a6d8d10
   echo -n "quoteOrderQty=20&side=BUY&symbol=ETHUSDTtimestamp=1649404670162&type=MARKET" | openssl dgst -sha256 -hmac "UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI" -hex
3. Send request: curl -H 'X-BX-APIKEY: Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU' -X POST 'https://open-api.bingx.com/openApi/spot/v1/trade/order?quoteOrderQty=20&side=BUY&symbol=ETHUSDT' -d 'timestamp=1649404670162&type=MARKET&signature=94e0b4925060a615e1e372d4c929015d4b59d3c89067dc0beeafcfb33a6d8d10'
```


# permission interface

## Query user API Key permissions

**interface**
```
    GET /openApi/v1/account/apiRestrictions
```

**parameter**

| parameter name          | type | required | Remark                                       |
| ------         |------|----------|----------------------------------------------|
| timestamp         | LONG | yes      | current timestamp  For example 1658748648396 |

CURL

```
curl --location --request GET 'https://open-api.bingx.com/openApi/v1/account/apiRestrictions?timestamp=1670215150028&signature=ecc819d72515095039b7b383310f718584af4cf70106b57609bc59473185c9a3'
```
**response**

| parameter name                | type      | Remark                                                                   |
| ------               |---------|--------------------------------------------------------------------------|    
| ipRestrict              | Boolean | Whether to restrict ip access                                            |
| createTime              | Long    | creation time                                                            |
| permitsUniversalTransfer              | Boolean  | Authorize the key to be used on a dedicated universal transfer interface |
| enableReading              | Boolean  | Can read                                                                 |
| enableFutures              | Boolean  | swap trading authority                                                   |
| enableSpotAndMarginTrading              | Boolean  | Spot authority                                                                     |



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

# User Universal Transfer Interface


## User Universal Transfer

**Interface**
```
    GET /openApi/api/v3/get/asset/transfer
```

**parameter**

| Parameter name | Type | Required or not | Remarks                                             |
| ------         | ------  |-----------------|-----------------------------------------------------|    
| type         | ENUM  | yes             | transfer tpye                                       |
| asset         | STRING  | no              | coin name e.g. USDT                                   |
| amount         | DECIMAL  | no               | amount                                              |
| recvWindow         | LONG  | no               | Execution window time, cannot be greater than 60000 |
| timestamp         | LONG  | no               | current timestamp e.g. 1658748648396                |

```
   
      ENUM of transfer types:
     
      FUND_SFUTURES，Funding Account -> Standard Contract
     
      SFUTURES_FUND，Standard Contract -> Funding Account
     
      FUND_PFUTURES，Funding Account -> Perpetual Futures
     
      PFUTURES_FUND，Perpetual Futures -> Funding Account
     
      SFUTURES_PFUTURES，Standard Contract -> Perpetual Futures
     
      PFUTURES_SFUTURES，Perpetual Futures -> Standard Contract
   
```

CURL
```
curl --location --request GET 'https://open-api.bingx.com/openApi/api/v3/get/asset/transfer?type=FUND_PFUTURES&asset=USDT&amount=100&timestamp=1670215150028&signature=ecc819d72515095039b7b383310f718584af4cf70106b57609bc59473185c9a3'
```
**response**

| parameter name | type | remarks |
| ------               | ------  |------|    
| tranId              | LONG  | Transaction ID |


```
{
    "tranId":13526853623
}
```



## Query User Universal Transfer History (USER_DATA)

**Interface**
```
    GET /openApi/api/v3/asset/transfer
```

**Parameter**

| Parameter name | Type | Required or not | Remarks              |
| ------         |------|-----------------|----------------------|    
| type         | ENUM | yes             | transfer type        |
| startTime         | LONG | no              | Starting time 1658748648396   |
| endTime         | LONG | no               | End Time   1658748648396 |
| current         | Int | no               | current page default1              |
| size         | Int  | no               | Page size default 10 can not exceed 100   |
| recvWindow         | LONG | no               | Execution window time, cannot be greater than 60000    |
| timestamp         | LONG | no               | current timestamp 1658748648396  |


```
   
      ENUM of transfer types:
     
      FUND_SFUTURES，Funding Account -> Standard Contract
     
      SFUTURES_FUND，Standard Contract -> Funding Account
     
      FUND_PFUTURES，Funding Account -> Perpetual Futures
     
      PFUTURES_FUND，Perpetual Futures -> Funding Account
     
      SFUTURES_PFUTURES，Standard Contract -> Perpetual Futures
     
      PFUTURES_SFUTURES，Perpetual Futures -> Standard Contract
      
      FUND_STRADING，Funding Account -> Grid Account
      
      STRADING_FUND，Grid Account -> Funding Account
  
      FUND_CTRADING，Funding Account -> Copy Trade Account
      
      SFUTURES_CTRADING，Standard Contract -> Copy Trade Account
      
      PFUTURES_CTRADING，Perpetual Futures -> Copy Trade Account
      
      CTRADING_FUND，Copy Trade Account -> Funding Account
      
      CTRADING_SFUTURES，Copy Trade Account -> Standard Contract
      
      CTRADING_PFUTURES，Copy Trade Account -> Perpetual Futures
   
```
**response**

| parameter name | type | remarks     |
| ------               |---------|-------------|    
| total              | LONG    | total       |
| rows              | Array   | Array       |
| asset              | String  | coin name   |
| amount              | DECIMAL | coin amount |
| type              | ENUM    | transfer type         |
| status              | String  | CONFIRMED   |
| tranId              | LONG    | Transaction ID        |
| timestamp              | LONG    | Transfer time stamp      |



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



## Deposit History(supporting network)

**Interface**
```
    GET /openApi/api/v3/capital/deposit/hisrec
```

**Parameter**

| Parameter name | Type | Required or not | Remarks                                                      |
| ------         |--------|-----------------|--------------------------------------------------------------|    
| coin         | String | no              | coin name                                                    |
| status         | Int    | no              | status(0:pending,6: credited but cannot withdraw, 1:success) |
| startTime         | LONG   | no              | Starting time   1658748648396                                         |
| endTime         | LONG   | no              | End Time   1658748648396                                         |
| offset         | Int    | no              | offset default0                                                       |
| limit         | Int    | no              | Page size default 1000 cannot exceed 1000                                        |
| recvWindow         | LONG   | no              | Execution window time, cannot be greater than 60000                                            |
| timestamp         | LONG   | yes             | current timestamp 1658748648396                                          |

**response**

| parameter name | type | remarks                                                                               |
| ------               |---------|---------------------------------------------------------------------------------------|    
| amount              | DECIMAL | Recharge amount                                                                       |
| coin              | String  | coin name                                                                             |
| network              | String  | recharge network                                                                                  |
| status              | Int     | Status Status 0-Confirmed-10-To be confirmed (under review) 20-Applied for block 30-Approved and passed 40-Approval failed 50-Exported 60-Preliminary confirmation of recharge (final confirmation becomes 0) 70-Approved failed and returned assets |
| address              | String  | recharge address                                                                                  |
| addressTag              | String  | Remark                                                                                    |
| txId              | LONG    | transaction id                                                                                  |
| insertTime              | LONG    | transaction hour                                                                                  |
| transferType              | LONG    | Transaction Type 0 = Recharge                                                                              |
| unlockConfirm              | LONG    | confirm times for unlocking                                                                           |
| confirmTimes              | LONG    | Network confirmation times                                                                                |


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
        "unlockConfirm":"12/12", // confirm times for unlocking
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


## Withdraw History (supporting network)

**Interface**
```
    GET /openApi/api/v3/capital/withdraw/history
```

**Parameter**

| Parameter name | Type | Required or not | Remarks |
| ------         |--------|-----------------|-------------------------------------------------------|    
| coin         | String | no              | coin name                                                  |
| withdrawOrderId         | String | no              | Custom ID, if there is none, this field will not be returned                                    |
| status         | Int    | no              | Status (0: Confirmation Email has been sent, 2: Waiting for confirmation 3: Rejected 4: Processing 5: Withdrawal transaction failed 6 Withdrawal completed) |
| startTime         | LONG   | no              | Starting time   1658748648396                                  |
| endTime         | LONG   | no              | End Time   1658748648396                                  |
| offset         | Int    | no              | offset default 0                                                |
| limit         | Int    | no              | Page size default 1000 cannot exceed 1000                                 |
| recvWindow         | LONG   | no              | Execution window time, cannot be greater than 60000                                     |
| timestamp         | LONG   | yes             | current timestamp 1658748648396                                   |

**response**

| parameter name | type | remarks                                                                               |
|---------------|---------|---------------------------------------------------------------------------------------|    
| address       | String  | address                                                                               |
| amount        | DECIMAL | Withdrawal amount                                                                     |
| applyTime       | Date    | Withdrawal time                                                                                  |
| coin        | String  | coin name                                                                                   |
| id       | String  | The id of the withdrawal                                                                               |
| withdrawOrderId    | String  | Custom ID, if there is none, this field will not be returned                                                                    |
| network          | String  | Withdrawal network                                                                                  |
| transferType    | Int     | Transaction Type 1 = Withdrawal                                                                              |
| status  | Int     | Status Status 0-Confirmed-10-To be confirmed (under review) 20-Applied for block 30-Approved and passed 40-Approval failed 50-Exported 60-Preliminary confirmation of recharge (final confirmation becomes 0) 70-Approved failed and returned assets |
| transactionFee | String  | handling fee                                                                                   |
| confirmNo  | Int     | Withdrawal confirmation times                                                                                |
| info  | String  | Reason for withdrawal failure                                                                                |
| txId  | String  | Withdrawal transaction id                                                                                |



```
[
    {
        "address": "0x94df8b352de7f46f64b01d3666bf6e936e44ce60",
        "amount": "8.91000000",   
        "applyTime": "2019-10-12 11:12:02",  
        "coin": "USDT",
        "id": "b6ae22b3aa844210a7041aee7589627c",  
        "withdrawOrderId": "WITHDRAWtest123", 
        "network": "ETH",
        "transferType": 0 
        "status": 6,
        "transactionFee": "0.004", 
        "confirmNo":3,  
        "info": "The address is not valid. Please confirm with the recipient", 
        "txId": "0xb5ef8c13b968a406cc62a93a8bd80f9e9a906ef1b3fcf20a2e48573c17659268"  
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

# authentication Interface

The base URL of Websocket Market Data is: `wss://open-api-ws.bingx.com/market`

User Data Streams are accessed at `/market?listenKey=`

```
wss://open-api-ws.bingx.com/market?listenKey=a8ea75681542e66f1a50a1616dd06ed77dab61baa0c296bca03a9b13ee5f2dd7
```

Use following API to fetch and update listenKey:

## generate Listen Key

listen key Valid for 1 hour

**interface**
```
    POST /openApi/user/auth/userDataStream
```

CURL

```
curl -X POST 'https://open-api.bingx.com/openApi/user/auth/userDataStream' --header "X-BX-APIKEY:g6ikQYpMiWLecMQ39DUivd4ENem9ygzAim63xUPFhRtCFBUDNLajRoZNiubPemKT"

```

**request header parameters**

| parameter name          | type   | Is it required | Remark         |
| ------         |--------|----------------|------------|    
| X-BX-APIKEY    | string | yes            | API KEY |


**response**

| parameter name                | type   | Remark     |
| ------               |--------|------------|    
| listenKey               | string | listen Key |


```
{"listenKey":"a8ea75681542e66f1a50a1616dd06ed77dab61baa0c296bca03a9b13ee5f2dd7"}
```


## extend Listen Key Validity period

The validity period is extended to 60 minutes after this call, and it is recommended to send a ping every 30 minutes.

**interface**
```
    PUT /openApi/user/auth/userDataStream
```

```
curl -i -X PUT 'https://open-api.bingx.com/openApi/user/auth/userDataStream?listenKey=d84d39fe78762b39e202ba204bf3f7ebed43bbe7a481299779cb53479ea9677d'
```

**request parameters**

| parameter name          | type   | Is it required | Remark         |
| ------         | ------  |----------------|------------|    
| listenKey   | string  | yes            | listenKey |


**response**

```
http status 200 success
http status 204 not content
http status 404 not find key
```

## delete Listen Key

delete User data flow.

**interface**
```
    DELETE /openApi/user/auth/userDataStream
```

```
curl -i -X DELETE 'https://open-api.bingx.com/openApi/user/auth/userDataStream?listenKey=d84d39fe78762b39e202ba204bf3f7ebed43bbe7a481299779cb53479ea9677d'
```

**request parameters**

| parameter name          | type   | Is it required | Remark        |
| ------         | ------  |----------------|-----------|    
| listenKey   | string  | yes            | listenKey |


**response**

```
http status 200 success
http status 204 not content
http status 404 not find key
```
