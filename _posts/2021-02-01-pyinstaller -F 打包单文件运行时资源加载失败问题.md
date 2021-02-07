---
layout: post
title: "pyinstaller -F 打包单文件运行时的资源加载失败问题"
date: 2021-02-01 19:45:21 +0800
author: pawpaw-love
tags: [python]
---

## 0x00 环境：

- win10-64
- python3.8.2 32位

## 0x01 关于 pyinstaller 打包单文件的资源路径问题  

  pyinstaller -F 打包单成单一可执行文件后，这个文件在运行时会将资源等解压到系统临时文件夹，从而导致读取资源路径错误。

其实这一问题在 pyinstaller 官方文档的 [Run-time Information][Run-time Information]  就有写到：

You can use the following code to check “are we bundled?”:

In the bundled main script itself the above might not work, as it is unclear where it resides in the package hierarchy. So in when trying to find data files relative to the main script, `sys._MEIPASS` can be used. The following will get the path to a file `other-file.dat` next to the main script if not bundled and in the bundle folder if it is bundled:

```python
from os import path
import sys
bundle_dir = getattr(sys, '_MEIPASS', path.abspath(path.dirname(__file__)))
path_to_dat = path.abspath(path.join(bundle_dir, 'other-file.dat'))
```

Or, if you’d rather use [pathlib](https://docs.python.org/3/library/pathlib.html):

```python
from pathlib import Path
import sys

if getattr(sys, 'frozen', False) and hasattr(sys, '_MEIPASS'):
    bundle_dir = Path(sys._MEIPASS)
else:
    bundle_dir = Path(__file__).parent

path_to_dat = Path.cwd() / bundle_dir / "other-file.dat"
```

It is always best to use absolute paths, so `path.abspath(path.join(bundle_dir, 'other-file.dat'))` is preferred over `path.join(bundle_dir, 'other-file.dat')`. Using relative paths can lead to confusing errors should you attempt to find the parent of the current working directory. You’ll get the incorrect value `''` instead of the expected `'..'`:

[Run-time Information]: https://pyinstaller.readthedocs.io/en/stable/runtime-information.html

