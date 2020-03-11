# websocket资产及订单

## 简介

### 接入url
wss://api.ws.newex.io/v1

### 心跳消息
当用户的Websocket客户端连接到Websocket服务器后，服务器会定期（当前设为20秒）向其发送ping消息并包含一整数值如下：
```
{
    "action": "ping",
    "data": {
        "ts": 1575537778295
    }
}
```
当用户的Websocket客户端接收到此心跳消息后，应返回pong消息并包含同一整数值：
```
{
    "action": "pong",
    "data": {
          "ts": 1575537778295 // 使用Ping消息中的ts值
    }
}
```
`当Websocket服务器连续三次发送了`ping`消息却没有收到任何一次`pong`消息返回后，服务器将主动断开与此客户端的连接。`

action的有效取值：
| 有效取值 | 取值说明 |
|:-:|:-:|
| sub | 订阅数据 |
| req | 数据请求 |
| ping、pong | 心跳数据 |
| push | 推送数据，服务端发送至客户端数据类型 |


### 鉴权

鉴权请求格式如下：
```
{
    "action": "req", 
    "ch": "auth",
    "params": { 
        "authType":"api",
        "accessKey": "sffd-ddfd-dfdsaf-dfdsafsd",
        "signatureMethod": "HmacSHA256",
        "signatureVersion": "2.1",
        "timestamp": "2019-09-01T18:16:16",
        "signature": "safsfdsjfljljdfsjfsjfsdfhsdkjfhklhsdlkfjhlksdfh"
    }
}
```

鉴权成功后返回数据格式如下：
```
{
    "action": "req",
    "code": 200,
    "ch": "auth",
    "data": {}
}
```

参数说明
| 参数 | 数据类型 | 是否必须 | 描述 |
|:-:|:-:|:-:|:-:|
| action | string | true | Websocket数据操作类型，鉴权固定值为req |
| ch | string | true | 请求主题，鉴权固定值为auth |
| authType | string | true | 鉴权类型，鉴权固定值为api |
| accessKey | string | true | 您申请的API Key中的AccessKey |
| signatureMethod | string | true | 签名方法，用户计算签名寄语哈希的协议，固定值为HmacSHA256 |
| signatureVersion | string | true | 签名协议版本，固定值为2.1 |
| timestamp | string | true | 时间戳，您发出请求的时间（UTC时间）在查询请求中包含此值有助于防止第三方截取您的请求。 |
| signature | string | true | 签名, 计算得出的值，用于确保签名有效和未被篡改 |


### 签名步骤

与 [openapi版本签名](signature.md) 步骤相似，具体区别如下：

1. 生成参与签名的字符串时，请求方法固定使用GET，请求地址固定为/ws/v2

2. 生成参与签名的固定参数名替换为：accessKey，signatureMethod，signatureVersion，timestamp

3. signatureVersion版本升级为2.1


签名前最后生成的字符串如下：
```
GET\n
api.ws.newex.io\n
/ws/v2\n
accessKey=0664b695-rfhfg2mkl3-abbf6c5d-49810&signatureMethod=HmacSHA256&signatureVersion=2.1&timestamp=1575537778295
```


### 订阅主题
成功建立与Websocket服务器的连接后，Websocket客户端发送如下请求以订阅特定主题：
```
{
    "action": "sub",
    "ch": "accounts.update"
}
```
成功订阅后，Websocket客户端将收到确认：
```
{
    "action": "sub",
    "code": 200,
    "ch": "accounts.update",
    "data": {}
}
```


### 取消订阅
取消订阅的格式如下：
```
{
    "action": "unsub", 
    "ch": "topic",
}
```

取消订阅成功确认：
```
{
    "action": "unsub",
    "code": 200,
    "ch": "accounts.update",
    "data": {}
}
```


### 请求数据
成功建立Websocket服务器的连接后，Websocket客户端发送如下请求用以获取一次性数据：
```
{
    "action": "req", 
    "ch": "topic",
}
```

请求成功后Websocket客户端会收到如下消息：
```
{
    "action": "req",
    "ch": "topic",
    "code": 200,
    "data": {} // 请求数据体
}
```




## 订阅账户更新

在账户余额发生变动或可用余额发生变动时推送

#### 主题订阅
accounts.update


#### Subscribe request
```
{
    "action": "sub",
    "ch": "accounts.update"
}
```

#### Response
```
{
    "action": "sub",
    "code": 200,
    "ch": "accounts.update",
    "data": {}
}
```

#### 数据更新字段列表
| 参数 | 数据类型 | 描述 |
|:-:|:-:|:-:|
| currency | string | 币种 |
| accountId | integer | 账户ID |
| balance | string | 账户余额 |
| available | string | 可用余额 |
| changeType | string | 余额变动类型，有效值：order-place(订单创建)，order-match(订单成交)，order-refund(订单成交退款)，order-cancel(订单撤销)，other(其他资产变化) |
| changeTime | long | 余额变动时间，unix time in millisecond |


#### Example
```
{
    "action": "push",
    "ch": "accounts.update",
    "data": {
        "currency": "btc",
        "accountId": 33385,
        "balance": "23.111",
        "available": "2028.699426619837209087",
        "changeType": "order.match",
        "changeTime": 1574393385167
    }
}
```



## 订阅订单更新

订阅账户下的订单更新

#### 主题订阅
orders.$symbol.update

| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| symbol | string | true | 交易对 | "newusdt", "btcusdt", "newbtc", 支持通配符"*" |

#### Subscribe request
```
{
    "action": "sub",
    "ch": "orders.htusdt.update"
}
```

#### Response
```
{
    "action": "sub",
    "code": 200,
    "ch": "orders.htusdt.update",
    "data": {}
}
```

#### 数据更新字段列表

| 参数 | 数据类型 | 描述 |
|:-:|:-:|:-:|:-:|:-:|
| order-id | integer | 订单编号 |
| symbol | string | 交易代码 |
| order-state | string | 订单状态, 有效取值: submitted, partial-filled, filled, canceled, partial-canceled |
| role | string | 最近成交角色（当order-state = submitted, canceled, partial-canceled时，role 为缺省值taker；当order-state = filled, partial-filled 时，role 取值为taker 或maker。） |
| price | string | 最新价 |
| filled-amount | string | 最近成交数量 |
| filled-cash-amount | string | 最近成交数额 |
| unfilled-amount | string | 最近未成交数量 |
| order-type | string | 订单类型，包括bbuy-limit, sell-limit |


#### Response
```
{
    "action": "push",
    "ch": "orders.htusdt.update",
    "data": {
        "unfilled-amount": "0.000000000000000000",
        "filled-amount": "5000.000000000000000000",
        "price": "1.662100000000000000",
        "order-id": 2039498445,
        "symbol": "htusdt",
        "match-id": 94984,
        "filled-cash-amount": "8301.357280000000000000",
        "role": "taker|maker",
        "order-state": "filled",
        "client-order-id": "a0001",
        "order-type": "buy-limit"
    }
}
```
