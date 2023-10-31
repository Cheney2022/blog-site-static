---
title: linux 下PaddleOCR c++ 环境的搭建以及编译
tags:
  - PaddleOCR
  - AI
  - 图片识别
  - c/c++
categories:
  - 开发
  - c/c++
keywords: 'AI,PaddleOCR,c++,图片识别'
description: 'PaddleOCR旨在打造一套丰富、领先、且实用的OCR工具库，助力开发者训练出更好的模型，并应用落地。本章节介绍PaddleOCR
  模型的C++部署方法。C++在性能计算上优于Python，因此，在大多数CPU、GPU部署场景，多采用C++的部署方式，本节将介绍如何在Linux
  (CPU)环境下配置C++环境并完成PaddleOCR模型部署。'

abbrlink: 55833
date: 2023-09-25 01:00:36
---

PaddleOCR旨在打造一套丰富、领先、且实用的OCR工具库，助力开发者训练出更好的模型，并应用落地。本章节介绍PaddleOCR 模型的C++部署方法。C++在性能计算上优于Python，因此，在大多数CPU、GPU部署场景，多采用C++的部署方式，本节将介绍如何在Linux (CPU)环境下配置C++环境并完成PaddleOCR模型部署。

主要过程包括:

- linux 下PaddleOCR c++ 环境的搭建以及编译
- linux下 识别文字的项目

# 安装编译环境

## 安装依赖编译环境

```sh
apt-get install net-tools tar wget make build-essential git python3-dev;
wget https://github.com/Kitware/CMake/releases/download/v3.22.5/cmake-3.22.5-linux-x86_64.sh;
sh cmake-3.22.5-linux-x86_64.sh --prefix=/usr/local --exclude-subdir

```

安装cmake 之前需要先卸载低版本的cmake，若cmake 版本不是太低的话可以跳过安装

## 编译opencv

官方教程使用的是3.6版本, 这里我使用最新版的4.6.0版本

```bash
apt-get install  libeigen3-dev libjpeg-dev libpng-dev libtiff-dev  libtiff5-dev
  #https://github.com/opencv/opencv/archive/refs/tags/4.6.0.tar.gz
  # 编译
  cmake .. \
  -DCMAKE_INSTALL_PREFIX=~/paddle/dep \
  -DCMAKE_BUILD_TYPE=Release \
  -DBUILD_SHARED_LIBS=OFF \
  -DWITH_IPP=OFF \
  -DBUILD_IPP_IW=OFF \
  -DWITH_LAPACK=OFF \
  -DBUILD_TESTS=OFF
  -DCMAKE_INSTALL_LIBDIR=lib64 \
  -DWITH_ZLIB=ON \
  -DBUILD_ZLIB=ON \
  -DWITH_JPEG=ON \
  -DBUILD_JPEG=ON \
  -DWITH_PNG=ON \
  -DBUILD_PNG=ON \
  -DWITH_TIFF=ON \
  -DBUILD_TIFF=ON
cd build && make -j 16 && make install
```

需要注意 -DCMAKE_INSTALL_PREFIX=~/paddle/dep 参数是指定安装目录， 需要修改成自己定义的目录
编译opencv 是用来操作的图像的 应该也可以使用其他的包管理安装如vcpkg 或conan

## 下载或者编译Paddle预测库

```bash
pip3 install numpy pyyaml protobuf -i http://mirrors.aliyun.com/pypi/simple --trusted-host mirrors.aliyun.com
  apt-get install  patchelf
  git clone https://github.com/paddlepaddle/Paddle
  cd Paddle
  # 建议使用git checkout切换到Paddle稳定的版本，如：
  git checkout v2.3.0
  cd Paddle
  mkdir build
  cd build
  cmake  .. \
    -DFLUID_INFERENCE_INSTALL_DIR=~/paddle/dep \
    -DWITH_CONTRIB=OFF \
    -DWITH_MKL=ON \
    -DWITH_MKLDNN=ON  \
    -DWITH_TESTING=OFF \
    -DWITH_GPU=OFF \
    -DWITH_NCCL=OFF \
    -DCMAKE_BUILD_TYPE=Release \
    -DWITH_INFERENCE_API_TEST=OFF \
    -DON_INFER=ON \
    -DWITH_PYTHON=ON
  make -j16 &&  make inference_lib_dist
```

需要注意一下下参数
-DFLUID_INFERENCE_INSTALL_DIR=~/paddle/dep  指安装的目录
-DWITH_GPU=OFF   开启GPU
-DWITH_NCCL=OFF  开启GPU 则需要安装nccl,

> 注: 官网也提供了各种编译完成预测库，[Paddle预测库官网](https://paddleinference.paddlepaddle.org.cn/user_guides/download_lib.html)上提供了不同cuda版本的Linux预测库，可以在官网查看并选择合适的预测库版本

# demo测试

## 准备模型

直接下载PaddleOCR提供的推理模型，或者参考模型预测章节，将训练好的模型导出为推理模型。模型导出之后，假设放在inference目录下，则目录结构如下。

## 编译PaddleOCR C++预测demo

# 错误解决

在官方推荐的编译器中gcc-5.4和gcc-8.2，博主在只是用的ubuntu22.04版本，默认的的gcc 是11.2版本在编译期间遇到不少错误。

## comparison object must be invocable as const

改错出现的位置大致是third_party/protobuf/src/extern_protobuf/src/google/protobuf/compiler/java/java_file.cc 文件中，是由gcc17标准的引起，只需要重载函数添加const 即可，如下

```c++
struct FieldDescriptorCompare {
  bool operator ()(const FieldDescriptor* f1, const FieldDescriptor* f2)  {
    if(f1 == NULL) {
      return false;
    }
    if(f2 == NULL) {
      return true;
    }
    return f1->full_name() < f2->full_name();
  }
};
```

修改成以下代码

```c++
struct FieldDescriptorCompare {
  bool operator ()(const FieldDescriptor* f1, const FieldDescriptor* f2) const {
    if(f1 == NULL) {
      return false;
    }
    if(f2 == NULL) {
      return true;
    }
    return f1->full_name() < f2->full_name();
  }
};
```

## size_t 未识别错误

编译 paddle/fluid/memory/detail/memory_block.h时 则出现size_t 未识别错误。不知道是linux平台问题还是g++版本问题。解决方式也很简单在其头部添加c的头文件即可:如何西

```c++
#include<stdio.h>
```

## copy elision 错误

编译器会出现以错误代码

```bash
error: moving a local object in a return statement prevents copy elision [-Werror=pessimizing-move]
  615 |   return std::move(thr_events);
```

大致return语句返回的对象是一个纯右值，且该对象类型与函数返回值类型相同时 导致阻碍编译器优化造成copy elision。通过增加编译参数将其转化为警告开关

```bash
find SRCROOT -type f -name CMakeLists.txt -exec sed -i -e '$aset(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-error=pessimizing-move")' {} \;
```

## -Werror=deprecated-copy

```bash
find SRCROOT -type f -name CMakeLists.txt -exec sed -i -e '$aset(CMAKE_CXX_STANDARD ${CXX_STANDARD})' {} \;
```

<!-- find SRCROOT -type f -name CMakeLists.txt -exec sed -i -e '$aset(CMAKE_CXX_STANDARD ${CXX_STANDARD})' {} \;

