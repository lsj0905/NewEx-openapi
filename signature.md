# signature 验证签名

因为使用 HMAC 进行签名计算时，使用不同内容计算得到的结果会完全不同。所以在进行签名计算前，请先对请求进行规范化处理。下面以查询某订单详情请求为例进行说明：

查询某订单详情
```
https://api.newex.io/v1/order/orders?
AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx
&SignatureMethod=HmacSHA256
&SignatureVersion=2
&Timestamp=1571746680
&order-id=1234567890
```

## 签名步骤

#### 1. 请求方法（GET 或 POST），后面添加换行符 “\n”
GET\n

#### 2. 添加小写的访问地址，后面添加换行符 “\n”
api.newex.io\n

#### 3. 访问方法的路径，后面添加换行符 “\n”
/v1/order/orders\n

#### 4. 按照ASCII码的顺序对参数名进行排序。例如，下面是请求参数的原始顺序，进行过编码后
```
AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx
order-id=1234567890
SignatureMethod=HmacSHA256
SignatureVersion=2
Timestamp=1571746680
```

 使用 UTF-8 编码，且进行了 URI 编码，十六进制字符必须大写，如 “:” 会被编码为 “%3A” ，空格被编码为 “%20”。

 时间戳（Timestamp）需要以时间戳格式添加。

#### 5. 经过排序之后
```
AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx
SignatureMethod=HmacSHA256
SignatureVersion=2
Timestamp=1571746680
order-id=1234567890
```

#### 6. 按照以上顺序，将各参数使用字符 “&” 连接
```
AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=1571746680&order-id=1234567890
```

#### 7. 组成最终的要进行签名计算的字符串如下
```
GET\n
api.newex.io\n
/v1/order/orders\n
AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=1571746680&order-id=1234567890
```

#### 8. 用上一步里生成的 “请求字符串” 和你的密钥 (Secret Key) 生成一个数字签名
```
4F65x5A2bLyMWVQj3Aqp+B4w+ivaA7n5Oi2SuYtCJ9o=
```
1.将上一步得到的请求字符串和 API 私钥作为两个参数，调用HmacSHA256哈希函数来获得哈希值。

2.将此哈希值用base-64编码，得到的值作为此次接口调用的数字签名。

#### 9. 将生成的数字签名加入到请求的路径参数里
最终，发送到服务器的 API 请求应该为
```
https://api.newex.io/v1/order/orders?AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&order-id=1234567890&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=1571746680&Signature=4F65x5A2bLyMWVQj3Aqp%2BB4w%2BivaA7n5Oi2SuYtCJ9o%3D
```
把所有必须的认证参数添加到接口调用的路径参数里

把数字签名在URL编码后加入到路径参数里，参数名为“Signature”。
