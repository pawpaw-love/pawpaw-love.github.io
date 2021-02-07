---
layout: post
title: "linux Shell 获取某目录下所有文件/文件夹的名称"
date: 2021-01-25 07:57:22 +0800
author: pawpaw-love
tags: [shell，linux]
---

查看目录下面的所有文件：  

创建并编辑文件 echo_filelist.sh ，内容如下：  
 
```bash
#!/bin/bash
echo "parm: $1" # 读取目标目录
echo "press anykey to continue..."
read
cd $1
for dir in $(ls *)
    do
        [ -d $dir ] && echo $dir
    done
```

查看目录下面的所有目录：

创建并编辑文件 echo_dirlist.sh ，内容如下：  

```bash
#!/bin/bash
echo "parm: $1"
echo "press anykey to continue..."
read
cd $1
for file in $(ls *)
    do
        echo $file
    done
```

--------

使用方法：  

```bash
#例如：
./echo_filelist.sh ./
```


