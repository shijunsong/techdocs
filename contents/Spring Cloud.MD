# Spring Cloud

### 1. GateWay 网关 

#### 1.1 如何实现路由转发

### 2. [OpenFeign](https://blog.csdn.net/qq_45668004/article/details/136357184)

#### 2.1 代理类生成调用方法

`Feign.builder() .target(TenAPIClient.class, "https://tenapi.cn");` 

#### 2.2 如何组装http请求

> HardCodedTarget(apiType, url ) 类实现了 **Target 接口**，  
> 用来将一个 RequestTemplate 实例转换为一个真正的 Request 实例的。

#### 2.3 核心组件

- **Client**

  > HTTP Client 接口，三种实现：HttpURLConnection、 Apache HttpClient、OkHttp

- **Contract**

  > 解析 interface 中方法的注解和参数，用于得知各个 API 的相关信息。  
  > 这个组件在代码中通过解析各个方法的注解和参数（比如 @RequestLine("GET /v2/yiyan")），  
  > 获得 List<MethodMetadata>，即各个 method 的 meta data，比如 URL、body、header 等等信息。

- **Retryer**

  > 用于实现重试的逻辑

- **RequestInterceptor** 和 **ResponseInterceptor**

  > 是一个拦截接口，可以实现在 Http 请求发送之前或接收到响应之后对内容进行修改。

- **Encoder** 和 **Decoder**

  > 用于序列化请求和反序列化响应的接口

- **InvocationHandlerFactory**

  > 用于创建 InvocationHandler，  
  > JDK 动态代理就是通过 InvocationHandler 来生成代理实例的，  
  > 所以 InvocationHandler 的 invoke() 方法的逻辑就是动态代理走的核心逻辑。

#### 2.4 原理



### 3. Ribbon

负载均衡算法：

RoundRobinRule轮询（默认）：调用次数 % 服务个数取余， 同时调用使用cas算法获取最新次数
RandomRule随机
RetryRule轮询重试
WeightedResponseTimeRule响应速度决定权重
BestAvailableRule最优可用
AvailabilityFilteringRule可用性过滤规则
ZoneAvoidanceRule区域内可用性能最优

### 4. Hystrix

#### 1. 是否自定义过熔断方式





# Spring Cloud Alibaba





# 面试题

### 1. SpringCloud和SpringCloudAlibaba分别组件和作用

- **SpringCloud Netflix**

  > Feign、Eureka、Hytrix、zuul、gateway、Ribbon、zipkin、seluth

<img src="../pictures/microservice/springcloud.png" alt="img"/>

- **SpringCloud Alibaba**

  > Nacos、Dubbo、Sentinal、RocketMQ、Seata
  
  <img src="../pictures/microservice/springcloud alibaba.png" alt="img" />
