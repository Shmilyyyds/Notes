# 微服务[SpringCloud]
## Eureka注册中心
### 基本原理
![alt text](image.png)

### 搭建Eureka服务
###### 注册Server端
- 引入依赖
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```
- 配置文件
```yaml
server:
  port: 8761
# Eureka服务器自己也是个微服务，所以也要注册到Eureka服务器当中
spring:
  application:
    name: eureka-server
eureka:
  client:
    servive-url:
      defaultZone: http://localhost:8761/eureka/
```

- `@EnableEurekaServer`

###### 注册Client端
- 引入依赖
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```
- 配置文件
```yaml
spring:
  application:
    name: eureka-client
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

###### 服务发现
![alt text](image-1.png)


## Ribbon负载均衡
### 基本原理
![alt text](image-2.png)

> ![alt text](image-3.png)

### 负载均衡规则配置
![alt text](image-4.png)


### 加载策略
![alt text](image-5.png)

## Nacos
### 注册中心功能
#### 简单搭建
- 下载启动Nacos服务端
- 微服务项目导入客户端依赖
- 项目配置Nacos服务器地址

#### 服务分级存储模型
![alt text](image-7.png)
> ![alt text](image-6.png)

#### 负载均衡规则
###### NacusRule
- 优先选择同集群服务实例列表
- 确定列表后再采用随机负载均衡挑选实例

###### 权重控制
- Nacos控制台可以设置实例权重
- 权重值在0-1之间，1代表最高权重，0代表最低权重（完全不会被访问）

#### 环境隔离-namespace
![alt text](image-9.png)
> ![alt text](image-8.png)

#### Nacos注册服务流程
![alt text](image-10.png)

### 配置中心功能
#### 配置管理功能
###### 概述
![alt text](image-11.png)
> 在Nacos控制端中，可以对配置进行管理，包括配置的添加、删除、修改、查询等操作。

###### 配置拉取流程
![alt text](image-12.png)
> ![alt text](image-13.png)

#### 配置热更新
![alt text](image-14.png)


#### 多环境配置共享
![alt text](image-15.png)

> 优先级：
> 服务名-profile.yaml > 服务名.yaml > application-profile.yaml > application.yaml 

#### 集群搭建
![alt text](image-16.png)

> MySQL存储配置信息，用于Nacos集群同步配置信息。

## Feign
### 基本使用
![alt text](image-17.png)

### 自定义配置
![alt text](image-18.png)


### 性能优化
![alt text](image-19.png)

## Gateway网关
### 网关概念
![alt text](image-20.png)
> ![alt text](image-21.png)


### 简单搭建
- 引入SpringCloudGateway依赖和Nacos服务发现依赖
- 配置Gateway路由规则
![alt text](image-22.png)


### 路由断言工厂
![alt text](image-23.png)


### 路由过滤器
###### Spring提供过滤器工厂
![alt text](image-24.png)

> 可以在Gateway映射微服务配置中配置Fliters，也可在Gateway整个服务中配置default-filters。

###### 自定义过滤器[全局过滤器GlobalFilter]
![alt text](image-25.png)

###### 过滤器执行顺序
![alt text](image-26.png)


### 跨域问题
![alt text](image-28.png)

![alt text](image-27.png)


## Sentinel
### 雪崩问题
![alt text](image-29.png)


### 快速入门
![alt text](image-30.png)


### 限流规则
###### 流控模式
![alt text](image-31.png)

> ![alt text](image-32.png)
>
> ![alt text](image-34.png)

###### 流控效果
![alt text](image-33.png)


### 隔离和降级
#### Feign整合Sentinel
![alt text](image-35.png)

#### 线程隔离实现
![alt text](image-36.png)


#### 熔断降级实现
![alt text](image-37.png)


### 授权规则和自定义异常处理
#### 获取请求来源接口
- `RequestOriginParser`

> ![alt text](image-38.png)

#### 自定义异常处理
- `BlockExceptionHandler`

> ![alt text](image-39.png)


### Sentinel规则持久化
Sentinel的控制台规则管理有三种模式：
- 原始模式：Sentinel默认模式，规则在内存中，重启失效。
- pull模式：保存在本地文件或数据库，定期更新
- push模式：保存在Nacos中，监听更新。


## 分布式事务Seata
### 概念
在分布式系统下，一个业务跨越多个服务或数据源，要保证每个分支的事务最终状态一致，这样的事务叫做分布式事务。

### 理论基础
###### CAP定理
![alt text](image-40.png)


###### BASE理论
![alt text](image-41.png)


### Seata
###### 架构
![alt text](image-42.png)

###### 模式
- XA模式：
![alt text](image-43.png)
- AT模式（存在全局事务锁、before-image、after-image避免脏写）：
![alt text](image-44.png)
- TCC模式：
![alt text](image-45.png)
- Saga模式：反向操作补偿

###### 高可用
![alt text](image-46.png)