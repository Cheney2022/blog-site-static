---
title: 针对ubuntu 设置终端代理、全局代理
tags:
  - 代理
  - 运维
categories:
  - 运维
  - 代理
keywords: '运维,ubuntu,代理'
description: 针对ubuntu 设置终端代理、全局代理
abbrlink: 19593
date: 2023-09-25 00:34:31
---


# 针对Ubuntu设置代理

## 终端代理

```bash
export http_proxy=http://用户名:密码@代理地址:代理端口
export https_proxy=http://用户名:密码@代理地址:代理端口
export no_proxy='127.0.0.1, localhost, *.cnn.com, 192.168.1.10, domain.com:8080'
```

> *.cnn.com 泛域名 但是测试好像没生效，

## apt 代理

编辑 /etc/apt/apt.conf.d/porxy.conf 文件添加以下

```bash
Acquire::http::Proxy "http://proxy.xxx.com:8080";
Acquire::https::Proxy "http://proxy.xxx.com:8080";
```

