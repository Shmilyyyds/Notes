# Spring Boot
## 起步依赖
![alt text](image.png)

## 配置文件
![alt text](image-1.png)

## YAML
### 基本概述
![alt text](image-2.png)
> 数组类似MARKDOWN中的列表
>
> ```yaml
> # 数组
> fruits:
>   - apple
>   - banana
>   - orange
> ```

> ==优点：容易阅读、容易与脚本语言交互、重数据轻格式==

### 读取YAML
- ![alt text](image-3.png)
- ![alt text](image-4.png)
- ![alt text](image-5.png)

## 多环境
### 配置
- YAML
    ![alt text](image-6.png)

- Properties
    ![alt text](image-7.png)

### 运行
![alt text](image-8.png)

### Maven和SpringBoot的多环境兼容
- **Maven的配置作为主导**
- ![alt text](image-9.png)
- ![alt text](image-10.png)
- ![alt text](image-11.png)

## 配置文件分类
![alt text](image-12.png)
> `file`指打包后`target`目录下
> `classpath`指项目结构`src/main/resources`目录下

## 整合第三方工具
### JUnit
![alt text](image-13.png)

### MyBatis
![alt text](image-14.png)
> 使用`@Mapper`注释，标注要MyBatis代理的Mapper接口



