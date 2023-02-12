---
title: Conda配置下载源
date: 2023-01-12
categories: [配置, Conda]
tags: [conda, config]

author: <author_id>

math: true
mermaid: true
pin: true
---



## 1. 查看当前下载源

```
conda config --show channels
```

## 2. 添加下载源

```
conda config --add channels https://pypi.tuna.tsinghua.edu.cn/simple
```

## 3. 删除下载源

```
conda config --remove channels https://pypi.tuna.tsinghua.edu.cn/simple
```

## 4. 下载时显示下载源

```
conda config --set show_channel_urls yes
```

## 5. 常用镜像源

```
https://pypi.douban.com/simple/
http://mirrors.aliyun.com/pypi/simple/
https://pypi.tuna.tsinghua.edu.cn/simple/
https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
```

