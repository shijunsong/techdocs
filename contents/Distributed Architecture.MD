# 分布式基础理论

### 1. CAP理论

一致性（Consisency）
可用性（Availability）
分区容错性（Partition tolerance）



# 分布式ID服务架构

### 1. 雪花算法

- 架构图

  <img src="../pictures/architecture/snow algorithm archistruct.png" style="zoom:67%;"/>
  



# 分布式锁

### 1. 解决方案

- 基于数据库实现分布式锁

  > 

- 基于缓存（Redis等）实现分布式锁

  > 

- 基于Zookeeper实现分布式锁

  > 

### 思考

1. 分布式锁设计需要考虑哪些方面？

# 分布式事务

### 1. Saga

>两种协调逻辑：编排 和 控制，
>两种恢复模式：向前恢复和向后恢复，
>适用场景：适用调用链路比较长

### 2. TCC（Try Confirm Cancel) 

> 适用场景：资金数据，银行业务，金融业务，涉及到交易、支付、账务，**超卖**，都可以考虑。
> 缺点：业务侵入比较大
> 代表中间件：ali  Seata

### 3. 2PC

### 思考

