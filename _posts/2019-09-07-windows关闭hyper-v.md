---
layout: post
title: "windows 关闭 Hyper-v"
date: 2019-09-07 16:22:36 +0800
image: windows.jpg
tags: [windows, hyper-v]
categories: windows
---

> 微软默认开启了hyper-v,即便是在启用或关闭Windows功能里不启用Hyper-V,也不能解决问题，需要解决的话就需要彻底关闭hyper-v功能。  

管理员权限执行以下:  
关闭: `bcdedit /set hypervisorlaunchtype off`  
开启: `bcdedit / set hypervisorlaunchtype auto`  
重启生效