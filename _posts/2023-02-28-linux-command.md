---
title: Linux常用命令
date: 2023-02-28
categories: [Linux]
tags: [linux, command]

author: <author_id>

math: true
mermaid: true
pin: true
---



## 1. 查看系统版本

- **查看内核**

    ```bash
    cat /proc/version
    ```

    ```bash
    uname -a
    ```
    
- **查看发行版**

    ```bash
    lsb_release -a
    ```

    ```bash
    cat /etc/issue
    ```

    ```bash
    cat /etc/redhat-release  # 查看Redhat系列linux版本
    ```


## 2. 判断环境是否为docker容器

```bash
# 输出一长串即为docker容器
cat /proc/1/cgroup
```

