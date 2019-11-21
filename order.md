访问交易相关的接口需要进行签名认证

# 下单
## Path
POST order/orders/place

## Params
| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| symbol | string | true | 交易对 | "newusdt", "btcusdt", "newbtc" |
| type | string | true | 订单类型 | buy-limit, sell-limit |
| amount | string | true | 订单交易量 |  |
| price | string | false | limit order的交易价格 |  |
| source | string | false | 暂不使用 |  |
| client-order-id | string | false | 用户自编订单号，暂不使用 |  |
| stop-price | string | false | 触发价格，暂不使用 |  |

## Returns
| 字段名称 | 数据类型 | 描述 |
|:-:|:-:|:-:|
| data | string | 返回的主数据对象是一个对应下单单号的字符串 |

## Example
```
{
    "error_code": 1,
    "data": {
        "data": "59378"
    }
}
```

# 撤单
## Path
POST order/orders/{order-id}/submitcancel

## Params
| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| order-id | string | true | 订单ID，填在path中 |  |

## Returns
| 字段名称 | 数据类型 | 描述 |
|:-:|:-:|:-:|
| data | string | 返回的主数据对象是一个对应下单单号的字符串 |

## Example
```
{
    "error_code": 1,
    "data": {
        "data": "59378"
    }
}
```

# 查询当前未完成订单
## Path
GET /v1/order/openOrders

## Params
| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| symbol | string | true | 交易对 | "newusdt", "btcusdt", "newbtc" |
| side | string | false | 指定只返回某一个方向的订单 | 暂不使用，默认both |
| from | string | false | 查询起始 ID |  |
| size | string | false | 查询记录大小，默认100 | 1-500 |
| direct | string | false | 排序方向，默认为prev | prev升序 or next降序 |

## Returns
| 字段名称 | 数据类型 | 描述 |
|:-:|:-:|:-:|
| id | integer | 订单id |
| symbol | string | 交易对 |
| price | string | limit order的交易价格 |
| created-at | int | 订单创建的调整为北京时间的时间戳，单位毫秒 |
| type | string | 订单类型 |
| filled-amount | string | 订单中已成交部分的数量 |
| filled-cash-amount | string | 订单中已成交部分的总价格 |
| filled-fees | string | 已交交易手续费总额 |
| source | string | 现货交易填写“api” |
| state | string | 订单状态，包括submitted, partial-filled, cancelling, created |
| stop-price | string | 止盈止损订单触发价格 |
| operator | string | 止盈止损订单触发价运算符 |

## Example
```
{
    "error_code": 1,
    "data": [
        {
            "id": 5454937,
            "symbol": "ethusdt",
            "account-id": 30925,
            "amount": "1.000000000000000000",
            "price": "0.453000000000000000",
            "created-at": 1530604762277,
            "type": "sell-limit",
            "filled-amount": "0.0",
            "filled-cash-amount": "0.0",
            "filled-fees": "0.0",
            "source": "web",
            "state": "submitted"
        }
    ]
}
```


# 查询历史订单
## Path
GET /order/history

## Params
| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| symbol | string | true | 交易对 | "newusdt", "btcusdt", "newbtc" |
| start-time | long | false | 查询起始时间 |  |
| end-time | long | false | 查询结束时间 |  |
| size | string | false | 查询记录大小，默认100 | 10-1000 |
| direct | string | false | 排序方向，默认为prev | prev升序 or next降序 |

## Returns
| 字段名称 | 数据类型 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|
| account-id | true | long | 账户 ID |  |
| amount | true | string | 订单数量 |  |
| canceled-at | false | long | 接到撤单申请的时间 |  |
| created-at | true | long | 订单创建时间 |  |
| field-amount | true | string | 已成交数量 |  |
| field-cash-amount | true | string | 已成交总金额 |  |
| field-fees | true | string | 已成交手续费（买入为基础币，卖出为计价币） |  |
| finished-at | false | long | 最后成交时间 |  |
| id | true | long | 订单ID |  |
| price | true | string | 订单价格 |  |
| source | true | string | 订单来源 | api |
| state | true | string | 订单状态 | partial-canceled 部分成交撤销, filled 完全成交, canceled 已撤销 |
| symbol | true | string | 交易对 | newusdt, btcusdt, newbtc ... |
| stop-price | false | string | 止盈止损订单触发价格 |  |
| operator | false | string | 止盈止损订单触发价运算符 | gte,lte |
| type | true | string | 订单类型 | buy-limit：限价买, sell-limit：限价卖 |

## Example
```
{
    "error_code": 1,
    "data": [
        {
            "id": 31215214553,
            "symbol": "btcusdt",
            "account-id": 4717043,
            "amount": "1.000000000000000000",
            "price": "1.000000000000000000",
            "created-at": 1556533539282,
            "type": "buy-limit",
            "field-amount": "0.0",
            "field-cash-amount": "0.0",
            "field-fees": "0.0",
            "finished-at": 1556533568953,
            "source": "web",
            "state": "canceled",
            "canceled-at": 1556533568911
        }
        ...
    ]
}
```

# 订单详情(含成交明细)
## Path
GET /order/orders/{order-id}

## Params
| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| order-id | string | true | 订单ID，填在path中 |  |

## Returns
| 字段名称 | 是否必须 | 数据类型 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| account-id | true | long | 账户 ID |  |
| amount | true | string | 订单数量 |  |
| canceled-at | false | long | 订单撤销时间 |  |
| created-at | true | long | 订单创建时间 |  |
| field-amount | true | string | 已成交数量 |  |
| field-cash-amount | true | string | 已成交总金额 |  |
| field-fees | true | string | 已成交手续费（买入为币，卖出为钱） |  |
| finished-at | false | long | 订单变为终结态的时间，不是成交时间，包含“已撤单”状态 |  |
| id | true | long | 订单ID |  |
| price | true | string | 订单价格 |  |
| source | true | string | 订单来源 | api |
| state | true | string | 订单状态 | submitted已提交, partial-filled部分成交, partial-canceled部分成交撤销, filled完全成交, canceled已撤销，created |
| symbol | true | string | 交易对 | btcusdt, ethbtc, rcneth ... |
| type | true | string | 订单类型 | buy-limit：限价买, sell-limit：限价卖 |
| stop-price | false | string | 止盈止损订单触发价格 |  |
| operator | false | string | 止盈止损订单触发价运算符 | gte,lte |

## Example
```
{
    "error_code": 1,
    "data": {
        "id": 59378,
        "symbol": "ethusdt",
        "account-id": 100009,
        "amount": "10.1000000000",
        "price": "100.1000000000",
        "created-at": 1494901162595,
        "type": "buy-limit",
        "field-amount": "10.1000000000",
        "field-cash-amount": "1011.0100000000",
        "field-fees": "0.0202000000",
        "finished-at": 1494901400468,
        "user-id": 1000,
        "source": "api",
        "state": "filled",
        "canceled-at": 0
    }
}
```

# 成交记录
## Path
GET /order/orders/{order-id}/matchresults

## Params
| 参数 | 数据类型 | 是否必须 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| order-id | string | true | 订单ID，填在path中 |  |

## Returns
| 字段名称 | 是否必须 | 数据类型 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| created-at | true | long | 成交时间 |  |
| filled-amount | true | string | 成交数量 |  |
| filled-fees true | string | 成交手续费 |  |
| id | true | long | 订单成交记录ID |  |
| match-id | true | long | 撮合ID |  |
| order-id | true | long | 订单 ID |   |
| trade-id | false | integer | Unique trade ID (NEW)唯一成交编号 |  |
| price |  true | string | 成交价格 |  |
| source | true | string | 订单来源 | api |
| symbol | true | string | 交易对 | btcusdt, ethbtc, rcneth ... |
| type | true | string | 订单类型 | buy-limit：限价买, sell-limit：限价卖 |
| role | true | string | 成交角色 | maker,taker |

## Example
```
{
    "error_code": 1,
    "data": [
        {
            "id": 29553,
            "order-id": 59378,
            "match-id": 59335,
            "trade-id": 100282808529,
            "symbol": "ethusdt",
            "type": "buy-limit",
            "source": "api",
            "price": "100.1000000000",
            "filled-amount": "9.1155000000",
            "filled-fees": "0.0182310000",
            "created-at": 1494901400435,
            "role": "maker",
        }
        ...
    ]
}
```
