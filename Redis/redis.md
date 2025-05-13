# Redis

## 基础篇
### NoSQL
![alt text](image.png)


### Redis概述
![alt text](image-1.png)


### 通用命令
- `keys pattern`：查询符合给定模式的键
- `del key_name`：删除给定键
- `exists key_name`：检查给定键是否存在
- `expire key_name n_seconds`：为给定键设置过期时间
- `ttl key_name`：查看给定键的剩余过期时间

### Redis数据类型
- `help @数据类型`：查看数据类型相关命令
![alt text](image-2.png)


### String类型
![alt text](image-3.png)

![alt text](image-4.png)


> ![alt text](image-5.png)


### Hash类型
![alt text](image-6.png)

![alt text](image-7.png)



### List类型
List与Java中LinkedList类似，双向链表。

![alt text](image-8.png)


### Set类型
Set与Java中HashSet类似，不允许重复元素。

![alt text](image-9.png)


### SortedSet/ZSet类型
![alt text](image-10.png)

![alt text](image-11.png)

## Redis-Java-Clients
### Jedis
- 引入依赖
    ```xml
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.2.0</version>
    </dependency>
    ```

- 使用jedis
    ```java
    class JedisApplicationTests {

        private Jedis jedis;

        @BeforeEach
        void setUp() {
            // 建立连接
            jedis = new Jedis("$IPAddr", $Port);
            // 设置密码
            jedis.auth("$password");
            // 选择库
            jedis.select(0);
        }

        @Test
        void testString() {
            // 方法名称就是redis的命令名
            String res = jedis.set("username", "kanade");
            System.out.println(res);

            String name = jedis.get("username");
            System.out.println(name);
        }

        @AfterEach
        void tearDown() {
            // 关闭链接
            if (jedis != null) jedis.close();
        }
    }
    ```
> ![alt text](image-12.png)


### Spring Data Redis
![alt text](image-13.png)

---
- Spring封装了Redis的命令为RedisTemplate
![alt text](image-14.png)

![alt text](image-15.png)


## 应用
### 缓存
#### 更新方案
- 选取更新数据库时删除Cache的方案，下次查询时再写入Cache。
![alt text](image-16.png)


#### 缓存穿透
![alt text](image-17.png)
> ![alt text](image-18.png)

#### 缓存雪崩
![alt text](image-19.png)


#### 缓存击穿
逻辑击穿：多个请求同时查询==一个==缓存失效的key，导致大量请求直接访问数据库。
![alt text](image-20.png)


### 超卖问题
![alt text](image-21.png)

### 分布式锁
几种分布式锁的实现方式：
![alt text](image-22.png)

#### Lua脚本
- Redis提供了Lua脚本功能，可以保证原子性操作。
- `eval "$(cat script.lua)" 0`：简单执行lua脚本文件，**用$和cat命令得到脚本内容。**
![alt text](image-23.png)
> ![alt text](image-24.png)

#### Redisson
###### 快速入门
- Redisson是一个分布式协调框架，封装了Redis的一些操作（Lua脚本），提供了多种分布式锁的实现方式。
- 引入依赖
- 配置Redisson
```java
@Configuration
public class RedissonConfig {
    @Bean
    public RedissonClient redissonClient() {
        Config config = new Config();
        config.useSingleServer().setAddress("redis://$IPAddr:$Port").setPassword("$password");
        return Redisson.create(config);
    }
}
```
- 使用Redisson
```java
@Service
public class RedissonService {
    @Autowired
    private RedissonClient redissonClient;

    public void lock() {
        // 获取锁：可重入，制定锁的名称
        RLock lock = redissonClient.getLock("lock");
        // 类setnx命令尝试锁，参数分别是最大等待时间，锁的过期时间，时间单位
        boolean isLock = lock.tryLock(1, 10, TimeUnit.SECONDS);
        if (isLock) {
            try {
                // 业务逻辑
            } finally {
                lock.unlock();
            }
        }
    }
}
```


###### 可重入锁、锁重复、看门狗机制
![alt text](image-25.png)

> ![alt text](image-26.png)
> > tryLock方法中：不设置leaseTime或设置leaseTime为-1，则启动看门狗机制，锁的过期时间为30s，自动续期。
> 1. 正常情况下，看门狗机制正常工作，自动续费锁，只能显式调用unlock方法释放锁。
> 2. 如果业务出现异常，看门狗机制与Redis服务器失去联系，锁经过30s后自动过期，此时锁被自动释放。


###### multi-lock
![alt text](image-27.png)

> 必须同时从各个节点获取锁，才算获取到锁。


### 异步优化
###### 概述
![alt text](image-28.png)



### 消息队列
###### 概述
异步处理超卖订单时，我们开始是使用Java提供的阻塞队列实现的，存在问题。
- 与Java程序绑定，JVM有内存限制，无法处理大量请求。
- 如果队列意外丢失，数据也会丢失。

###### Redis消息队列
![alt text](image-29.png)

- 基于List结构实现的消息队列，生产者和消费者都可以往队列中写入和读取消息。
  - `BLPOP`、`BRPOP`：阻塞式读取，直到队列有消息才返回。
  - ![alt text](image-30.png)
---

- `PubSub`-`Publish/Subscribe`模式：发布者和订阅者模式，订阅者可以订阅一个或多个频道，当有消息发布到该频道时，所有订阅该频道的订阅者都会收到消息。
  - ![alt text](image-31.png)
  - 缺点：
    - 不支持数据持久化
    - 无法避免消息丢失
    - 消息堆积有上限

---
- `Stream`：Redis的一种数据类型，可以实现一个功能完善的消息队列。
- 单消费模式：
  - ![alt text](image-32.png)
  - ![alt text](image-33.png)
  - 缺点：
    - 有漏读风险
- 消费组模式：
  - ![alt text](image-34.png)
  - ![alt text](image-36.png)
  - ![alt text](image-35.png)

> 同一个消费者组中的消费者共用一个游标，但同一个消息队列的不同消费者组游标相互独立。

### Feed流
- Feed：投喂。推送行为。
![alt text](image-37.png)

### GEO数据结构
![alt text](image-38.png)

#### 附近商铺功能
首先需要将数据库中的商铺经纬度信息存入Redis的GEO数据结构中。
因为MySQL没有提供处理类GEO数据结构的函数，所以需要使用Redis的命令实现。


### BitMap
- 01串：Redis中下标从左到右数。
![alt text](image-39.png)


### HyperLogLog
#### 基本概念
![alt text](image-40.png)


#### 应用
![alt text](image-41.png)


## 高级
### 分布式缓存
#### Redis持久化
###### RDB持久化
![alt text](image-42.png)

![alt text](image-43.png)

> ![alt text](image-44.png)


###### AOF持久化
![alt text](image-45.png)

![alt text](image-46.png)

> ![alt text](image-47.png)

###### RDB和AOF的对比
![alt text](image-57.png)

#### 主从架构
###### 概述
![alt text](image-48.png)

###### 配置主从
- 永久配置：
  - `redis.conf`文件中配置`slaveof <masterip> <masterport>`命令，指定主服务器的IP地址和端口。
  - 启动时，Redis会自动从主服务器同步数据。
- 临时配置：
  - `slaveof`命令在运行时执行，指定主服务器的IP地址和端口。
  - `replicaof`
- `INFO replication`命令查看主从关系。

###### 数据同步原理
- 全量同步：
![alt text](image-49.png)
> repl_baklog是专门用来主从复制的一个环形缓冲区

- 增量同步：
![alt text](image-50.png)

###### 优化
![alt text](image-51.png)


#### 哨兵机制Sentinel
###### 概述
![alt text](image-52.png)

###### 心跳机制监控
![alt text](image-53.png)

###### 选举新master
- 先排除断开时间过长节点
- 判断`slave-priority`值，越小优先级越高
- 判断复制偏移量`offset`，越大数据越新
- 最后根据`id`

###### 故障转移
![alt text](image-54.png)

###### RedisTemplate的哨兵模式
- 配置：==配置的是Sentinel的地址，不是Redis的地址，因为Redis集群主从关系一直在变化，要基于Sentinel集群发现Redis主从关系、节点地址。==
![alt text](image-55.png)
- 主从读写分离
![alt text](image-56.png)


#### 分片集群Cluster
###### 概述
![alt text](image-58.png)


###### 配置集群
- 在`redis.conf`中`enable-cluster`设置为`yes`开启集群模式
- 配置集群配置文件名称，不需要我们创建，redis自己维护
  - `cluster-config-file filepath/$port/nodes.conf`
- 启动集群：
![alt text](image-59.png)
- 集群命令：`redis-cli --cluster help`
- 查看集群状态：`redis-cli -p $port cluster nodes`
- 添加节点：`redis-cli --cluster add-node $ip:$port $master_ip:$master_port`
- 分配插槽：`redis-cli --cluster reshard $master_ip:$master_port`

###### 散列插槽Slot
![alt text](image-60.png)

> 使数据与Slot绑定，增加可靠性，即使节点宕机，只需转移Slot中的数据，其他节点依然可以提供服务。

###### 数据迁移
![alt text](image-61.png)


###### RedisTemplate的集群模式
与哨兵模式类似，配置Redis集群的地址，RedisTemplate会自动发现集群的主从关系。



### 多级缓存
###### 概述
![alt text](image-63.png)


###### JVM本地缓存
![alt text](image-64.png)


###### 冷启动和缓存预热
![alt text](image-65.png)


###### 缓存同步
![alt text](image-66.png)


### Redis最佳策略
###### 键值设计
- `业务:属性:id`
- 避免bigKey，避免过长的key，避免过多的key，避免过多的field


###### 批处理
- `MSET`等指令的使用
- ![alt text](image-67.png)

> pipeline不保证多条指令之间的原子性

- 集群下的批处理
![alt text](image-68.png)


###### 服务端优化
- 持久化配置
![alt text](image-69.png)
- 慢查询
- 命令及安全配置
![alt text](image-70.png)
- 内存配置
![alt text](image-71.png)
- 集群配置和集群、主从选择

## 原理
### 数据结构
#### 动态字符串SDS
![alt text](image-72.png)
> ![alt text](image-73.png)

#### IntSet
![alt text](image-74.png)


#### Dict
![alt text](image-75.png)

> ![alt text](image-77.png)

- rehash
![alt text](image-78.png)


#### ZipList
![alt text](image-79.png)