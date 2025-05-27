# RabbitMQ
## MQ
### 同步调用
- 优点：
  - 时效性强，等待到结果后才返回
- 缺点：
  - 扩展性差
  - 性能下降
  - 级联失效问题

### 异步调用
![alt text](image.png)
- 优点：
  - 扩展性强
  - 性能高
- 缺点：
  - 依赖Broker稳定性

### 技术选型
![alt text](image-1.png)

## RabbitMQ
### 整体架构
![alt text](image-2.png)

> 还存在Channel概念，用于操作MQ的工具。


### 数据隔离
Virtual Hosts：
- 隔离不同业务的数据
- 不同业务可以共用同一个RabbitMQ服务器


### Java-RabbitMQ
过于复杂，不推荐使用，可见[官网](https://www.rabbitmq.com/tutorials/tutorial-one-java)

### Spring AMQP
- AMQP：
  - Queue-Exchange-Binding
![alt text](image-3.png)

###### 收发消息
![alt text](image-4.png)


###### WorkQueue模型
- 多个消费者绑定到一个队列，加快处理速度
- 通过设置prefetch来控制消费者预获取的消息数量，实现“能者多劳”。

###### Fanout交换机（广播）
![alt text](image-5.png)

> 绑定的所有队列都接收到消息。

###### Direct交换机（定向）
![alt text](image-6.png)


###### Topic交换机（主题）
![alt text](image-8.png)


###### 声明队列与交换机
![alt text](image-9.png)
- 基于代码
```java
@Configuration
public class RabbitConfig {
    @Bean
    public FanoutExchange fanoutExchange() {
        // ExchangeBuilder.fanoutExchange("").build();
        return new FanoutExchange("my.exchange");
    }

    @Bean
    public Queue myQueue() {
        // 持久化：MQ重启后队列不会丢失
        // QueueBuilder.durable("my.queue").build();
        return new Queue("my.queue");
    }

    @Bean
    public Binding bindingExchangeQueue(FanoutExchange fanoutExchange, Queue myQueue) {
        return BindingBuilder.bind(myQueue).to(fanoutExchange);
    }
}
```

- 基于注解
![alt text](image-10.png)



###### 消息转换器
- 默认转换器是JDK自带的序列化工具，不是很好。
![alt text](image-11.png)



### 可靠性
#### 生产者可靠性
###### 生产者重连
![alt text](image-12.png)


###### 生产者确认
![alt text](image-13.png)

> ![alt text](image-14.png)
> ![alt text](image-15.png)
> ![alt text](image-16.png)
>

#### MQ可靠性
![alt text](image-17.png)
###### 数据持久化
- 交换机持久化
- 队列持久化
- 消息持久化
  - 防止Page out阻塞

###### LazyQueue
- 与消息持久化区别：
  - 消息持久化会先尝试将消息写入内存，然后写入磁盘。
  - LazyQueue会将消息直接写入磁盘且做了IO优化，不写入内存。
![alt text](image-19.png)
> ![alt text](image-18.png)


#### 消费者可靠性
###### 消费者确认机制
![alt text](image-20.png)

> ![alt text](image-22.png)

###### 消费者重试机制
![alt text](image-23.png)


> ![alt text](image-24.png)

  ```java
  @Configuration
  @ConfitionalOnProperty(prefix = "spring.rabbitmq.listener.simple.retry", name = "enabled", havingValue = "true")
  public class RabbitConfig {
      @Bean
      public MessageRecoverer messageRecoverer(RabbitTemplate rabbitTemplate) {
          // 消息恢复器
          return new RepublishMessageRecoverer(rabbitTemplate, "error.exchange", "error");
      }
  }
  ```



###### 业务幂等性
![alt text](image-25.png)


- 唯一ID法：
  - ![alt text](image-26.png)
- 业务判断法


#### 兜底方案
消费者定期查询DB状态，若发现有异常自行处理。

### 延迟消息
#### 死信交换机
![alt text](image-28.png)


#### 延迟消息插件
==需要先安装rabbitmq-delayed-message-exchange插件==
设置延迟消息交换机：
![alt text](image-29.png)

> 发送延迟消息：
> ![alt text](image-30.png)