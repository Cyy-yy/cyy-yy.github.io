---
title: docker常用命令
date: 2023-01-12
categories: [Docker]
tags: [docker, command]

author: <author_id>

math: true
mermaid: true
pin: true
---



## 1. 搜索镜像

```bash
docker search <img_name>
```

## 2. 拉取镜像

```bash
docker pull <img_name>:<version>
```

## 3. 查看镜像

```bash
docker images
```

## 4. 删除镜像

```bash
docker rmi <img_name>
```

## 5. 创建容器

```bash
# 后台运行
docker run -id --name=<container_name> <img_name>:<version> <init command>

# 启动交互式控制台
docker run -it --name=<container_name> <img_name>:<version> /bin/bash
```

## 6. 查看容器

```bash
docker ps
docker ps -a
```

## 7. 进入容器

```bash
docker exec -it <container_name> /bin/bash
```

## 8. 启动/停止/退出容器

```bash
docker start <container_name>
docker stop <container_name>
exit
```

