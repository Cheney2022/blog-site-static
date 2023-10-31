---
title: vscode/vs 配置 vcpkg 的c++ 开发环境
tags:
  - vscode
  - vs
  - vcpkg
  - c++
categories:
  - 开发
  - c++
keywords: 'vscode,vcpkg,c++,vs'
description: '针对vscode,vs 等编辑器实现对cmake、vcpkg的集成，实现cmake编译系统的一键下载第三方依赖包、编译。'
abbrlink: 8694
date: 2023-09-25 00:46:31
---
# Vistual Studio

Vistual Studio 配置vcpkg的环境开发需要在勾选vcpkg 组件即可。

<!--more -->

# VS Code

## 前提准备

- VS Code
- cmake
- vcpkg

## 插件安装

VS Code  需要安装一下插件

- c/c++
- cmake
- cmake Tools

## 配置vcpkg

在setting.json 文件中添加

```bash
"cmake.configureSettings": {
      "CMAKE_TOOLCHAIN_FILE": "${VCPKG_ROOT}scripts/buildsystems/vcpkg.cmake"
  }
```

> setting.json 为vscode 的配置文件，其存在项目、全局、和远程 三种配置，具体配置那种可按照自身意愿。

## 创建测试工程

### 安装spdlog 库
在项目目录下创建vcpkg.json文件，

```json
{
    "$schema": "https://raw.githubusercontent.com/microsoft/vcpkg-tool/main/docs/vcpkg.schema.json",
    "name": "mylibrary",
    "version": "1.0",
    "dependencies": [
        {
            "name": "spdlog",
            "version>=": "1.12.0",
            
        }
    ],
    "builtin-baseline": "3f6e8e9c09e28a0eae4b37cceb2982ca2518278c"
}
```


### 创建工程

```cmake
cmake_minimum_required(VERSION 3.0.0)
  project(vcpkg_test VERSION 0.1.0 LANGUAGES C CXX)

  include(CTest)
  enable_testing()
  find_package(spdlog CONFIG REQUIRED)
      
  add_executable(vcpkg_test main.cpp)
  target_link_libraries(main PRIVATE spdlog::spdlog_header_only)
  set(CPACK_PROJECT_NAME ${PROJECT_NAME})
  set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
  include(CPack)
```

```c++
#include <iostream>
  #include<spdlog/spdlog.h>
  int main(int, char **) {
    spdlog::info("Hello, world!");
  }
```

完成以上操作， 直接按下 ctrl+shift+p ，输入

```bash
cmake:configure
```
> 上述命令若无法完成spdlog包的下载，则执行一下命令后，重新输入以上命令
> ```bash
> vcpkg integrate install
> ```
等待完成后，直接按下 ctrl+shift+p ，输入

```bash
cmake:build
```

等待编译完成

