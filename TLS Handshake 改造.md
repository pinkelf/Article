## 背景
SSL建连需要2个RTT和Server交换证书、协商密钥等，在移动端弱网的情况下体验比较差，这严重影响公司业务API推广HTTPS通道，另外移动端应用的新协议如SPDY/HTTP 2.0的前提也是如何保证有效建立SSL连接。

IETF发布的The Transport Layer Security (TLS) Protocol Version 1.3 中提出了如何使用1-RTT甚至0-RTT建立SSL连接，目前TLS 1.3处于草案阶段，还没有具体的实现。腾讯和手淘基于TLS 1.3的标准各自实现了对TLS握手的改造，实现了1-RTT和0-RTT，优化了SSL建连成本。

## 已完成工作
我们参考了TLS 1.3以及腾讯和手淘方案，尝试基于Chromium网络栈和Nginx对TLS握手进行改造，目前已经完成的工作如下：
### 客户端改造
在客户预埋服务端的证书，减少证书交换环节
- TLS Handshake请求流程改造
- SSL_Read改造
- SSL_Write改造
- 移除NPN/ALPN协商过程，默认支持SPDY

### 服务端改造
- TLS Handshake处理流程改造
- SSL_Read改造
- SSL_Write改造
- 移除NPN/ALPN协商过程, 默认支持SPDY

## 后续工作
- 修复客户端读取SPDY Response时的Crash问题
- 完善SPDY协议协商代码
- 自定义Handshake数据包首字节，扩展nginx处理Handshake的逻辑，以实现客户端主动降级的方案
- 测试Handshake时间和首包返回时间
- 基于随机数生成私钥
- SessionKey计算
- 数据加解密
- 故障处理、降级方案
- …

## 首次建连时间对比
### 测试方法及环境
- 基于Tengine搭建的Local server
- 通过SPDY协议请求newapi.mogujie.com
- 测试设备 HM Note
- 网络环境 mogujie-guest

### 说明
- 以下数据是请求发出到数据返回的时间，包括TLS握手时间
- SPDY协议会保持并复用连接，因此我们只关注首次请求返回时间(建立连接时间)
- 因为服务端部署在Local，所以无法测试3G/4G情况

|Handshake|平均时间 (ms)|最长时间 (ms)|最短时间 (ms)
|:---:|:---:|:---:|:---:|
|MoguSSL|440|587|277
|TLS|588.6|941|430

![](http://s17.mogucdn.com/new1/v1/bmisc/66c12868d460cbc3cf19f3a5ffe131db/176964141097.png
)

![](http://s17.mogucdn.com/new1/v1/bmisc/d2f51160884bacb2378bc6aa8cc69402/176964231757.png
)
