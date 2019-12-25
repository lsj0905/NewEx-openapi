# 请求格式

所有的API请求都以GET或者POST形式发出。对于GET请求，所有的参数都在路径参数里；对于POST请求，所有参数则以JSON格式发送在请求主体（body）里。


# 返回格式

所有的接口返回都是JSON格式。在JSON最上层有几个表示请求状态和属性的字段："status", 和 "ts". 实际的接口返回内容在"data"字段里.

## 返回内容格式
| 参数名称 | 数据类型 | 描述 |
| :- | :-: | :-: |
| status | string | "ok" or "error" |
| ts | string | 接口返回的调整为北京时间的时间戳，单位毫秒 |
| error_code | string | 错误码，具体错误码请见列表 |
| error_message | string | 错误消息 |
| data | object | 接口返回数据主体 |
