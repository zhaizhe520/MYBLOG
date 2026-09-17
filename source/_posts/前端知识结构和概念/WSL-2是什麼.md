---
title: WSL 2是什麼?
date: 2026-04-13 16:40:59
tags:
categories:
  - 為什麼
---
# WSL 2 = Windows 里的「轻量 Linux 虚拟机」，让你在 Windows 上直接跑 Linux 程序，不用装双系统，也不用开笨重的 VMware 虚拟机。
一、WSL 2 全称 & 本质
全称：Windows Subsystem for Linux 2（Windows Linux 子系统 第 2 代）
本质：微软给 Windows 做的「原生 Linux 兼容层」，用轻量虚拟机技术，在 Windows 里跑一个完整的 Linux 内核。
作用：让你在 Windows 上直接用 Linux 命令、跑 Linux 程序，和真实 Linux 系统几乎没区别，而且性能比第一代 WSL 强太多。
--------------------------
二、为什么 Docker 在 Windows 上必须用 WSL 2？
Docker 原本是为 Linux 设计的技术，它的核心功能（容器隔离、cgroups、namespace）都是 Linux 内核的特性。
在 Linux 上：Docker 直接用系统内核，不用额外东西。
在 Windows 上：Windows 内核不支持 Docker 依赖的 Linux 特性，所以必须用 WSL 2 提供一个「Linux 环境」，让 Docker 在里面跑。
简单说：WSL 2 是 Docker 在 Windows 上的「地基」，没有地基，Docker 根本盖不起来。
-----------------------
三、WSL 2 有什么用？（除了给 Docker 用）
除了跑 Docker，它还有这些超实用的功能：
直接在 Windows 用 Linux 命令：比如 ls、grep、awk、ssh，不用装 Git Bash 之类的工具。
跑 Linux 专属软件：比如 Nginx、MySQL、Redis、Python/Java 环境，不用装 Windows 版，直接用 Linux 版，配置更简单。
开发环境统一：Windows 上写代码，WSL 2 里跑 Linux 环境，和服务器环境完全一致，避免「本地跑没问题，上线就崩」。
文件互通：Windows 的 C:\ 盘，在 WSL 2 里直接挂载成 /mnt/c/，可以直接读写，不用来回传文件。
-------------------
四、WSL 2 和 普通虚拟机（VMware/VirtualBox）有什么区别？
```
特性	WSL 2	VMware/VirtualBox
启动速度	秒开，几乎无等待	慢，要等虚拟机开机
资源占用	极低，只占少量内存 / CPU	高，要给虚拟机分配固定资源
文件互通	原生互通，直接读写 Windows 文件	麻烦，要共享文件夹、传文件
性能	接近原生 Linux，IO 性能极强	有性能损耗，不如 WSL 2
使用场景	日常开发、跑 Docker、轻量 Linux 环境	完整虚拟机、多系统测试、图形化 Linux
```
--------------------
wsl --shutdown
补充：WSL 1 和 WSL 2 有什么区别？
WSL 1：第一代，用「翻译层」把 Linux 命令翻译成 Windows 命令，兼容性差、性能弱，现在已经淘汰。
WSL 2：第二代，用真实 Linux 内核，100% 兼容 Linux 程序，性能拉满，是现在的标准版本，Docker 只支持 WSL 2，不支持 WSL 1。