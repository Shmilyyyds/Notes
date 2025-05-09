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