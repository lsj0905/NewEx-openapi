# websocket资产及订单

## 简介

### 接入url
wss://api.ws.newex.io/

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

v2.1版本签名与 [v2.0版本签名](signature.md) 步骤相似，具体区别如下：

1. 生成参与签名的字符串时，请求方法固定使用GET，请求地址固定为/ws/v2

2. 生成参与签名的固定参数名替换为：accessKey，signatureMethod，signatureVersion，timestamp

3. signatureVersion版本升级为2.1


签名前最后生成的字符串如下：
```
GET\n
api.huobi.pro\n
/ws/v2\n
accessKey=0664b695-rfhfg2mkl3-abbf6c5d-49810&signatureMethod=HmacSHA256&signatureVersion=2.1&timestamp=2019-12-05T11%3A53%3A03
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
    "action": "unsub",
    "code": 200,
    "ch": "accounts.update",
    "data": {}
}
```
