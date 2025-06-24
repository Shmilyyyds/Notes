# 操作系统

## 操作系统的概念
![alt text](image.png)
### 定义
**操作系统**是指**控制和管理整个计算机系统的硬件和软件资源**，并合理地组织调度计算机工作和资源的分配；**以提供给用户方便、有效的接口和使用环境为目的**；是计算机系统的最基本的**系统软件**。
- 操作系统是系统资源的管理者
- 向上层提供方便易用的服务

$$
    \text{计算机硬件} \rightarrow \text{操作系统} \rightarrow \text{应用软件} \rightarrow \text{用户}
$$

### 操作系统的功能
- 资源的管理
  - 处理机管理：分配和调度系统资源，控制程序的执行
  - 存储器管理：管理系统存储器，包括内存、磁盘、磁带机等
  - 文件管理：管理文件，包括目录、文件、设备等
  - 设备管理：管理计算机外部设备，包括输入输出设备、网络设备等
- 向上层提供服务
  - GUI
  - 命令接口
  - 程序接口
- 对硬件机器的扩展：有机结合CPU等硬件

### 操作系统的特征
#### 并发
**指多个事件在同一时间间隔内发生。宏观上时间是同时发生的，微观上是交替运行的。**
> 并行微观上也是同时发生的。

#### 共享
**指系统中的资源可供多个并发进程共同使用。共享资源包括内存、磁盘、网络等。**
- 互斥共享：一个资源同时只允许一个进程使用
- 同时访问：多个进程可以同时访问一个资源

#### 虚拟
**指把一个物体的实体变为若干个逻辑上的对应物。**
- 空分复用技术，如虚拟存储器技术
- 时分复用技术，如虚拟CPU

#### 异步
**指由于资源有限，进程的执行不是一贯到底，而是走走停停，进程以不可预知的速度向前推进，不受其他进程的影响。**

> 并发和共享互为存在条件
> 没有并发和共享，就谈不上虚拟和异步，因此==并发和共享是操作系统的两个最基本的特征==


### 操作系统的发展与分类
![alt text](image-1.png)


### 操作系统的运行机制
#### CPU内核态与用户态
- 内核态：CPU处于此态时，可以执行特权指令。
- 用户态：CPU处于此态时，只能执行非特权指令。
> CPU依靠PSW寄存器的标志位来判断当前运行的状态，当PSW的T位为1时，CPU处于内核态；当T位为0时，CPU处于用户态。


![alt text](image-2.png)

#### 中断与异常
![alt text](image-3.png)

#### 系统调用
![alt text](image-9.png)
![alt text](image-4.png)
> 陷入指令是非特权指令，在CPU用户态执行。


### 操作系统引导（开机过程）
![alt text](image-5.png)

### 虚拟机
![alt text](image-6.png)



## 处理机管理
### 进程
#### 概念
**进程**是*动态*的，是程序的一次执行过程；==是进程实体的运行过程，是系统进行资源分配和调度的基本单位。==
> 程序：程序是静态的，是指令的集合。


#### 组成
> PCB是给操作系统用的
> 程序段、数据段是给进程自己用的
##### PCB
**操作系统需要对各个并发运行的进程进行管理，==但凡管理时所需要的信息都会被放在一个数据结构 $PCB(Process \space Control \space Block)$== 中**

- **PCB**
  - 进程描述信息
    - ==进程标识符PID==
    - 用户标识符UID
  - 进程控制和管理信息
  - 资源分配清单
  - 处理机相关信息

###### 程序段、数据段
- 程序段：进程执行的指令
- 数据段：进程执行时产生的各种数据

<hr>

> ![alt text](image-7.png)


#### 特征
![alt text](image-8.png)

#### 状态与转换
![alt text](image-10.png)

##### 进程的组织
- 链式方式
- 索引方式

#### 进程控制
##### 原语的原子性
![alt text](image-11.png)

##### 创建原语
- 申请空白PCB
- 为新进程分配资源
- 初始化PCB
- 将PCB插入就绪队列

##### 撤销原语
- 从PCB集合中找到指定PCB
- 若进程正在运行，立即剥夺CPU，将CPU分配给其他进程
- 终止其所有子进程
- 回收其资源到父进程或操作系统
- 删除PCB

##### 阻塞和唤醒原语
![alt text](image-12.png)


##### 切换原语
![alt text](image-13.png)


#### 进程通信 PIC
##### 共享存储
![alt text](image-14.png)
> 分为数据结构、存储区两种。

##### 消息传递
- 直接通信
![alt text](image-15.png)
- 间接通信
![alt text](image-16.png)


##### 管道通信
![alt text](image-17.png)


### 线程
#### 与进程的区别
![alt text](image-18.png)

#### 特点
![alt text](image-19.png)

#### 实现方式与模型
![alt text](image-20.png)

### 调度
#### 分类、层次
- 高级调度
![alt text](image-21.png)
- 低级调度
![alt text](image-22.png)
- 中级调度
![alt text](image-23.png)

> 七状态状态：
> ![alt text](image-24.png)


> 三层调度的对比
> ![alt text](image-25.png)


#### 时机与切换
![alt text](image-26.png)


#### 调度算法
##### 评估标准
![alt text](image-27.png)

##### 先来先服务 FCFS
![alt text](image-28.png)


##### 短作业优先 SJF
![alt text](image-29.png)

##### 高响应比优先 HRRN
![alt text](image-30.png)


##### 时间片轮转 RR
![alt text](image-31.png)

##### 优先级调度
![alt text](image-32.png)
> ![alt text](image-33.png)


##### 多级反馈队列调度
![alt text](image-34.png)

![alt text](image-35.png)


##### 多级队列调度
![alt text](image-36.png)


### 同步与互斥
#### 概念
![alt text](image-37.png)

#### 进程互斥实现方法
###### 软件方法
- 单标志法
- 双标志先检查法
- 双标志后检查法
- Peterson算法
  ![alt text](image-38.png)



###### 硬件方法
1. 中断
![alt text](image-39.png)

2. TestAndSet指令
![alt text](image-40.png)

3. Swap指令
![alt text](image-41.png)


#### 信号量机制
##### 概念
![alt text](image-42.png)

##### 信号量解决互斥、同步问题
![alt text](image-43.png)


#### 死锁
###### 概述
![alt text](image-44.png)

###### 预防死锁
![alt text](image-45.png)


###### 避免死锁
银行家算法：
![alt text](image-46.png)


###### 检测和解除死锁
- 检测死锁
![alt text](image-47.png)

- 解除死锁
  - 资源剥夺法
  - 撤销进程法
  - 进程回退法


## 内存管理
### 基本知识
#### 内存管理的概念
![alt text](image-48.png)

#### 内存映射
![alt text](image-49.png)

#### 覆盖和交换
###### 覆盖技术
![alt text](image-51.png)

###### 交换技术
![alt text](image-52.png)

### 内存分配
#### 连续分配
###### 三种分配
![alt text](image-50.png)

###### 动态分区分配算法
- 首次适应算法：按地址递增排序
- 最佳适应算法：按容量递增排序
- 最大适应算法：按容量递减排序
- 邻近适应算法：按地址递增排序，但每次都从上次查询结束位置开始查找

#### 非连续分配
###### 基本分页存储管理
![alt text](image-53.png)
![alt text](image-54.png)
![alt text](image-55.png)
![alt text](image-56.png)

###### 基本分段存储管理
![alt text](image-57.png)
![alt text](image-58.png)
![alt text](image-59.png)


###### 段页式管理
![alt text](image-60.png)


### 虚拟内存
![alt text](image-61.png)
![alt text](image-62.png)
###### 页面置换算法
- 最佳置换算法：每次淘汰最少使用的页面
- 先进先出置换算法：每次淘汰最先进入的页面（硬件要求大）
- 最近最久未使用置换算法：每次淘汰最久没有被访问的页面
- 时钟置换算法：每次淘汰最久没有被访问的页面，但需要维护一个访问位图
- 优化时钟置换算法：
![alt text](image-63.png)


###### 页面分配策略
![alt text](image-64.png)

> 抖动：原因是分配给进程的物理块不够。


## 文件管理
### 逻辑结构
###### 无结构文件
文件内部的数据就是一系列二进制流或字符流组成。

###### 有结构文件
![alt text](image-65.png)


### 文件目录
![alt text](image-66.png)


### 物理结构
- 连续分配
- 链式分配（隐式链接）（每个盘块包含一个指针）
- 链式分配（显示连接）（存在FAT-文件分配表，存放每个盘块的下一个盘块号）
![alt text](image-67.png)
- 索引分配
![alt text](image-68.png)

### 存储空间管理
![alt text](image-69.png)
![alt text](image-70.png)
![alt text](image-71.png)
![alt text](image-72.png)


### 文件操作
![alt text](image-73.png)

### 文件共享
- 硬链接
![alt text](image-74.png)

- 软连接（快捷方式）


## IO
### IO控制器
![alt text](image-75.png)

###### 控制方式
- 程序控制方式
![alt text](image-78.png)
- 中断驱动方式
- DMA方式
![alt text](image-76.png)
- 通道方式
![alt text](image-77.png)

### 机器磁盘
#### 磁盘结构
![alt text](image-79.png)
![alt text](image-80.png)


> 磁头默认在最外圈。

#### 磁盘调度算法
- 先来先服务调度算法（FCFS）
- 最短寻道时间优先算法（SSTF）
- 扫描算法（SCAN）（电梯算法，往右要到底）
- LOOK算法（电梯算法，无需到底）
- 循环扫描算法（C-SCAN，左右到底）
- C-LOOK算法（C-SCAN，无需到底）

> ![alt text](image-81.png)

#### 引导块
![alt text](image-82.png)

