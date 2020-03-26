# Binance 对接

* 参考API文档： https://binance-docs.github.io/apidocs/spot/cn/#b122f813d5 

* 交易所地址： https://www.binance.com/ 

***

## API文档介绍

### 基本信息

#### API基本信息

* 接口可能需要用户的API key
* 接口的baseurl： **https://api.binance.com** 
* 所有接口的相应都是json格式
* 响应按时间升序排列
* 所有时间、时间戳均为UNIX时间，单位为毫秒
* HTTP返回码具体参考官方说明

#### 接口基本信息

* GET方法的接口，参数必须放在query string中发送
* POST、PUT和DELETE方法的接口，参数可以在内容形式为application/x-www-from-urlencoded的query string中发送，也可以在request body中发送，也可以混用。
* 对参数顺序不做要求
* 如果同一个参数名在query string和request body都有，query string中的优先被采用。

#### 访问限制

* 时间表示方式

  * 秒：S
  * 分：M
  * 时：H
  * 天：D
* 违反任何速率限制（包括IP限制和账户限制）时，将返回429，收到429时，应该退回而不发送更多请求，否则将导致API的IP被禁止（http状态418）

#### 接口鉴权类型

* NONE：不需要鉴权
* TRADE：API-Key + 签名
* USER_DATA：API-Key + 签名
* USER_STREAM：API-Key 
* MARKET_DATA：API-Key 

