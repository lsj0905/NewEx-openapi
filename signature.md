# signature 验证签名

## 签名说明
API 请求在通过 internet 传输的过程中极有可能被篡改，为了确保请求未被更改，除公共接口（基础信息，行情数据）外的私有接口均必须使用您的 API Key 做签名认证，以校验参数或参数值在传输途中是否发生了更改。

一个合法的请求由以下几部分组成：

1.方法请求地址：即访问服务器地址 api.open.newex.io，比如 api.open.newex.io/v1/order/orders

2.API 访问密钥（AccessKeyId）：您申请的 API Key 中的 Access Key。

3.签名方法（SignatureMethod）：用户计算签名的基于哈希的协议，此处使用 HmacSHA256。

4.签名版本（SignatureVersion）：签名协议的版本，此处使用2。

5.时间戳（Timestamp）：您发出请求的时间 (UTC 时间) 。在查询请求中包含此值有助于防止第三方截取您的请求。

6.必选和可选参数：每个方法都有一组用于定义 API 调用的必需参数和可选参数。可以在每个方法的说明中查看这些参数及其含义。 请一定注意：对于 GET 请求，每个方法自带的参数都需要进行签名运算； 对于 POST 请求，每个方法自带的参数不进行签名认证，即 POST 请求中需要进行签名运算的只有 AccessKeyId、SignatureMethod、SignatureVersion、Timestamp 四个参数，其它参数放在 body 中。

7.签名：签名计算得出的值，用于确保签名有效和未被篡改。


## 签名步骤

因为使用 HMAC 进行签名计算时，使用不同内容计算得到的结果会完全不同。所以在进行签名计算前，请先对请求进行规范化处理。下面以查询某订单详情请求为例进行说明：

查询某订单详情
```
https://api.open.newex.io/v1/order/orders?
AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx
&SignatureMethod=HmacSHA256
&SignatureVersion=2
&Timestamp=1571746680
&order-id=1234567890
```

#### 1. 请求方法（GET 或 POST），后面添加换行符 “\n”
GET\n

#### 2. 添加小写的访问地址，后面添加换行符 “\n”
api.open.newex.io\n

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

 时间戳（Timestamp）需要以时间戳格式添加，单位为秒。

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
api.open.newex.io\n
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

最终，发送到服务器的 API 请求应该为：

```
https://api.open.newex.io/v1/order/orders?AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&order-id=1234567890&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=1571746680&Signature=4F65x5A2bLyMWVQj3Aqp%2BB4w%2BivaA7n5Oi2SuYtCJ9o%3D
```
1.把所有必须的认证参数添加到接口调用的路径参数里

2.把数字签名在URL编码后加入到路径参数里，参数名为“Signature”。
