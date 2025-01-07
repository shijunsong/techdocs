redis 数据同步方案
1. 使用消息中间件
2. 使用三方插件，vitess

Redis雪崩 是指在 Redis 缓存系统中，当大量缓存同时失效时，所有请求直接打到数据库，导致数据库瞬间压力激增，甚至崩溃的现象

缓存穿透：缓存和数据库中都没有的数据，可用户还是源源不断的发起请求，导致每次请求都会到数据库，从而压垮数据库。

布隆过滤器

缓存击穿问题也叫热点Key问题，就是⼀个被高并发访问并且缓存重建业务较复杂的key突然失效了
热点key，即只是在一段时间内，其被访问的频次很高



#### redis 数据结构（核心RedisObject）

String（3种数据结构存储方式）， List（2种）， Set（无序集合，2种），ZSet（有序集合，2种），Hash表（2种）

#### 过期删除策略

定期删除和惰性删除（定期删，查的时候删）

#### redis淘汰策略

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

#### 内存淘汰算法

- 随机
- TTL（过期时间）
- LRU（Least Recently Used，最近最少使用）
- LFU（Least Frequently Used，最不经常使用）

#### 四种集群部署模式

客户端分片
twemproxy： 只请求转发，无法在线扩容、缩容，运维不方便
Codis：proxy-请求读写转发，
	      dashboard-故障自动恢复，数据在线迁移，节点缩容扩容，自动化运维api
	      group-基于3.2.8版本开发的redis server，增加异步数据迁移
	      fe-管理多个集群UI界面

Redis Cluster：官方集群方案，支持数据节点迁移扩容，内置哨兵模式故障恢复功能

​	      通过smart-client进行路由转发
​	      节点数据迁移、扩容缩容时，告知客户端到正确节点
​	      缺点：迁移性能不高，大key的时候导致节点阻塞	      

#### 冷热备份（linux corntab定时任务， dump.rdb）

一小时，保留48小时
每天备份，保留一个月
本机备份，上传到云服务器

#### 性能调优

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

#### 数据迁移

#### 分布式锁

`jedis.set(keys,args,"NX","PX",3000);`
该命令仅在密钥尚不存在时才设置密钥（NX选项），到期时间为3000毫秒（PX选项，EX选项单位为秒）

#### redis为什么那么快

redis client方案

Jedis

Redisson

集群执行lua脚本时键不在那个节点上怎么办