# JVM
## 基本架构
![alt text](image.png)
![alt text](image-4.png)

## 内存结构
### 程序计数器
#### 作用
![alt text](image-1.png)

#### 特点
- 线程私有，随着线程创建而创建，销毁而销毁
- 唯一一个不会存在内存溢出的区域

### 虚拟机栈
#### 基本概念
![alt text](image-2.png)

#### 特点
1. 每个线程运行时所需要的内存，称为虚拟机栈
2. 一个栈帧（Frame）对应一个方法调用，包含局部变量表、操作数栈、动态链接、方法出口信息
3. 每个线程只能有一个活动栈帧
4. 垃圾回收不涉及栈内存

#### 内存溢出
- 栈帧过多
- 栈帧过大

### 本地方法栈
给本地方法（Native Method）提供的内存空间

### 堆
- `new`创建的对象和数组都保存在堆内存中
- 它是线程共享的，堆中的对象都需要考虑线程安全问题
- 有垃圾回收机制

### 方法区
#### 组成
![alt text](image-3.png)

#### 常量池和运行时常量池
- 常量池：可以理解为就是一张表，虚拟机指令根据这种常量表找到要执行的类名、方法名、参数类型、字面量等信息。
- 运行时常量池：常量池是*class文件中的，当该类被加载，它的常量池信息就会放入运行时常量池，并把里面的符号地址变为真实地址。


#### StringTable串池
```java
public class Main {
    public static void main(String[] args) {
        // 下边两种都会存入串池
        String s1 = "hello";
        String s2 = new String("world");
        // new StringBuilder().append(s1).append(s2).toString();
        // toString() -> new String("helloworld"); 
        String s3 = s1 + s2; 
        String s4 = "helloworld";
        // s3 != s4

        // "test" "str"会存入串池，但"teststr"不会
        String s5 = new String("test") + new String("str");
    }
}
```
对于s1和s2：
- 还未加载`Main.class`，`"hello"`等字符串还是常量池中的信息。
- 当该类被加载，`"hello" "world"`就会被放入运行时常量池。
- 随后，StringTable（无法扩容hashtable结构，初始为空）会添加`"hello"`等字符串。


对于s3和s4：
- 详见代码注释，s3最终是堆中对象的引用，堆中对象`"helloworld"`不会放入串池（可以用`intern`方法放入）。
- 对于s4，语句等价`String s4 = "hello" + "world";`，因为都是常量，编译器可以优化。

> JDK1.7及之前的版本，StringTable在堆中，其实也是存放了一个个String对象。

### 直接内存
#### 基本特点
- 属于操作系统，是系统为Java程序提供的内存，使Java程序可以直接访问物理内存，可以提高性能
- 常见于NIO操作，用于数据缓冲区
- 分配回收成本较高，但读写性能高
- 不受JVM内存回收管理


#### 分配与回收
- 底层：使用`Unsafe`对象完成直接内存的分配回收，分配需要`allocateMemory()`方法，回收需要`freeMemory()`方法（类似C中的`malloc()`和`free()`）
- 与JVM-gc的联动：`ByteBuffer`的实现类内部，使用了`Cleaner`虚引用来检测`ByteBuffer`对象，一旦`ByteBuffer`对象被回收，那么就会由`ReferenceHandler`线程通过`Cleaner`的`clean()`方法调用`freeMemory()`方法释放直接内存

## 垃圾回收
### 判断垃圾
- 引用计数法：每个对象有一个引用计数器，当计数器为0时，说明没有任何对象引用它，可以回收。缺点是**循环引用问题**。
- 可达性分析算法：GC Roots作为起点，从这些节点开始向下搜索，搜索所走过的路径称为引用链，当一个对象到GC Roots没有任何引用链相连时，则证明此对象是不可达的，可以回收。