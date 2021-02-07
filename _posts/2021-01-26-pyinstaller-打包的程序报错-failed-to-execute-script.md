---
layout: post
title: "pyinstaller 打包的程序启动报错 failed to execute script"
date: 2021-01-26 08:15:02 +0800
tags: [python]
---

环境：

- win10-64  
- python3.8.2 32位  

```sh
pyinstaller -Fw test.py --noupx  # 打包后，exe 文件启动失败，报错：failed to execute script
```

1. 首先可以到 `./build/test/warn-test.txt` 中查看是否有 import 错误
    - 若存在 pyinstaller 无法准确获取库文件路径的情况，可以 `--paths C:/***/` 形式告诉它去哪里找
2. 可以通过 `pyinstaller -D test.py --noupx` 命令开启 Debug 打包一次，然后进入 `./dist/test/` 中打开命令行，从命令行中启动 exe 文件，便可以看到具体的错误提示了
