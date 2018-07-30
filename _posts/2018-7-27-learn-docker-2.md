---
layout:     post
title:      "Docker 学习笔记2"
subtitle:   "container and service"
date:       2018-07-27
author:     "luyx"
header-img: "img/post-bg-unix-linux.jpg"
tags:
    - cloud
    - docker
---





> https://docs.docker.com/get-started/part2/#prerequisites

# contianers容器

1. 基本结构
  - Stack
  - Service
  - Containers

2. [Dockerfile](#dockerfile)
3. [构建镜像\运行容器](#构建运行)
4. [分发镜像](#分发镜像)
5. [常用命令](#常用命令)

---

## Dockerfile

1.从Dockerfile定义容器 （自定义容器）
简单概括就是从镜像仓库拉一个可用的运行环境（runtime）**基础镜像**（如python java tomcat nginx .etc），然后设定运行环境，拷贝应用到容器内相应目录，启动应用的命令行如（tomcat/bin/startup.sh  python3 app.py），这些过程均设定在**Dockerfile**中。
> 拉取基础镜像如docker pull java：7  默认都是latest 可用的tags参考docker hub

2.基本步骤
  - 创建一个空的目录，用于存放Dockerfile 及生成镜像内容中包含的文件如应用war包,点py，点sh等
  - **创建DockerFIle**

```
#官方给的示例，格式书写参考 
#python 运行 flask 连接redis（redis并没有装）
#first step 指定基础镜像
FROM python：2.7-slim
# 设定工作目录 指的是容器内
WORKDIR /app
# copy 当前文件夹内容 到容器内 根据实际内容拷贝到相应目录
ADD . /app
# 安装依赖包(
RUN pip install --trusted-host pypi.python.org -r requirments.txt
# 开放容器对外端口80
EXPOSE 80
# 定义环境变量
ENV NAME World
# 当容器启动时运行的命令行，用引号和逗号分隔单个命令
CMD ["python"，"app.py"]

依赖文件
----
#requirements.txt -- 用于python pip安装指定的包
Flask
Redis(python 连接redis库，非redis本身)
----
#app.py
from flask import Flask
from redis import Redis,RedisError
import os
import socket

# Connect to Redis
redis = Redis(host="redis", db=0, socket_connect_timeout=2, socket_timeout=2)

app = Flask(__name__)

@app.route("/")
def hello():
    try:
        visits = redis.incr("counter")
    except RedisError:
        visits = "<i>cannot connect to Redis, counter disabled</i>"

    html = "<h3>Hello {name}!</h3>" \
           "<b>Hostname:</b> {hostname}<br/>" \
           "<b>Visits:</b> {visits}"
    return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname(), visits=visits)

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)

----

```

## 构建运行  
   - 构建镜像


```
# 在包含Dockerfile的目录内执行 -t tags 用于指定镜像别名
docker bulid -t friendlyhello 
docker image ls 或docker images 查看创建生成的镜像
```

   - 访问失败故障排除

```
1. 在Dockerfile中编辑设置代理 proxy
ENV http_proxy host:port
ENV https_proxy host:port

2. 设置DNS
vi /etc/docker/daemon.json
{
"dns":{"your_dns_address","8.8.8.8"
}
systemctl daemon reload
systemctl restart docker

#重试构建
```

    - 运行app
 
```
docker run -d -p 4000:80 friendlyhello
#命令说明 
#-p [host_port]:[container_port] 指定主机容器端口映射 
#friendlyhello 为自己构建镜像名，优先从本地仓库寻找，若找不到则会从默认的docker hub仓库查找并拉取镜像运行 
# -d 后台运行 run the app in the background in detached mode
```
    - 访问浏览器或通过curl http://localhost:4000 查看app运行状态十分正常
    - 停止运行 docker container stop 1fa4a （container ID 前几位即可）

 
## 分发镜像 

- 为了上载镜像用于分发，首先需要一个docker registry（公有：如Docker hub，私有：自己部署），用于存放镜像仓库的集合。dokcer默认使用公共的registry即docker hub。需要注册一个账号[docker hub](hub.docker.com)
- 为镜像打tag(用于指定版本号)并上传至docker hub仓库

```
docker login
docker tag image username：repository：tag
docker push username/repostiory:tag

```

## 常用命令
- 基本常用命令如下

```
docker build -t friendlyhello .  # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyhello  # Run "friendlyname" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyhello         # Same thing, but in detached mode
docker container ls                                # List all running containers
docker container ls -a             # List all containers, even those not running
docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                             # List all images on this machine
docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry
```