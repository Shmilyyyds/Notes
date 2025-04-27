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