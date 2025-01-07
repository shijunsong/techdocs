# Network 网络



### 网络体系结构

#### 1. OSI七层

#### 2. TCP/IP五层

- 传输层

  Tcp： transmission control protocol

  - 端口：65535个， 端口由16位二进制表示，2^16 - 1(除0) = 65535个

- 网络层

  IP: Internet protocol

- 数据链路层

  Lvs：linux virtual server， 负载均衡

- 一个链接五个确定维度

  源ip，目标ip，源port，目标port，协议号

#### 3. TCP (TransmissionControl Protocol)

- Transmission Control Protocol, 传输控制协议
- 特性：`面向链接，可靠的数据流服务，一次建立，超时重传 & 应答机制保证其可靠性，全双工` 。
- 三次握手详细过程及标志位
  - 漏洞：SYN洪泛攻击

- 四次挥手

#### 4. Http三次握手过程





# RPC

## 1. 架构



## 2. 基础知识

- 调用模块-代理
- 序列化
- 协议编码
- 网络模块

## 3. 原理



# Socket



# 面试

### 1.socket 与 rpc， http协议， dubbo协议区别

- rpc: 进程之间通信方式，远程过程调用，是一种服务调用模式。包含 **传输协议** 和 **序列化协议**

  <img src="https://img2020.cnblogs.com/blog/980882/202006/980882-20200610111520262-1414556598.jpg" alt="img" style="zoom: 67%;" />

- http协议 相较于 自定义tcp 报文协议：**增加的开销在于连接的建立与断开。**

- 比如著名的 grpc，它底层使用的是 http2 协议；还有 dubbo 一类的自定义报文的 tcp 协议



