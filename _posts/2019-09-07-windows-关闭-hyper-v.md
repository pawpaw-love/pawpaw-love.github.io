---
layout: post
title: "windows 关闭 Hyper-v"
feature-img: assets/img/feature-img/hyper-v.png
thumbnail: assets/img/feature-img/hyper-v.png
author: pawpaw-love
tags: [windows, hyper-v]
---

> 微软默认开启了hyper-v,即便是在启用或关闭Windows功能里不启用Hyper-V,也不能解决问题，需要解决的话就需要彻底关闭hyper-v功能。  

管理员权限执行以下:  
关闭: `bcdedit /set hypervisorlaunchtype off`  
开启: `bcdedit / set hypervisorlaunchtype auto`  
重启生效