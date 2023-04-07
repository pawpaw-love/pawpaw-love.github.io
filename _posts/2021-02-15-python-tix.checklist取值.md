---
layout: post
title: python tix.checklist 添加条目以及获取条目内容
date: 2021-02-25 17:57:22 +0800
color: blue
author: pawpaw-love
tags: [python, gui]
---  

tkinter 作为 Tcl/Tk 的 python 接口库是真 TM 麻烦，tix.checklist 网上能找到的 python 资料里，全都是告诉怎么创建条目，我就纳了闷了，这些写博客写教程的，就只创建条目用来好看么，不获取条目的具体值那有毛线用啊。真是烦。  

害我直接跑到 tix.py 里找，终于在 `Hlist` 类里找到了一个能用的方法，叫 `item_cget`，其实也是调用的 `tk.call` ，其实绝大部分功能的实现都是调用 `tk.call` ，而在 tix.py 里没有它的定义，按照继承关系一路往上扒愣是没照到定义，又到网上查了一下，果然都说没有官方文档，也没看到别人找到具体函数方法解释的。  

反正能用就行，先把 `Hlist.item_cget` 的定义和用法贴在这里了：  
 `Hlist.item_cget` 定义：
```python
def item_cget(self, entry, col, opt):
    return self.tk.call(self._w, 'item', 'cget', entry, col, opt)
```
可见主要就是传三个参数 `entry`, `col`, `opt` 其中 `entry` 就是 `checklist.hlist.add` 对应的参数，这里也贴出定义：  
```python
def add(self, entry, cnf={}, **kw):
    return self.tk.call(self._w, 'add', entry, *self._options(cnf, kw))
```
用法:  
```python
check_l = CheckList(root)  # 创建 checklist
check_l.hlist.add('CL0.Item1', text='data here')  # 添加条目
check_l.hlist.item_cget('CL0.Item1', 0, '-text')  # 读取条目的值
```  
这里 '-text' 不能用 'text' 替代，真是坑，全网到处都找不到用法，我是硬生生自己尝试出来的...
