---
layout: post
title: "如何为 Windows 10 UWP 应用设置代理"
date: 2021-10-12
author: lucklcy
cover: "/assets/img/uwp.jpg"
tags: uwp
---

# 如何为 Windows 10 UWP 应用设置代理

UWP 是微软在 Windows 10 中引入的新概念，由于所有 UWP 应用均运行在被称为 App Container 的虚拟沙箱环境中，其安全性及纯净度远胜于传统的 EXE 应用。但 App Container 机制同时也阻止了网络流量发送到本机（即 loopback）， 使大部分网络抓包调试工具无法对 UWP 应用进行流量分析。同样的，该机制也阻止了 UWP 应用访问 localhost，即使你在系统设置中启用了代理，也无法令 UWP 应用访问本地代理服务器，十分恼人。

其实 Windows 10 自带了一款名为 CheckNetIsolation.exe 的命令行工具可以帮助我们将 UWP 及 Windows 8 Metro 应用添加到排除列表，具体步骤如下：

## 1.通过注册表获取应用的 SID

首先通过 Win + R 快捷键打开「运行」窗口，输入「Regedit」打开注册表编辑器，然后定位到 HKEY_CURRENT_USER\Software\Classes\Local Settings\Software\Microsoft\Windows\CurrentVersion\AppContainer\Mappings，接着在左边的注册表项中找到你想解除网络隔离的应用，右边的 DisplayName 就是应用名称，而左边那一大串字符就是应用的 SID 值了。

![image](/assets/img/uwp/reg_sid.jpg)

## 2.打开 CMD 面板

在终端中输入 CheckNetIsolation.exe loopbackexempt -a -p=SID，出现「完成」后就大功告成了。

![image](/assets/img/uwp/cmd_sid.jpg)

这时再打开 UWP 应用窗口，你应该就能愉快的上网了。

![image](/assets/img/uwp/uwp_success.jpg)

转自：[【知乎】](https://zhuanlan.zhihu.com/p/29989157) 如何为 Windows 10 UWP 应用设置代理 侵删
