# 多线程与并发

### 1. 响应比

> 响应比 = (等待时间 + 要求服务时间) / 要求服务时间

### 2. 线程池设置大小公式

> 线程数 = 可用核心数 * 目标 CPU 利用率 * (1 + (等待时间 / 服务时间))

`目标 CPU 利用率`：是您希望应用程序使用的 CPU 时间百分比(**CPU消耗/内核的CPU消耗的比率在65%~70%/30%~35%左右**)  
`等待时间`：是线程等待 I/O 操作完成所花费的时间(**top 命令中cpus `wa<iowait>`参数**)  
`服务时间`：是线程执行计算所花费的时间  

### 3. 常用线程池

- ThreadExecutorPool





# 锁

### 1. AQS 原理及应用

### 2. CAS原理及应用类

### 3. synchronized原理

### 4. volatile关键字原理

### 锁升级原理

<img src="../pictures/java/lock upgrade.png" alt="img" style="zoom:70%;" />

# 面试题

### 1. CompletableFuture手动事务提交

```java
@Service
public class MyService{
    @Autowired
    DataSourceTransactionManager transactionManager;
    
    public ResultMap lockStockWhenNewOrder(List<StockChangeByOrderDto> goodsModels) {
        //2.获取事务定义
        DefaultTransactionDefinition def = new DefaultTransactionDefinition();
        //3.设置事务隔离级别，开启新事务
       def.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRES_NEW);
        //4.获得事务状态，相当于开启事物
        TransactionStatus transactionStatus = transactionManager.getTransaction(def);
        try{
            //insert or update ...
            transactionManager.commit(transactionStatus);
        } catch (InterruptedException e) {
            transactionManager.rollback(transactionStatus);
        }
    }
}
```

### 2. 防止内存泄漏的策略包括

- 确保对象仅在需要时才处于范围内；
- 谨慎使用静态字段；
- 避免无限增长的静态集合；
- 在不再需要时，始终取消注册侦听器和回调；
- 限制缓存的大小；
- 在不再需要对象时，从集合中删除它们；
- 谨慎使用内部类实例；
- 使用后始终关闭资源（例如文件，流，连接）；
- 分析应用程序的内存使用情况；
- 单元和集成测试以检查内存泄漏。











# 1. 并发三要素

原子性，有序性，一致性

# 2. 对线程安全的理解

判断是否形成线程竞态条件

# 2. 锁概念

<img src="https://picx.zhimg.com/v2-f51a9f072bf5a8054fa78acdb15126cb_1440w.jpg" alt="img" style="zoom:70%;" />

| 应用名称               | 乐观/悲观锁 | 自旋锁 | 可重入锁 | 读/写锁 | 公平/非公平锁 | 共享/独占锁        | 重量/轻量 | 偏向锁 | 分段锁 | 互斥/同步 |
| ---------------------- | ----------- | ------ | -------- | ------- | ------------- | ------------------ | --------- | ------ | ------ | --------- |
| CAS                    | √(L)        | √      |          |         |               |                    |           |        |        |           |
| synchronized           | √(B)        |        | √        |         | √(F)          | √(D)               | √(Z)      |        |        | √         |
| ReentrantLock          |             |        | √        |         | √(默认F)      |                    |           |        |        |           |
| Lock                   |             |        | √        |         |               |                    |           |        |        |           |
| ReentrantReadWriteLock |             |        |          | √       |               | 读√(G)<br />写√(D) |           |        |        |           |
| CopyOnWriteArrayList   |             |        |          | √       |               |                    |           |        |        |           |
| CopyOnWriteArraySet    |             |        |          | √       |               |                    |           |        |        |           |
| ConcurrentHashMap      |             |        |          |         |               | √(D)               |           |        | √      |           |
| HashTable              | √(B)        |        |          |         |               | √(D)               |           |        |        |           |
| Vector                 | √(B)        |        |          |         |               | √(D)               |           |        |        |           |
|                        |             |        |          |         |               |                    |           |        |        |           |

# 3. 如何查看线程死锁

1.可以通过jstack命令进行查看，
2.数据库死锁查询方式

```sql
1. 查询是否锁表
show OPEN TABLES where In_user > 0;
2. 查询进程
show processlist;
3.查正在锁的事务
select * from infomation_schema.INNODB_LOCKS;
4. 查看等待锁的事务
select * from infomation_schema.INNODB_LOCKS_WAITS;
```

# 4. 锁的重入

- 加了两把，只释放一把会有什么问题？

  `答：` 程序卡死，线程不能出来，也就是说我们申请了几把锁，就需要释放几把锁。

- 加了一把，释放两次会有什么问题？

  `答：` 会报错，java.lang.IllegalMonitorStateException。

# 4. CountDownLatch和Semaphore的区别和底层原理

- CountDownLatch表示计数器，可以给CountDownLatch设置一个数字，
  一个线程调用await()将会阻塞，
  其他线程可以调用countDown()方法来对CountDownLatch中的数字减一，
  当数字被减成0后，所有await的线程都将被唤醒。
  对应的底层原理就是，调用await()方法的线程会利用AQS排队，一旦数字被减为0，则会将AQS中排队的线程依次唤醒。
- Semaphore表示信号量，可以设置许可的个数，
  表示同时允许最多多少个线程使用该信号量，
  通过acquire()来获取许可，如果没有许可可用则线程阻塞，并通过AQS来排队，
  可以通过release()方法来释放许可，
  当某个线程释放了某个许可后，会从AQS中正在排队的第一个线程开始依次唤醒，直到没有空闲许可。

# 5. Sychronized的自旋锁、偏向锁、轻量级锁、重量级锁

1.**偏向锁**: 在锁对象的对象头中记录一下当前获取到该锁的线程ID，该线程下次如果又来获取该铁就可以直接获取到了
2.**轻量级锁**: 由偏向锁升级而来，当一个线程获取到铁后，此时这把锁是偏向锁，此时如果有第二个线程来竞争锁，偏向锁就会升级为轻量级锁之所以叫轻量级锁，是为了和重量级锁区分开来，轻量级锁底层是通过自旋来实现的，并不会阻塞线程
3.**自旋锁**: 自旋锁就是线程在获取锁的过程中，不会去阻塞线程，也就无所谓唤醒线程，阻塞和唤醒这两个步骤都是需要操作系统去进行的，比较消耗时间，自旋锁是线程通过CAS获取预期的一个标记，如果没有获取到，则继续循环获取，如果获取到了则表示获取到了锁，这个过程线程直在运行中，相对而言没有使用太多的操作系统资源，比较轻量。
4.**重量级锁**：如果自旋次数过多仍然没有获取到锁，则会升级为重量级锁，更量级锁会导致线程阻塞

`锁升级过程`

![锁升级过程](C:/Users/Administrator/Desktop/锁升级过程.png)

# 5. ReentrantLock中tryLock()和lock()方法的区别

`1. `tryLock()表示尝试加锁，可能加到，也可能加不到，
该方法不会阻塞线程，如果加到锁则返回true，没有加到则返回false
`2. `lock()表示阻塞加锁，线程会阻塞直到加到锁，方法也没有返回值

# 5. ReentrantLock公平锁与非公平锁区别

公平锁：获取锁时需要再AQS队列中排队
非公平锁：线程在获取锁时，先尝试加锁， 如果未获取到锁则加入等待队列
ReentrantLock，**默认：非公平锁**，可重入锁，在锁释放时会唤醒排在最前面的线程。

# 6. Java死锁如何避免

- 造成死锁的几个原因:
  1.一个资源每次只能被一个线程使用
  2.一个线程在阻塞等待某个资源时，不释放已占有资源
  3.一个线程已经获得的资源，在未使用完之前，不能被强行剥夺
  4.若干线程形成头尾相接的循环等待资源关系
  这是造成死她必须要达到的4个条件，如果要游免死锁，只需要不满足其中某一个条件肌可，而其中前3个条件是作为就要符合的条件，所以要游免死锁就需要打破笔4个条件不出现循环等待锁的关系。
- 在开发过程中:
  1.要注意加铁顺序，保证每个线程按同样的顺序进行加铁
  2.要注意加锁时限，可以针对所设置一个超时时间
  3.要注意死锁检查，这是一种预防机制，确保在第一时间发现死铁并进行解决

# 7. volatile关键字如何保证可见性，有序性

`1. `对于加了volatie关键字的成员变量，在对这个变量进行修改时，会直接将CPU高级缓存中的数据写回到主内存，对这个变量的读取也会直接从主内存中读取，从而保证了可见性
`2. `在对volatile修饰的成员变量进行读写时，会插入内存屏障，而内存屏障可以达到禁止重排序的效果，从而可以保证有序性

# 8. sychronized和ReentrantLock的区别

`1. `sychronized是一个关键字，ReentrantLoch是一个类
`2. `sychronized会自动的加锁与释放锁，ReentrantLock需要程序员手动加锁与释放锁
`3. `sychronized的底层是JVM层面的锁，ReentrantLock是API层面的锁
`4. `sychronized是非公平锁，ReentrantLock可以选择公平锁或非公平锁
`5. `sychronized锁的是对象，锁信息保存在对象头中，ReentrantLock锁的是线程，通过代码中int类型的state标识来标识锁的状态
`6. `sychronized底层有一个锁升级的过程

# 简述线程池原理，FixedThreadPool用的阻塞队列是什么

线程池内部是通过队列+线程实现的，当我们利用线程池执行任务时:
`1. `如果此时线程池中的数量小于corePoolsize，即使线程池中的线程都处于空闲状态，也要创建新的线程来处理被添加的任务。
`2. `如果此时线程池中的数量等于corePoolSize，但是缓冲队列workQueue未满，那么任务被放入缓冲队列。
`3. `如果此时线程池中的数量大于等于corePoolsize，缓)中队列workQueue满，并且线程池中的数量小于maximumPoolsize，建新的线程来外理被添加的任务。
`4. `如果此时线程池中的数量大于corePoolsize，缓冲队列workQueue满，并且线程池中的数量等于maximnumpoolize，那么通过 handler所指定的策路来处理此任务,。
`5. `当线程池中的线程数量大于corePoolSize时，如果某线程空闲时间超过keepAliveTime，线程将被终止。这样，线程池可以动态的调整池中的线程数
FixedThreadPool代表定长线程池，底层用的LinkedBlockingQueue，表示无界的阻塞队列。

# 线程之间如何进行通讯

`1. `线程之间可以通过共享内存或基于网络来进行通信
`2. `如果是通过共享内存来进行通信，则需要考虑并发问题，什么时候阻塞，什么时候唤醒
`3. `像Java中的wait0)、notify0就是阻塞和唤醒
`4. `通过网络就比较简单了，通过网络连接将通信数据发送给对方，当然也要考虑到并发问题，处理方式就是加锁等方式

