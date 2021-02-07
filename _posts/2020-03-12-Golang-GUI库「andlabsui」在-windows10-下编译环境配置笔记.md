---
layout: post
title: Golang GUI库「andlabs/ui」在 windows10 下编译环境配置笔记
feature-img: assets/img/feature-img/golang.jpg
thumbnail: assets/img/feature-img/golang.jpg
author: pawpaw-love
tags: [golang, gui, envirement]
---
## andlabs/ui 在 win10 的编译环境配置笔记

1. 安装 golang：[国内用这个吧，官方的因众所周知的原因经常抽风](https://links.jianshu.com/go?to=https%3A%2F%2Fstudygolang.com%2Fdl)
2. 安装git：[https://git-scm.com/download/win](https://links.jianshu.com/go?to=https%3A%2F%2Fgit-scm.com%2Fdownload%2Fwin)
3. 安装mingw64（这个是重点）：[http://www.msys2.org/](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.msys2.org%2F)
   3.1. 将下载的 msys2-x86_64-版本号xxx.exe 安装在 C:/msys64/ 目录下
   3.2. 安装完成后，运行 C:/msys64/mingw64.exe
   3.3. 在终端输入：`pacman -S mingw-w64-x86_64-gcc`
   *此举是安装  “mingw-w64-x86_64-gcc” ，不安装的话 C:\msys64\mingw64\bin 目录下是空的，编译会产生 “undefined reference to \`__imp_TaskDialog'” 的错误
   还可以通过 `pacman -Sl | grep gcc` 命令查看所有安装的包，根据自己的平台选择安装*
   3.4. 在系统环境变量 path 中加入：C:\msys64\mingw64\bin
   3.5. 重启系统

至此就可以在win下对go文件进行编译了。