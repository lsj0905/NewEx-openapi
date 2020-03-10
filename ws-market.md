# websocket行情数据

## 简介

### 接入url
wss://api.ws.newex.io/

### 数据压缩
WebSocket API 返回的所有数据都进行了 GZIP 压缩，需要 client 在收到数据之后解压。

### 心跳消息
当用户的Websocket客户端连接到Websocket服务器后，服务器会定期（当前设为5秒）向其发送ping消息并包含一整数值如下：
```
{"ping": 1492420473027}
```
当用户的Websocket客户端接收到此心跳消息后，应返回pong消息并包含同一整数值：
`当Websocket服务器连续两次发送了`ping`消息却没有收到任何一次`pong`消息返回后，服务器将主动断开与此客户端的连接。`

### 订阅主题
成功建立与Websocket服务器的连接后，Websocket客户端发送如下请求以订阅特定主题：
```
{
  "sub": "market.btcusdt.kline.1min",
  "id": "id1"
}
```
成功订阅后，Websocket客户端将收到确认：
```
{
  "id": "id1",
  "status": "ok",
  "subbed": "market.btcusdt.kline.1min",
  "ts": 1489474081631
}
```
之后, 一旦所订阅的主题有更新，Websocket客户端将收到服务器推送的更新消息（push）：
```
{
  "ch": "market.btcusdt.kline.1min",
  "ts": 1489474082831,
  "tick": {
    "id": 1489464480,
    "amount": 0.0,
    "count": 0,
    "open": 7962.62,
    "close": 7962.62,
    "low": 7962.62,
    "high": 7962.62,
    "vol": 0.0
  }
}
```

### 取消订阅
取消订阅的格式如下：
```
{
  "unsub": "market.btcusdt.trade.detail", #topic to unsub
  "id": "id4" #id generate by client
}
```

取消订阅成功确认：
```
{
  "id": "id4",
  "status": "ok",
  "unsubbed": "market.btcusdt.trade.detail",
  "ts": 1494326028889
}
```




## K线数据

一旦K线数据产生，Websocket服务器将通过此订阅主题接口推送至客户端

#### 主题订阅
market.$symbol$.kline

| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| symbol | string | true | 交易对 | "newusdt", "btcusdt", "newbtc" |

#### Subscribe request
```
{
  "sub": "market.ethbtc.kline",
  "id": "id1"
}
```

#### Response
```
{
  "id": "id1",
  "status": "ok",
  "subbed": "market.ethbtc.kline",
  "ts": 1489474081631
}
```

#### 数据更新字段列表
| 参数 | 数据类型 | 描述 |
|:-:|:-:|:-:|:-:|:-:|
| id | integer | unix时间，同时作为K线ID |
| amount | string | 成交量 |
| count | integer | 成交笔数 |
| open | string | 开盘价 |
| close | string | 收盘价（当K线为最晚的一根时，是最新成交价） |
| low | string | 最低价 |
| high | string | 最高价 |
| vol | string | 成交额, 即每一笔成交价 * 该笔的成交量 |

#### Example
```
{
  "ch": "market.ethbtc.kline",
  "ts": 1489474082831,
  "tick": {
    "id": 1489464480,
    "amount": "0.0",
    "count": 0,
    "open": "7962.62",
    "close": "7962.62",
    "low": "7962.62",
    "high": "7962.62",
    "vol": "0.0"
  }
}
```



## 市场深度MBP行情数据

用户可订阅此频道以接收最新深度行情Market By Price (MBP) 的增量数据推送；
建议下游数据处理方式：
1） 订阅增量数据并开始缓存；
2） 请求全量数据（同等档位数）并根据该全量消息的seqNum与缓存增量数据中的prevSeqNum对齐；
3） 开始连续增量数据接收与计算，构建并持续更新MBP订单簿；
4） 每条增量数据的prevSeqNum须与前一条增量数据的seqNum一致，否则意味着存在增量数据丢失，须重新获取全量数据并对齐；
5） 如果收到增量数据包含新增price档位，须将该price档位插入MBP订单簿中适当位置；
6） 如果收到增量数据包含已有price档位，但size不同，须替换MBP订单簿中该price档位的size；
7） 如果收到增量数据某price档位的size为0值，须将该price档位从MBP订单簿中删除；
8） 如果收到单条增量数据中包含两个及以上price档位的更新，这些price档位须在MBP订单簿中被同时更新。

#### 主题订阅
market.$symbol.mbp

| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| symbol | string | true | 交易对 | "newusdt", "btcusdt", "newbtc" |

#### Subscribe request
```
{
  "sub": "market.btcusdt.mbp",
  "id": "id1"
}
```

#### Response
```
{
  "id": "id1",
  "status": "ok",
  "subbed": "market.btcusdt.mbp",
  "ts": 1489474081631
}
```

#### 数据更新字段列表

| 参数 | 数据类型 | 描述 |
|:-:|:-:|:-:|:-:|:-:|
| seqNum | integer | 消息序列号 |
| prevSeqNum | integer | 上一消息序列号 |
| bids | object | 买盘，按price降序排列，["price","size"] |
| asks | object | 卖盘，按askPrice升序排列，["price","size"] |

#### Response
```
{
    "ch": "market.btcusdt.mbp.150",
    "ts": 1573199608679,
    "tick": {
        "seqNum": 100020146795,
        "prevSeqNum": 100020146794,
        "bids": [],
        "asks": [
            [645.140000000000000000, 26.755973959140651643] // [price, size]
        ]
    }
}
```



## 买一卖一逐笔行情

当买一价、买一量、卖一价、卖一量，其中任一数据发生变化时，此主题推送逐笔更新。

#### 主题订阅
market.$symbol.bbo

| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| symbol | string | true | 交易对 | "newusdt", "btcusdt", "newbtc" |

#### Subscribe request
```
{
  "sub": "market.btcusdt.bbo",
  "id": "id1"
}
```

#### Response
```
{
  "id": "id1",
  "status": "ok",
  "subbed": "market.btcusdt.bbo",
  "ts": 1489474081631
}
```

#### 数据更新字段列表

| 参数 | 数据类型 | 描述 |
|:-:|:-:|:-:|:-:|:-:|
| symbol | string | 交易代码 |
| quoteTime | integer | 盘口更新时间 |
| bid | string | 买一价 |
| bidSize | string | 买一量 |
| ask | string | 卖一价 |
| askSize | string | 卖一量 |


#### Response
```
{
    "ch": "market.btcusdt.bbo",
    "ts": 1489474082831,
    "tick": {
        "symbol": "btcusdt",
        "quoteTime": "1489474082811",
        "bid": "10008.31",
        "bidSize": "0.01",
        "ask": "10009.54",
        "askSize": "0.3"
    }
}
```



## 成交明细

此主题提供市场最新成交逐笔明细。

#### 主题订阅
market.$symbol.trade.detail

| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| symbol | string | true | 交易对 | "newusdt", "btcusdt", "newbtc" |

#### Subscribe request
```
{
  "sub": "market.btcusdt.trade.detail",
  "id": "id1"
}
```

#### Response
```
{
  "id": "id1",
  "status": "ok",
  "subbed": "market.btcusdt.trade.detail",
  "ts": 1489474081631
}
```

#### 数据更新字段列表

| 参数 | 数据类型 | 描述 |
|:-:|:-:|:-:|:-:|:-:|
| tradeId | string | 唯一成交ID |
| amount | integer | 成交量 |
| price | string | 成交价 |
| ts | integer | 成交时间 (UNIX epoch time in millisecond) |
| direction | string | 成交主动方 (taker的订单方向) : 'buy' or 'sell' |

#### Response
```
{
    "ch": "market.btcusdt.trade.detail",
    "ts": 1489474082831,
    "tick": {
        "id": 14650745135,
        "ts": 1533265950234,
        "data": [
            {
                "amount": "0.0099",
                "ts": 1533265950234,
                "tradeId": "102043494568",
                "price": "401.74",
                "direction": "buy"
            }
            // more Trade Detail data here
        ]
    }
}
```




## 市场概要

此主题提供24小时内最新市场概要快照。快照频率不超过每秒10次。

#### 主题订阅
market.$symbol.detail

| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| symbol | string | true | 交易对 | "newusdt", "btcusdt", "newbtc" |

#### Subscribe request
```
{
    "sub": "market.btcusdt.detail",
    "id": "id1"
}
```

#### Response
```
{
    "id": "id1",
    "status": "ok",
    "subbed": "market.btcusdt.detail",
    "ts": 1489474081631
}
```

#### 数据更新字段列表

| 参数 | 数据类型 | 描述 |
|:-:|:-:|:-:|:-:|:-:|
| id | integer | unix时间，同时作为消息ID |
| ts | integer | unix系统时间 |
| count | integer | 成交笔数 |
| amount | string | 成交量 |
| open | string | 开盘价 |
| close | string | 最新价 |
| low | string | 最低价 |
| high | string | 最高价 |
| vol | string | 成交额 |


#### Response
```
{
    "ch": "market.btcusdt.detail",
    "ts": 1489474082831,
    "tick": {
        "ts":     1494496390000,
        "id":     1494496390,
        "count":  15195,
        "amount": "12224.2922",
        "open":   "9790.52",
        "close":  "10195.00",
        "high":   "10300.00",
        "low":    "9657.00",
        "vol":    "121906001.75475"
    }
}
```
