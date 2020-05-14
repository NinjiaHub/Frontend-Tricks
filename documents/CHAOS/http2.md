# HTTP/2

## 特点

* 二进制协议
* 头部压缩
* 多路复用
* 加密传输

## 帧类型

|Frame|Type	Code|描述|
|-----|--------|-----|
|DATA|0x0|传输流的核心内容|
|HEADERS|0x1|包含HTTP首部，和可选的优先级参数|
|PRIORITY|0x2|指示或者更改流的优先级和依赖|
|RST_STREAM|0x3|允许一端停止流（通常是由于错误导致的）|
|SETTINGS|0x4|协商连接级参数|
|PUSH_PROMISE|0x5|提示客户端，服务端要推送些东西|
|PING|0x6|测试连接可用性和往返时延（RTT）|
|GOAWAY|0x7|告诉另外一端，当前端已结束|
|WINDOW_UPDATE|0x8|协商一端要接收多少字节（用于流量控制）|
|CONTINUATION|0x9|用以拓展HEADER数据块|

## 使用帧(frame)的优点

* 基于帧 -- 不需要分块编码，因为数据长度已知
* 流量控制 -- 帧分为不同的类型，`WINDOW_UPDATE` 类型的帧可以告诉对方自己想要接收/发送处理的数据量，窗口的初始大小为 65535字节。每次发送给对方的 `WINDOW_UPDATE` 的值即为下次准备接收的数据量。
* 优先级 -- 通过 HEADERS 帧和 PRIORITY 帧，客户端可以 明确地和服务端沟通它需要什么，以及它需要这些资源的顺序。这是通过声明依赖关系树 和树里的相对权重实现的。

## 与 http/1.x 的加载速度对比

* 对于包含很多小型资源(365 个，每个 2KB)的 Web 页面，h2 加载页面的时间比 h1 更 短。这是因为 h1 下(有 6 个 TCP 连接)服务器只能并行发送 6 个资源(由于队头阻塞)， 而 h2 下多个流(stream)可以共用连接。进一步说，随着网络条件变差，h1 和 h2 下 PLT 都会增加;但是对 h2 的影响更值得注意，因为这是单连接架构造成的。如果唯一 的连接发生了丢包，所有工作都会受影响。
* 对于包含少量大型资源的 Web 页面(10 个，每个 435KB)，在所有网络条件下，h1 性 能上都比 h2 表现要好。这个多少令人感到意外的结果是初始拥塞窗口导致的。 如果开启 6 个连接，h1 的初始拥塞窗口大小实际上是 h2 的 6 倍。这意味着，在会话开 始阶段，h2 的连接窗口尚未增长到最佳值，但 h1 早就能更快地传输更多数据了。这个 问题目前仍在解决，因为它导致初始拥塞窗口对 h2 而言太小，然而对 h1 而言又太大。
* 对于包含一些极大型资源的 Web 页面，两者没有任何差异。h2 的初始拥塞窗口劣势被 整体下载时长掩盖了，多路复用此时也不再具有优势。

HTTP/2 主要解决的也就是第一种情况，即包含多个小型资源，这也符合现实生活中的网站的现状。由于 HTTP/2 的特点，也导致了之前 HTTP/1.x 时代一些优化手段在 HTTP/2 中是无效的。