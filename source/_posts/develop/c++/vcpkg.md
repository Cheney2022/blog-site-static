---
title: vcpkg 设置景象代理
tags:
  - github
  - vcpkg
  - c/c++
categories:
  - 开发
  - c/c++
keywords: 'github,vcpkg,c++'
description: "GitHub是目前全球最大的开源代码库之一。但是因为国内经常访问不了Github,或者访问速度慢，导致国内机器无法拉取项目部署，解决这些问题可以通以下国内镜像，来提高访问速度。"
 
abbrlink: 36536
date: 2023-09-25 00:57:56
---


> 针对github release、archive以及项目文件的加速项目，和vcpkg 使用代理镜像镜像加速方式

# github  镜像

GitHub是目前全球最大的开源代码库之一。但是因为国内经常访问不了Github,或者访问速度慢，导致国内机器无法拉取项目部署，解决这些问题可以通以下国内镜像，来提高访问速度。

|  域名   | 是否有效  | clone 加速 |Raw|Releases|api|
:------------               | :------------- |:-------------|:-------------|:-------------|:-------------|
|https://gitclone.com/      | ✔️ |https://gitclone.com/github.com  | https://gitclone.com/raw.githubusercontent.com/|https://gitclone.com/github.com|
|https://hub.fastgit.xyz/ | x |https://hub.fastgit.xyz|https://raw.fastgit.org|https://hub.fastgit.xyz/|
|https://mirror.ghproxy.com | x  |https://mirror.ghproxy.com/https://github.com/| https://mirror.ghproxy.com/https://raw.githubusercontent.com/|https://mirror.ghproxy.com/https://github.com/|
|https://ghproxy.com | ✔️|https://ghproxy.com/https://github.com/|https://ghproxy.com/https://raw.githubusercontent.com/|https://ghproxy.com/https://github.com/|

> 注: api.github.com 不需要加速可直接访问.
> 注: raw.githubusercontent.com 支持ipv6 所以如支持IPV6可以不设置

# vcpkg 使用代理镜像镜像加速方式

Vcpkg是微软团队在GitHub上的一个开源项目，他类似于poython pip 的c++包管理工具.它提供一系列简单的命令，其支持从各种存储方式拉取源码然后编译成三方库.(如url gitlab github git sourceforge 等开源社区).因大部分的源码存在github因此同样的存在无法访问或者存在下载超时的情况.因此可以通过以下加速vcpkg:

- 替换scripts/vcpkgTools.xml 所有的github.com
- 将scripts/cmake/vcpkg_from_github.cmake 中 set(github_host "https://github.com") 替换set(github_host "https://hub.fastgit.xyz")

> 注1: github_host 也可以通过设置arg_GITHUB_HOST 实现
> 注2: vcpkg 并没对raw.githubusercontent.com 引入.因此大多数在ports的引入的,没有更好的解决方式.可以尝试直接修改 raw.githubusercontent.com 指向hub.fastgit.org
> 注3: 该修改方式并不能影响ports

