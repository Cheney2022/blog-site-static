---
title: linux 设置clang 为默认编译器
tags:
  - clang
  - c/c++
categories:
  - 开发
  - c/c++
keywords: 'clang,c++'
description: "linux 设置clang 为默认编译器"
abbrlink: 3301
date: 2023-09-25 01:03:07
---


# 安装clang
clang 安装方式 官网提供一下两种安装方式，及默认安装和自动脚本安装。两种安装方式本质上并没有什么不同。

{% tabs 安装,-1 %}
<!-- tab 默认仓库安装 -->
```bash
   apt-get install clang-format clang-tidy clang-tools clang clang libc++-dev libc++1 libc++abi-dev libc++abi1 libclang-dev libclang1 liblldb-dev libllvm-ocaml-dev libomp-dev libomp5 lld lldb llvm-dev llvm-runtime llvm python3-clang 
```
> 安装的版本版本为仓库默认版本，需要 添加以下源 来选择不同版本
```bash
deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic main
deb-src http://apt.llvm.org/bionic/ llvm-toolchain-bionic main
# Needs 'sudo add-apt-repository ppa:ubuntu-toolchain-r/test' for libstdc++ with C++20 support
# 16
deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-16 main
deb-src http://apt.llvm.org/bionic/ llvm-toolchain-bionic-16 main
# 17
deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-17 main
deb-src http://apt.llvm.org/bionic/ llvm-toolchain-bionic-17 main
deb http://apt.llvm.org/focal/ llvm-toolchain-focal main
deb-src http://apt.llvm.org/focal/ llvm-toolchain-focal main
# 16
deb http://apt.llvm.org/focal/ llvm-toolchain-focal-16 main
deb-src http://apt.llvm.org/focal/ llvm-toolchain-focal-16 main
# 17
deb http://apt.llvm.org/focal/ llvm-toolchain-focal-17 main
deb-src http://apt.llvm.org/focal/ llvm-toolchain-focal-17 main
deb http://apt.llvm.org/jammy/ llvm-toolchain-jammy main
deb-src http://apt.llvm.org/jammy/ llvm-toolchain-jammy main
# 16
deb http://apt.llvm.org/jammy/ llvm-toolchain-jammy-16 main
deb-src http://apt.llvm.org/jammy/ llvm-toolchain-jammy-16 main
# 17
deb http://apt.llvm.org/jammy/ llvm-toolchain-jammy-17 main
deb-src http://apt.llvm.org/jammy/ llvm-toolchain-jammy-17 main
deb http://apt.llvm.org/lunar/ llvm-toolchain-lunar main
deb-src http://apt.llvm.org/lunar/ llvm-toolchain-lunar main
# 16
deb http://apt.llvm.org/lunar/ llvm-toolchain-lunar-16 main
deb-src http://apt.llvm.org/lunar/ llvm-toolchain-lunar-16 main
# 17
deb http://apt.llvm.org/lunar/ llvm-toolchain-lunar-17 main
deb-src http://apt.llvm.org/lunar/ llvm-toolchain-lunar-17 main
```
<!-- endtab -->

<!-- tab shell 自动化方式安装 -->
```bash
bash -c "$(wget -O - https://apt.llvm.org/llvm.sh)"
``` 
> 安装的版本版本为最新的稳定版， 若想指定安装版本可以使用以下方式安装
```bash
bash -c "$(wget -O - https://apt.llvm.org/llvm.sh)" 16 all
```
<!-- endtab -->
{% endtabs %}


# 设置clang 为默认的编译器

```bash
    sudo update-alternatives --install /usr/bin/cc cc /usr/bin/clang 100
    sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/clang++ 100
```


