---
title: qemu/kvm 源码分析与应用 第一节
tags:
  - 虚拟化
  - kvm
  - qemu
categories:
  - 虚拟化
  - kvm
keywords: '虚拟化,kvm,qeme,c'
description: '本节主要介绍kvm源码安装,以及qemu-kvm的安装'
abbrlink: 17906
date: 2023-09-25 01:29:21
---
> 本节主要介绍kvm源码安装,以及qemu-kvm的安装

## kvm 源码安装

> 操作系统版本为ubuntu 22.04

### 安装依赖

```bash
sudo apt-get install git build-essential  libssl-dev  pkg-config libncurses-dev flex bison libelf-dev
```


### 下载源码

源码的下载使用中国科技大学的镜像站点下载linux内核源码

```bash
git clone https://mirrors.tuna.tsinghua.edu.cn/git/linux-stable.git -b linux-6.4.y
```

### 编译内核kvm模块

```bash
cd linux-stable
    cp /boot/config-$(uname -r) .config
    make menuconfig
    make -j $(nproc)
    sudo make modules_install 
    sudo make install     #安装kernel
```

或者

```bash
   cd linux-stable
   cp /boot/config-$(uname -r) .config
   make menuconfig
   make vmlinux -j $(nproc)       #编译kernel   ##编译很慢请耐心等待##
   make bzImage            #编译bzImage
   make modules -j $(nproc)        #编译内核模块
   make modules_install       #安装module
   make install              #安装kernel
```

### 配置内核启动参数

```bash
sudo vim /etc/default/grub
```

<!-- ##  qemu-kvm 源码安装 -->

## 编译过程中可能遇到的问题。

### 'certs/x509_certificate_list

```bash
make[3]: *** No rule to make target 'debian/canonical-certs.pem',  needed by 'certs/x509_certificate_list'.  Stop.
```

解决方法：

将CONFIG_SYSTEM_REVOCATION_KEYS 和 CONFIG_SYSTEM_TRUSTED_KEYS 都设置为值设置为""，即空字符串.
重新执行

```bash
make -j $(nproc)
```




