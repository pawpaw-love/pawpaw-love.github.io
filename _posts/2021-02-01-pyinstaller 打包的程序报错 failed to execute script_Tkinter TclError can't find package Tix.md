---
layout: post
title: "pyinstaller 打包的程序报错 failed to execute script_Tkinter TclError can't find package Tix"
date: 2021-02-01 19:21:40 +0800
author: pawpaw-love
tags: [python]
---

## 0x00 环境： 

- win10-64
- python3.8.2 32位  

```sh
pyinstaller -Fw test.py --noupx  # 打包后，exe 文件启动失败，报错：failed to execute script
```

-------------

## 0x01 查找问题过程  

1. 到 `./build/test/warn-test.txt` 中查看有 Tix 引入失败  
```sh
missing module named Tix - imported by TkinterDnD2.TkinterDnD (optional)
missing module named Tkinter - imported by TkinterDnD2.TkinterDnD (optional)
```  
2. 通过 `pyinstaller -D -w test.py --noupx` 命令打包一次，然后进入 `./dist/test/` 中打开命令行，从命令行中启动 exe 文件，提示：  
```sh
_Tkinter TclError can't find package Tix
```
3. 通过 `pyinstaller -D -w --hidden-import=Tix --noupx test.py` 仍然报同样错误 `_Tkinter TclError can't find package Tix`

## 0x02 解决方法  
经查，`tix` 打包需附带额外资源 `–add-data=…tix8.4.3;tix8.4.3`
打包时加上 `--add-data=D:\software\Python\Python-3.8.2\tcl\tix8.4.3;tix8.4.3` (注：windows以;分割，linux以:分割)

即：

```sh
pyinstaller -F -w --add-data=D:\software\Python\Python-3.8.2\tcl\tix8.4.3;tix8.4.3 test.py
```