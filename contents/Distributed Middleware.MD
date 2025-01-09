# 分布式

### 分布式基础理论

#### 1. CAP理论

一致性（Consisency）
可用性（Availability）
分区容错性（Partition tolerance）



### 分布式ID服务架构

#### 1. 雪花算法

- 架构图

  <img src="../pictures/architecture/snow algorithm archistruct.png" style="zoom:67%;"/>

### 分布式锁

https://www.cnblogs.com/liuqingzheng/p/11080501.html

#### 1. 解决方案

- 基于数据库实现分布式锁
  - 获取锁时向数据库` insert `数据（使用列唯一性，排他锁）
  - 释放时执行`delete` 删除行数据

- 基于缓存（Redis等）实现分布式锁

  - **使用SETNX方式**：

    代码：`set wukong 1111 NX`
    缺点：故障导致锁失效， 即使设置expire非原子操作

  - **使用setIfAbsent原子指令**：
    代码：`setIfAbsent("lock", "123", 10, TimeUnit.SECONDS)`

  - **setIfAbsent + 释放锁值比对**

    释放前比对下值是否相等，再进行释放

  - **Redisson**（终极方案）

    ```java
      //加锁
      public static boolean acquire(String lockName){
        //声明key对象
        String key = LOCK_TITLE + lockName;
        //获取锁对象
        RLock mylock = redisson.getLock(key);
        //加锁，并且设置锁过期时间3秒，防止死锁的产生  uuid+threadId
        mylock.lock(2,3,TimeUtil.SECOND);
        //加锁成功
        return  true;
      }
    
      //锁的释放
      public static void release(String lockName){
        //必须是和加锁时的同一个key
        String key = LOCK_TITLE + lockName;
        //获取所对象
        RLock mylock = redisson.getLock(key);
        //释放锁（解锁）
        mylock.unlock();
      }
    ```

- 基于Zookeeper实现分布式锁

  **原理**：内部是一个分层的文件系统目录树结构，规定同一个目录下只能有一个唯一文件名。

  （1）创建一个目录mylock；
  （2）线程A想获取锁就在mylock目录下创建临时顺序节点；
  （3）获取mylock目录下所有的子节点，然后获取比自己小的兄弟节点，如果不存在，则说明当前线程顺序号最小，获得锁；
  （4）线程B获取所有节点，判断自己不是最小节点，设置监听比自己次小的节点；
  （5）线程A处理完，删除自己的节点，线程B监听到变更事件，判断自己是不是最小的节点，如果是则获得锁。

  **方案**：Curator（Apache的开源库）提供的InterProcessMutex是分布式锁的实现，acquire方法用于获取锁，release方法用于释放锁。

  **优点**：具备高可用、可重入、阻塞锁特性，可解决失效死锁问题。

  **缺点**：因为需要频繁的创建和删除节点，性能上不如Redis方式。

#### 2. 设计需要考虑点

- 在分布式系统环境下，一个方法在同一时间只能被一个机器的一个线程执行

- 高可用的获取锁与释放锁

  > 在分布式环境中，网络通信的性能损耗比单机环境大，分布式锁的设计需要尽量减少对系统性能的影响

- 高性能的获取锁与释放锁

  > 分布式锁服务需要保证高可用性，避免因为服务不可用导致整个系统不可用。

- 可重入特性

  > 线程可以重复获取同一把锁，而不会造成死锁

- 锁失效机制，防止死锁

  > 为了避免应用程序因为故障而未能释放锁，需要设计超时机制来确保锁在一定时间后被自动释放

- 非阻塞锁特性，即没有获取到锁将直接返回获取锁失败

  > 业务逻辑需要考虑锁是否被立即获取，对于不能立即获取锁的情况，需要支持非阻塞等待或立即返回

### 分布式事务

#### 1. Saga

- 两种协调逻辑：编排 和 控制，  
- 两种恢复模式：向前恢复和向后恢复，  
- 适用场景：适用调用链路比较长  

#### 2. TCC（Try Confirm Cancel) 

- 适用场景：资金数据，银行业务，金融业务，涉及到交易、支付、账务，**超卖**，都可以考虑。  
- 缺点：业务侵入比较大

#### 3. 2PC

# 中间件

### [Kafka](https://cloud.tencent.com/developer/article/1828222)

#### 1. 集群架构

<img src="../pictures/middleware/kafka cluster.png" alt="img"/>

- Producer： 

  **三种确认机制**：

  - **同步发送**(producer.send())

    > Producer 会等待 Kafka 确认消息已经被成功写入到本地 Broker 之后才会继续执行后续操作。  
    > 这种方式可以保证消息一定会被写入到 Kafka，但可能会降低吞吐量。

  - **异步发送**(producer.send(msg, callback))

    > Producer 不会等待 Kafka 的确认消息，而是直接返回一个发送结果。  
    > 如果消息发送失败，Producer 可以根据返回的错误信息进行重试或者其他处理。  
    > 这种方式可以提高吞吐量，但无法保证消息一定会被写入到 Kafka。

  - **幂等发送**

    > 在幂等性发送模式下，Kafka 会为每个主题分配一个唯一的序列号，Producer 在发送消息时会包含这个序列号。  
    > 如果消息已经被写入到 Kafka，那么 Kafka 会认为这是一个重复的消息，并忽略它。  
    > 这种方式可以确保消息不会重复写入到 Kafka，但需要额外的逻辑来处理重复的消息。

- Broker ： 分区管理

  - Topic ：Topic 是一个逻辑的概念， 生成者消费者使用的直接交互者
    - partition：log文件
      - record：消息记录。每个 record 包含了 key、value 和 timestamp
    - offset：消息在分区中的唯一标识，保证消息在分区内的顺序性

  - Replication：设置topic副本

- Consumer

- Zookeeper

  - **存储所有的集群信息和元数据信息**

  - **选举Controller Broker过程， broker start时**

  <img src="../pictures/middleware/kafka zookeeper.png" alt="img"/>

  - **Controller作用**

    1. *处理 Broker 节点的上线和下线**，包括自然下线、宕机和网络不可达导致的集群变动，Controller 需要及时更新集群元数据，并将集群变化通知到所有的 Broker 集群节点；**
    2. 创建 Topic 或者 Topic 扩容分区**，Controller 需要负责分区副本的分配工作，并主导 **Topic 分区副本的 Leader 选举**。**
    3. 管理集群中所有的副本和分区的状态机**，**监听状态机变化事件**，并作出相应的处理。Kafka 分区和副本数据采用状态机的方式管理，分区和副本的变化都在状态机内会引起状态机状态的变更，从而触发相应的变化事件。

  - **脑裂问题**：

    > 原因：控制器所在broker挂掉 或者 zookeeper出现假死， 选举出新的控制器，被取代的控制器又恢复正常
    > 解决方案：controller_epoch持久节点，epoch number解决

#### 2. [保证消息可靠性](https://cloud.tencent.com/developer/article/1589157)

- **两个核心条件**：**1.** 必须是已提交的消息，**2.**N个Broker中至少有` 1 `个存活

- **生产者端**
  - 使用**同步发送或幂等性发送模式**，以确保消息一定会被写入到 Kafka。
  - 配置 **Producer 的重试次数和重试间隔**，以便在发送失败时能够自动重试。
  - 使用**幂等性生产者**（Idempotent Producer），确保即使消息重复发送也不会导致数据不一致。
  - 配置 **Kafka 的副本因子（Replication Factor）**，以提高数据的可靠性和容错能力。
  - 定期监控 Kafka 集群的健康状况和性能指标，及时发现并解决问题。

- **消费者端**

  `kafka`通过先消费消息，后更新`offset`，来保证消息不丢失， 但是可能会出现重复消费问题

- 总结：

  - producer端使用`producer.send(msg, callback)`
  - 设置`acks = all`（表明所有`Broker`都要接收到消息）
  - 设置`retries`为一个较大的值
  - 设置`unclean.leader.election.enable = false`
  - 设置`replication.factor >= 3`（保存多份消息冗余）
  - 设置`min.insync.replicas > 1`（消息至少要被写入到多少个副本才算是“已提交”）

#### 3. 提高吞吐量方式

- producer端使用异步发送，需要做好失败方案
- 增加分区partition个数，提高并发量

#### 4. 集群部署规划

Zookeeper 节点和 Kafka 节点共用同一台物理机。

| IP 地址     | 角色                                 |
| ----------- | ------------------------------------ |
| 192.168.1.6 | Kafka Broker，Zookeeper，Kafka Eagle |
| 192.168.1.7 | Kafka Broker，Zookeeper              |
| 192.168.1.8 | Kafka Broker，Zookeeper              |

#### 5. ack 机制

在Kafka发送数据的时候，每次发送消息都会有一个确认反馈机制，确保消息正常被收到。

可以通过设置`request.required.acks `决定 ack 策略。

> - `0`: 生产者不会等待 broker 的 ack，这个延迟最低但是存储的保证最弱。当 server 挂掉的时候就会丢数据；
> - `1`：服务端会等待 ack 值。leader 副本确认接收到消息后发送 ack 。如果 leader 挂掉后，不确保是否复制完成，也就是说新 leader 可能会丢失数据；
> - `-1`：同样在 1 的基础上，服务端会等所有的 follower 的副本收到数据后， leader 才会发出 的 ack，这样数据就不会丢失。

### RocketMQ

### Seata

