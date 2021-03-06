---
layout:     post
title:      "Docker 学习笔记1"
subtitle:   "翻译总结自官方文档"
date:       2018-07-25
author:     "luyx"
header-img: "img/post-bg-unix-linux.jpg"
tags:
    - cloud
    - docker
    - Linux
---



> 翻译总结 参考 官方文档https://docs.docker.com/install/linux/

1.	**Docker**是什么
一种轻量容器，提供了应用安全独立隔离的运行环境，方便快速的开发、部署、测试、迁移。
- 灵活（Flexible）: 支持部署多种应用
- 轻量（Lightweight）: 容器技术可以高效利用和共享os内核.
- 可交互（Interchangeable）: 快速部署更新
- 可移植（Portable）: 本地开发，云端部署，移植性强
- 可扩展（Scalable）: 快速复制，快速分发，可用于业务高可靠性
- 可堆叠（Stackable）: 快速堆叠业务架构
   - 与虚拟机的区别：
容器共享宿主机操作系统内核，使用独立进程，占用内存小，比虚拟机节省更多的系统资源
  

2.	版本说明
    - 社区版（CE）适用于个人开发及小型团队基于容器的应用实验
    - 企业版（EE）专为构建运行大型关键业务的企业开发设计
3.	安装方式
   - 直接安装rpm 或 deb 包
   - 前往https://download.docker.com/linux/ubuntu/dists/,   下载对应版本deb包 安装
   - 设定docker官方仓库路径后，包管理工具如yum下载最新docker

```
- 以Ubuntu18.04为例
- 卸载历史旧版本docker sudo apt-get remove docker docker-engine docker.io
- 存储格式支持 默认overlay2 设定aufs 需要手动配置
- 设定docker官方仓库
     

$ sudo apt-get update
$ sudo apt-get install apt-transport-https ca-certificates curl \software-properties-common


- 添加官方docker GPG

$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

- 核对官方秘钥
    
$ sudo apt-key fingerprint 0EBFCD8F
pub   4096R/0EBFCD88 2017-02-22
      Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid                  Docker Release (CE deb) <docker@docker.com>
sub   4096R/F273FCD8 2017-02-22

- 添加docker仓库
    
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
lsb_release -cs 为当前Ubuntu版本发行代号 18.04 为bionic

- 更新apt 源 并安装docker-ce


$ sudo apt-get update
$ sudo apt-get install docker-ce

```

安装完成后 运行docker run hello-world查看是否安装成功

4.	docker运行过程说明

```
lyx@ubuntu:~$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
9db2ca6ccae0: Pull complete 
Digest: sha256:4b8ff392a12ed9ea17784bd3c9a8b1fa3299cac44aca35a85c90c5e3c7afacdc
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.  
寻找当前本地镜像是否有匹配，若没有从docker-hub下载
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
(amd64) 
守护进程创建指定的镜像生成的新的容器并运行可执行的过程
 3. The Docker daemon created a new container from that image which runs the
executable that produces the output you are currently reading.
打印运行容器是设定的命令行输出流到终端显示 
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

```

5. 基本的步骤
   - 安装docker运行环境
   - 创建镜像
     - 从docker hub 等镜像仓库站拉取基础镜像
     - 编辑修改Dockerfile 后 build镜像（镜像二次构建）
   - 部署应用到容器
   - 分发应用容器形成集群（依赖镜像）
   - 增加后端数据存储增加服务
   - 部署应用到生产环境

6. 其他相关
>Kubernetes is a portable, extensible open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation. It has a large, rapidly growing ecosystem. Kubernetes services, support, and tools are widely available.

   - [kubernetes_zh](http://docs.kubernetes.org.cn/227.html)
   - [kubernetes_en](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)


