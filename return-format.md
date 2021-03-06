# 请求格式

所有的API请求都以GET或者POST形式发出。对于GET请求，所有的参数都在路径参数里；对于POST请求，所有参数则以JSON格式发送在请求主体（body）里。


# 返回格式

所有的接口返回都是JSON格式。在JSON最上层有几个表示请求状态和属性的字段："status", 和 "ts". 实际的接口返回内容在"data"字段里.

## 返回内容格式
| 参数名称 | 数据类型 | 描述 |
| :- | :-: | :-: |
| status | string | "ok" or "error" |
| error_code | string | 错误码，具体错误码请见列表 |
| error_message | string | 错误消息 |
| data | object | 接口返回数据主体 |

## 错误码列表
| 错误码 | 描述 |
| :- | :-: |
| 500 | 内部错误 |
| 1001 | 签名参数错误 |
| 1002 | access_key不存在 |
| 1003 | access_key过期 |
| 1004 | 验签失败 |
| 1005 | timestamp超过当前时间5秒 |
| 1006 | IP验证失败 |
| 1007 | 错误的交易对 |
| 1008 | 错误的币种 |
| 1009 | 用户不存在 |
| 1010 | 参数验证失败 |
