# Docker
## 镜像和容器
![alt text](image.png)

> ![alt text](image-11.png)

## 命令
### 基本解读
![alt text](image-1.png)

### 常用命令
![alt text](image-2.png)

## 数据卷
### 概述
![alt text](image-3.png)

### 常用命令
- 挂载数据卷需要在容器创建时完成，可以用指令`docker run -v volume_name:container_path`中指定。
> 可以不走数据卷，走本地目录挂载`docker run -v /local_path:/container_path`
![alt text](image-4.png)


## 自定义Image
### 镜像结构
![alt text](image-5.png)

### Dockerfile
![alt text](image-6.png)


## 网络
![alt text](image-7.png)
![alt text](image-8.png)

## DockerCompose
![alt text](image-9.png)
![alt text](image-10.png)