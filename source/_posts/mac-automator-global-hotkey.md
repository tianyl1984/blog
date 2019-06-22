---
layout: post
title: mac 如何通过 Automator 配置全局快捷键打开应用程序
description: mac 如何通过 Automator 配置全局快捷键打开应用程序
category: mac
---

### 背景
在 Mac 中有多种方式设置全局快捷键快速打开应用程序，借助 Automator 可以在不使用第三方软件仅使用系统配置的情况下做到这一点。

### 配置 Automator 脚本

打开 Automator 

![](/images/hotkey1.png)

选择“服务”，依次选择“实用工具”、“开启应用程序”、服务收到选择“没有输入”、选择要开启的应用程序

![](/images/hotkey2.png)

操作完成之后保存脚本。

**注意：脚本必须保存到 ~/Library/Services 目录，默认保存路径不同系统版本会有不同，可以使用 Command+Shift+Option+S 快捷键保存到 ~/Library/Services 下**

### 配置快捷键

打开“系统偏好设置”、“键盘”、“快捷键”，选择“服务”，在“通用”下可以看到配置的脚本，设置快捷键即可。如果不在“通用”下，可能是保存路径问题，也可能是配置脚本时“服务收到”选择错误导致。

### 注意

配置脚本时特殊程序不能选择，比如“Finder”，可以选择一个变量，指向 finder.app

