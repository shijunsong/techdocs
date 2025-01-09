### Redis 数据结构（核心RedisObject）

String（3种数据结构存储方式），
List（2种），
Set（无序集合，2种），
ZSet（有序集合，2种），
Hash表（2种）

### 雪崩，缓存穿透，缓存击穿

- Redis雪崩 

  > 定义：Redis 缓存系统中，当大量缓存同时失效时，所有请求直接打到数据库，导致数据库瞬间压力激增，甚至崩溃的现象
  > 解决方案：

- 缓存穿透：

  > 定义：缓存和数据库中都没有的数据，可用户还是源源不断的发起请求，导致每次请求都会到数据库，从而压垮数据库。
  > 解决方案：1. 布隆过滤器    2. 对于不存在的key可缓存一个value = null的数据

- 缓存击穿（热点Key问题）

  > ⼀个被高并发访问并且缓存重建业务较复杂的key突然失效了
  > 热点key，即只是在一段时间内，其被访问的频次很高

### 数据删除清理

#### 1. 过期删除策略

定期删除和惰性删除（定期删，查的时候删）

#### 2. redis淘汰策略

配置内存（`maxmemory`不能超过内存大小）

- ##### no-envicition（默认）

- ##### **allkeys**-random

- ##### allkeys-lru

- allkeys-lfu

- ##### volatile-random

- ##### volatile-ttl

- ##### volatile-lru

- ##### volatile-lfu

```
config get maxmemory-policy    //获取当前内存淘汰策略
config set maxmemory-policy valatile-lru  //通过命令修改淘汰策略
```

#### 3. 内存淘汰算法

- 随机
- TTL（过期时间）
- LRU（Least Recently Used，最近最少使用）
- LFU（Least Frequently Used，最不经常使用）

### [四种部署模式](https://cloud.tencent.com/developer/article/2222406)

#### 1. 单机

**特点**：RDB和AOF持久化机制能将数据持久化，读写无法分离

#### 2. 主从复制

> Redis 提供了复制（replication）功能实现master数据库中的数据更新后，会自动将更新的[数据同步](https://cloud.tencent.com/product/datainlong?from_column=20065&from=20065)到其他slave数据库上。

<img src="../pictures/middleware/redis lf.png" alt="img"/>

**特点**：一个master可以有多个salve节点；salve节点可以有slave节点，从节点是级联结构。  
**优点**：主从结构具有读写分离，提高效率、数据备份，提供多个副本等优点。  
**缺点**：不具备自动容错和恢复功能，主节点故障，集群则无法进行工作，可用性比较低，从节点升主节点需要人工手动干预。  

> 1. `SLAVE NO ONE`命令将从数据库提升成主数据继续服务  
> 2. 启动之前崩溃的主数据库，然后使用`SLAVEOF`命令将其设置成新的主数据库进行数据同步 

#### 3. 哨兵（2.6以后版本支持）

<img src="../pictures/middleware/redis sentinel.png" />

**核心是redis主从同步/复制模式， 系统会启动一个sentinel进程**  
**解决可主从模式中master故障不可以自动切换故障的问题。**

**实现原理**：

> 1. 建立连接：启动读取配置文件，找master节点，通过`info`命令获取slave节点信息  
> 2. 订阅主数据库_sentinel_:hello频道以获取同样监控该数据库的哨兵节点信息  
> 3. 定期向主数据库发送info命令，获取主数据库本身的信息。  

- 监控主/客观下线

> 1. 发送**ping命令超过一定时间**(down-after-millisecond)**未回复**
> 2. 发送命令(SENTINEL is-master-down-by-addr) 询问**其他哨兵节点**是否也是主观下线，当达到指定数量(quorum)时

- 主从切换

> 1. 选出领头哨兵
>
> 2. 领头哨兵所有的slave选出优先级最高的从数据库
>
>    优先级：**首先：**slave-priority选项  ，**其次：**从数据库复制的命令偏移量（最大），**最后：**run ID较小

**主从区别**：新master选举后，之前的master会变成slave

**缺点**：

> 1. 是一种中心化的集群实现方案，只有一个Redis主机接受和处理写请求
> 2. 集群里所有节点保存的都是全量数据，浪费内存空间，非分布式存储，数据量过大影响master性能
> 3. Redis主机宕机后， 哨兵master选举成功期间，redis开启保护机制，禁止写操作

#### 4. [集群](#redis cluster)

> **数据分片存储，解决哨兵模式下节点全量数据问题**
> 为什么RedisCluster会设计成16384个槽？



### <a id="redis cluster">四种集群方案</a>

#### 架构图

<img src="../pictures/middleware/redis cluster.png" alt="img"/>

#### 1. 客户端分片

**内容**：逻辑放在Redis客户端实现(Sharded Jedis)，手动定义分片的程序（使用一致性哈希），把对Key的访问转发到不同的Redis实例中，查询数据时把返回结果汇集  

**缺点**：增删节点需要调整程序，历史数据也会有问题

#### 2. 代理分片(Twemproxy)

- **优点：**
  - 客户端像连接Redis实例一样连接Twemproxy，不需要改任何的代码逻辑。
  - 支持无效Redis实例的自动删除。
  - Twemproxy与Redis实例保持连接，减少了客户端与Redis实例的连接数。

- **缺点：**
  - 由于Redis客户端的每个请求都经过Twemproxy代理才能到达Redis服务器，这个过程中会产生性能损失。
  - 没有友好的监控管理后台界面，不利于运维监控。
  - Twemproxy最大的痛点在于，无法平滑地扩容/缩容。

#### 3. Codis

proxy-请求读写转发，  
dashboard-故障自动恢复，数据在线迁移，节点缩容扩容，自动化运维api  
group-基于3.2.8版本开发的redis server，增加异步数据迁移  
fe-管理多个集群UI界面

#### 4. Redis Cluster

官方推荐，集群部署至少要 3 台以上的master节点，最好使用 3 主 3 从六个节点的模式。Redis Cluster集群具有如下几个特点：

- 集群完全去中心化，采用多主多从；所有的redis节点彼此互联(PING-PONG机制)，内部使用二进制协议优化传输速度和带宽。
- 客户端与 Redis 节点直连，不需要中间代理层。客户端不需要连接集群所有节点，连接集群中任何一个可用节点即可。
- 每一个分区都是由一个Redis主机和多个从机组成，分片和分片之间是相互平行的。
- 每一个master节点负责维护一部分槽，以及槽所映射的键值数据；集群中每个节点都有全量的槽信息，通过槽每个node都知道具体数据存储到哪个node上。

缺点：迁移性能不高，大key的时候导致节点阻塞	      

### 性能调优

`server端`

1.限制内存大小  
2.使用lazy-free特性  
3.使用slowlog优化耗时命令  
4.检查数据持久化策略（通常用混合）  
5.使用分布式架构 6.禁用THP特性

`client端`

1.设置过期时间  
2.禁止使用key *  
3.严格控制数据结构大小  
4.排序，交集 并集 放到客户端执行  
5.删除大数据用异步删除方式unlink， 不阻塞主redis进程

### 数据迁移

#### 1. 冷热备份（linux corntab定时任务， dump.rdb）

一小时，保留48小时
每天备份，保留一个月
本机备份，上传到云服务器



### Redis为什么那么快

### 集群执行lua脚本时键不在那个节点上怎么办