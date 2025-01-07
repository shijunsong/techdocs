# GateWay 网关 





# OpenFeign

底层原理，如何组装http请求的



# Eureka



# ribbon

负载均衡算法：

RoundRobinRule轮询（默认）：调用次数 % 服务个数取余， 同时调用使用cas算法获取最新次数
RandomRule随机
RetryRule轮询重试
WeightedResponseTimeRule响应速度决定权重
BestAvailableRule最优可用
AvailabilityFilteringRule可用性过滤规则
ZoneAvoidanceRule区域内可用性能最优



# hystrix

#### 是否自定义过熔断方式