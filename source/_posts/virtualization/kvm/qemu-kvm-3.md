---
title: qemu/kvm 源码分析与应用 第三节
tags:
  - 虚拟化
  - kvm
  - qemu
  - libvirtd
categories:
  - 虚拟化
  - kvm
keywords: '虚拟化,kvm,qeme,c,libvirtd'
description: qemu/kvm 源码分析与应用 第二节 libvirtd 安装
abbrlink: 43562
date: 2023-09-25 01:38:04
---

> 本节主要介绍libvirtd源码安装

## libvirtd 源码安装

> 操作系统版本为ubuntu 22.04

### 安装依赖

```bash
sudo apt-get install git build-essential  libssl-dev  pkg-config libncurses-dev flex bison libelf-dev libpciaccess-dev  xsltproc libyajl-dev python3-docutils -y
```

### 下载源码

从 https://libvirt.org/sources/ 下载新版本的 Libvirt 源码。

```bash
curl -# -O https://download.libvirt.org/libvirt-9.6.0.tar.xz -o libvirt-9.6.0.tar.xz
```

<!--more -->

### 编译Libvirt 工具

```bash
tar -xf libvirt-9.6.0.tar.xz
   cd  libvirt-9.6.0
   meson build -Dsystem=true -Ddriver_qemu=enabled -Ddriver_libvirtd=enabled -Ddriver_remote=enabled  
   ninja -C build
   sudo ninja -C build install
```

### 检查是否安装成功：

```bash
virsh --version
```

## 编译过程中可能遇到的问题。

### libvirt-libvirt-common.html not find

sudo ninja -C build install 过程中出现ERROR: File 'docs/html/libvirt-libvirt-common.html' could not be found 错误，导致 无法正常完成安装，
查看 build/docs/html 并没有libvirt-xx-.html 文件，因为其存在 build/html 下.
解决方案也很简单 将 html 文件 拷贝到docs/html 下即可，也可以做一个链接

```bash
ln -s `pwd`/build/html/* `pwd`/build/docs/html/
```

### internal error: Network is already in use by interface ens3

安装完成后 执行

```bash
systemctl status libvirtd
```

发现libvirtd 进程并未正常启动，发现错误日志为

```bash
internal error: Network is already in use by interface ens3
```

出现该原因的情况是libvirtd 需要基于ens3网卡创建一个对虚拟机使用的虚拟网桥，该网桥的默认网络与ens3 网卡的网络重复导致的。
解决方案 修改默认的virtio 网络地址

```bash
sudo vim /etc/libvirt/qemu/networks/default.xml
```

```xml
<network>
  <name>default</name>
  <uuid>4a9f5265-47e0-4a1a-ad05-2a615690e14a</uuid>
  <forward mode='nat'/>
  <bridge name='virbr0' stp='on' delay='0'/>
  <mac address='52:54:00:fe:27:f0'/>
  <!-- 修改部分-->
  <ip address='192.168.100.1' netmask='255.255.255.0'> 
    <dhcp>
      <!-- 修改部分-->
      <range start='192.168.100.2' end='192.168.100.254'/>
    </dhcp>
  </ip>
</network>
```

重新执行

```bash
systemctl start libvirtd 
 systemctl status libvirtd
```

> 出现该原因 可能是博主使用环境是kvm的虚拟机导致的

### invalid argument: Failed to parse user 'libvirt-qemu'

安装之后通过 systmctl status libvirtd 发现其进程并未正常启动，报错原因为

```
8月 02 10:15:16 invalid argument: Failed to parse user 'libvirt-qemu'
8月 02 10:15:16 cheney-Standard-PC-i440FX-PIIX-1996 libvirtd[295371]: Initialization of QEMU state driver failed: invalid argument: Failed t>
8月 02 10:15:16 cheney-Standard-PC-i440FX-PIIX-1996 libvirtd[295371]: Driver state initialization failed
```

解决方案：
添加 一下用户组和用户即可

```bash
sudo groupadd libvirt-qemu
  sudo groupadd  libvirt
  sudo groupadd libvirtd
  sudo useradd libvirt-qemu -g libvirt-qemu -M -s /usr/sbin/nologin

  sudo systemctl start libvirtd
```


