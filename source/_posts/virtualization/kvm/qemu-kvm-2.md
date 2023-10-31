---
title: qemu/kvm 源码分析与应用 第二节 qemu 安装
tags:
  - 虚拟化
  - kvm
  - qemu
categories:
  - 虚拟化
  - kvm
keywords: '虚拟化,kvm,qeme,c'
description: 本节主要qemu-kvm的安装
abbrlink: 65310
date: 2023-09-25 01:35:20
---



> 本节主要qemu-kvm的安装

## qemu 源码安装

> 操作系统版本为ubuntu 20.04

### 安装依赖

```bash
sudo apt-get install build-essential zlib1g-dev pkg-config libglib2.0-dev binutils-dev libboost-all-dev autoconf libtool libssl-dev libpixman-1-dev   virtualenv libtool liblog4cpp5-dev libavcodec-dev libssl-dev libglu1-mesa-dev libasound2-dev libfreetype6-dev  libfontconfig1-dev libogg-dev libxrandr-dev libgcrypt20-dev libsdl1.2-dev libsasl2-dev  libnss3-dev libpixman-1-dev libxfixes-dev libjpeg8-dev libiscsi-dev  libusbredirparser-dev libusbredirhost-dev usbredirectlibspice-server-dev
```


### 下载源码

源码的下载使用中国科技大学的镜像站点下载qemu

```bash
git clone https://mirrors.tuna.tsinghua.edu.cn/git/qemu.git -b stable-8.0
git submodule update --recursive
```

或者

```bash
curl https://mirrors.tuna.tsinghua.edu.cn/git/qemu/qemu.sh | bash
```

### 编译qemu

```bash
cd qemu
    ./configure --enable-usb-redir  --enable-spice --enable-libiscsi --enable-spice-protocol --enable-strip
    sudo make -j $(nproc) 
    sudo make install
```

