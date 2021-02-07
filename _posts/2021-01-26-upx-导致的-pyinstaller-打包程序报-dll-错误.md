---
layout: post
title: "upx 导致的 pyinstaller 打包程序报 dll 未在指定的 windows 上运行"
date: 2021-01-26 07:15:02 +0800
tags: [python]
---

环境： 

- win10-64 
- python3.8.2 32位 

pyinstaller 打包后报多个 dll 未在指定的 windows 上运行。查到说是 upx 版本问题导致的。
pyinstaller 默认会自动调用已安装的 upx 进行压缩，但不会查 upx 32 位还是 64 位，是否与 python 匹配，结果导致错误。

解决方法：

**1. 可以通过加入 `--noupx` 参数打包**  
**2. 也可以通过 `--upx-dir=D:\software\upx-3.96-win32` 来指定正确版本的 upx 位置**（但不知道为什么测试还是不成功，懒得深究了，就不用 upx 了）

