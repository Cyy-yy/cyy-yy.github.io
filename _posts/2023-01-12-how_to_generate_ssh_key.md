---
title: 如何通过SSH连接GitHub？
date: 2023-01-12
categories: [配置, Git操作]
tags: [github, ssh]

author: <author_id>

math: true
mermaid: true
pin: true
---



## 1. 本地创建SSH密钥

```bash
cd ~/.ssh  # 切换到.ssh目录
```

```bash
ssh-keygen -t rsa -b 4096 -C "cyy-yy@github.com"  # 生成密钥，选项全部回车确认
```

## 2. 在GitHub中添加公钥

复制`~/.ssh/id_rsa.pub`中的公钥到GitHub上

## 3. 测试连接

```bash
ssh -T git@github.com
```

出现GitHub用户名提示即成功
