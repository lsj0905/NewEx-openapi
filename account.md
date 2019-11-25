访问账户相关的接口需要进行签名认证

# 账户余额
## Path
GET /account/accounts/balance

## Params
无

## Returns
| 参数名称 | 数据类型 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|
| id | long | 用户id |  |
| state | string | 账户状态 | working：正常 lock：账户被锁定 |
| type | string | 账户类型 | spot：现货账户 |
| list | Array | 钱包数组 |  |
| balance | string | 余额 |  |
| currency | string | 币种 |  |
| type | string | 类型 | trade: 交易余额，frozen: 冻结余额 |

## Example
```
{
    "error_code": 1,
    "data": {
        "id": 100009,
        "type": "spot",
        "state": "working",
        "list": [
            {
                "currency": "usdt",
                "type": "trade",
                "balance": "5007.4362872650"
            },
            {
                "currency": "usdt",
                "type": "frozen",
                "balance": "348.1199920000"
            }
        ]
    }
}
```

# 账户流水
## Path
GET /account/history

## Params
| 参数名称 | 数据类型 | 是否必需 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| currency | string | false | 币种 |  |
| transact-types| string | false | 变动类型 | "all" |
| start-time | long | false | 远点时间 |  |
| end-time | long  | false | 近点时间 |  |
| sort | string | false | 检索方向 | asc or desc |
| size | int | false | 最大条目数量，默认100 | [1,500] |

## Returns
| 字段名称 | 数据类型 | 描述 |
|:-:|:-:|:-:|
| account-id | long | 用户id |
| currency | string | 币种 |
| transact-amt | string | 变动金额（入账为正 or 出账为负） |
| transact-type | string | 变动类型 |
| avail-balance | string | 可用余额 |
| acct-balance | string | 账户余额 |
| transact-time | long | 交易时间 |
| record-id | string | 记录编号 |

## Example


# 充币地址查询
## Path
GET /account/deposit/address

## Params
| 参数名称 | 数据类型 | 是否必需 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| currency | string | true | 币种 | "NEW", "USDT", "BTC" |

## Returns
| 字段名称 | 数据类型 | 描述 |
|:-:|:-:|:-:|
| currency | string | 币种 |
| address | string | 充币地址 |
| addressTag | string | 充币地址标签 |
| chain | string | 链名称 |

## Example
```
{
    "error_code": 1,
    "data": [ 
        {
            "currency": "btc",
            "address": "1PSRjPg53cX7hMRYAXGJnL8mqHtzmQgPUs",
            "addressTag": "",
            "chain": "btc"
        }
    ]
}
```

# 提币额度查询
## Path
GET /account/withdraw/quota

## Params
| 参数名称 | 数据类型 | 是否必需 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| currency | string | true | 币种 | "NEW", "USDT", "BTC" |

## Returns
| 字段名称 | 数据类型 | 描述 |
|:-:|:-:|:-:|
| currency | string | 币种 |
| chains| object |  |
| maxWithdrawAmt | string | 单次最大提币金额 |
| withdrawQuotaPerDay | string | 当日提币额度 |
| remainWithdrawQuotaPerDay | string | 当日提币剩余额度 |
| withdrawQuotaPerYear | string | 当年提币额度 |
| remainWithdrawQuotaPerYear | string | 当年提币剩余额度 |
| withdrawQuotaTotal | string | 总提币额度 |
| remainWithdrawQuotaTotal | string | 总提币剩余额度 |

## Example
```
{
    "error_code": 1,
    "data": {
        "currency": "btc",
        "chains": 
            {
                "chain": "btc",
                "maxWithdrawAmt": "200.00000000",
                "withdrawQuotaPerDay": "200.00000000",
                "remainWithdrawQuotaPerDay": "200.000000000000000000",
                "withdrawQuotaPerYear": "700000.00000000",
                "remainWithdrawQuotaPerYear": "700000.000000000000000000",
                "withdrawQuotaTotal": "7000000.00000000",
                "remainWithdrawQuotaTotal": "7000000.000000000000000000"
            } 
    }
}
```

# 充提记录
## Path
GET /query/deposit-withdraw

## Params
| 参数名称 | 数据类型 | 是否必需 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| currency | string | false | 币种 |  |
| type | string | true | 充值或提现 | deposit 或 withdraw |
| from | string | false | 查询起始 ID |  |
| size | string | false | 查询记录大小，默认100 | 1-500 |
| direct | string | false | 排序方向，默认为prev | prev升序 or next降序 |

## Returns
| 字段名称 | 数据类型 | 描述 |
|:-:|:-:|:-:|
| id | long |  |
| type | string | 类型：'deposit', 'withdraw' |
| currency | string | 币种 |
| tx-hash | string | 交易哈希 |
| chain | string | 链名称 |
| amount | float | 数量 |
| address | string | 提币地址 |
| address-tag | string | 提币地址标签 |
| fee | float | 手续费 |
| state | string | 状态 |
| created-at | long | 发起时间 |
| updated-at | long | 最后更新时间 |

## Example
```
{
    "error_code": 1,
    "data": [
        {
            "id": 1171,
            "type": "deposit",
            "currency": "xrp",
            "tx-hash": "ed03094b84eafbe4bc16e7ef766ee959885ee5bcb265872baaa9c64e1cf86c2b",
            "amount": 7.457467,
            "address": "rae93V8d2mdoUQHwBDBdM4NHCMehRJAsbm",
            "address-tag": "100040",
            "fee": 0,
            "state": "safe",
            "created-at": 1510912472199,
            "updated-at": 1511145876575
        },  
        ...
    ]
}
```

# 获取当前手续费率
## Path
GET /fee/fee-rate/get

## Params
| 参数名称 | 数据类型 | 是否必需 | 描述 | 取值范围 |
|:-:|:-:|:-:|:-:|:-:|
| symbols | string | true | 交易对 | "newusdt", "btcusdt", "newbtc" |

## Returns
| 字段名称 | 数据类型 | 描述 |
|:-:|:-:|:-:|
| symbol | string | 交易对 |
| maker-fee | string | 挂单手续费率 |
| taker-fee | string | 吃单手续费率 |

## Example
```
{
    "error_code": 1,
    "data": [
        {
            "symbol": "btcusdt",
            "maker-fee":"0.0001",
            "taker-fee":"0.0002"
        },
        {
            "symbol": "newusdt",
            "maker-fee":"0.002",
            "taker-fee":"0.002"
        },
        {
            "symbol": "newusdt",
            "maker-fee":"0.0015",
            "taker-fee":"0.0018"
        }
    ]
}
```
