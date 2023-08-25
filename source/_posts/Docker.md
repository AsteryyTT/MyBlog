---
title: Docker
date: 2023-03-19 11:18:57
tags: 中间件
categories: 中间件
cover: /img/images/docker.png
---

# Docker

## Docker容器技术

Docker是一门平台级别的技术



### Docker安装和部署

在服务器上安装各种各样的环境需要花费很多时间，有了Docker以后我只需要将这些环境打包成一个镜像，就可以实现一键部署了

卸载旧版本

> ```
>  sudo yum remove docker \
>                   docker-client \
>                   docker-client-latest \
>                   docker-common \
>                   docker-latest \
>                   docker-latest-logrotate \
>                   docker-logrotate \
>                   docker-engine
> ```

> ```
> sudo yum install -y yum-utils
> 
> $ sudo yum-config-manager \
>     --add-repo \
>     https://download.docker.com/linux/centos/docker-ce.repo
> ```

> ```
> sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
> ```

> ```
> sudo systemctl start docker
> ```

> ```
>  sudo docker run hello-world
> ```



### 容器与镜像



### 容器网络管理



### 容器存储管理



### 容器资源管理





docker命令

systemctl start docker

systemctl restart docker 

systemctl status docker

docker images

systemctl enable docker

systemctl stop docker