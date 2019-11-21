# 交易对k线数据
## Path
GET /market/history/kline

## Parmas
| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| symbol | string | true | 交易对 | "newusdt", "btcusdt", "newbtc" |
| period | string | false | 返回数据的时间粒度 | '1min', '15min'，'60min', "4hour", '1day', '1week', '1month'；默认为'1min' |
| size | int | false | 返回K线数据条数，默认150 | [1,2000] |

## Returns
| 字段名称 | 数据类型 | 描述 |
|:-:|:-:|:-:|
| id | int | 调整为北京时间的时间戳，单位秒，并以此作为此K线柱的id |
| open | string | 本阶段开盘价 |
| close | string | 本阶段收盘价 |
| high | string | 本阶段最高价 |
| low | string | 本阶段最低价 |
| count | int | 交易次数 |
| amount | string | 以基础币种计量的交易量 |
| vol | string | 以报价币种计量的交易量 |

## Example
```
{
    "error_code": 1,
    "result": {
        "data": [
            {
                "id": 1571736586,
                "open": "0.0002",
                "close": "0.0006",
                "high": "0.0008",
                "low": "0.0001",
                "count": 10000,
                "amount": "10000.001",
                "vol":"613071.4384",
            },
            {
                "id": 1571736591,
                "open": "0.0002",
                "close": "0.0006",
                "high": "0.0008",
                "low": "0.0001",
                "count": 10000,
                "amount": "10000.001",
                "vol":"613071.4384",
            },
        ]
    }
}
```


# 交易对缩略信息
## Path
GET /market/detail/merged

此接口获取ticker信息同时提供最近24小时的交易聚合信息。

## Parmas
| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| symbol | string | true | 交易对 | "newusdt", "btcusdt", "newbtc" |

## Returns
| 字段名称 | 数据类型 | 描述 |
|:-:|:-:|:-:|
| id | int | 调整为北京时间的时间戳，单位秒，并以此作为此K线柱的id |
| count | int | 交易次数 |
| amount | string | 成交量 |
| vol | string | 成交额 |
| open | string | 开盘价 |
| close | string | 收盘价/当前价 |
| high | string | 最高价 |
| low | string | 最低价 |
| bid | object | 当前的最高买价 [price, quote volume] |
| ask | object | 当前的最低卖价 [price, quote volume] |

## Example
```
{
    "error_code": 1,
    "data": {
        "id": 1571736591,
        "count": 10000,
        "amount":"12121321213",
        "vol":"613071.4384",
        "open":86.21,
        "close":94.35,
        "high":98.7,
        "low":84.63,
        "bid": [1885.0000,21.8804],
        "ask": [1884.0000,1.6702],
    }
}
```


# 交易对盘口信息
## Path
GET /market/depth

此接口返回指定交易对的当前市场深度数据。

## Parmas
| Field | type | description | 取值范围 |
|:-:|:-:|:-:|:-:|
| symbol | string | 交易对 | "newusdt", "btcusdt", "newbtc" |
| depth | int | 返回深度的数量，默认20 | 5，10，20 |

## Returns
| Field | type | description |
|:-:|:-:|:-:|:-:|
| ts | int | 调整为北京时间的时间戳，单位毫秒 |
| bids | object | 当前的所有买单 [price, quote volume] |
| asks | object | 当前的所有卖单 [price, quote volume] |

## Example
```
{
    "error_code": 1,
    "data": {
        "ts": 1489464585407,
        "bids": [
            [7964, 0.0678], // [price, amount]
            [7963, 0.9162],
            [7961, 0.1],
            [7960, 12.8898],
            [7958, 1.2],
            ...
        ],
        "asks": [
            [7979, 0.0736],
            [7980, 1.0292],
            [7981, 5.5652],
            [7986, 0.2416],
            [7990, 1.9970],
            ...
        ]
    }
}
```

# 交易对成交信息
## Path
GET /market/history/trade

## Parmas
| 参数 | 数据类型 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|
| symbol | string | 交易对 | "newusdt", "btcusdt", "newbtc" |
| size | int | 返回的交易记录数量，默认1 | [1,2000] |

## Returns
| Field | type | description |
|:-:|:-:|:-:|
| trade-id | string | 唯一成交ID |
| price | string | 以报价币种为单位的成交价格 |
| amount | string | 成交量 |
| direction | int | 交易方向：“buy” 或 “sell” |
| ts | int | trade timestamp |

## Example
```
{
    "error_code": 1,
    "data": [
            {
                "trade-id": "E157224261438985",
                "price": "10.0",
                "amount": "100000",
                "direction": "sell",
                "ts": 1572446037167,
            },
            {
                "trade-id": "E157244568346928",
                "price": "10.0",
                "amount": "100000",
                "direction": "buy",
                "ts": 1572445684786,
            },
    ]
}
```
