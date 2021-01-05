---
layout: post
title: "SavePageWe 保存的 csdn 网页 html 打开后自动跳转问题的解决过程"  
date: 2019-08-23 08:12:36 +0800
image: 
tags: [csdn, savepagewe]
categories: chrome
---
> ## 0x00 故事背景
> 今天捣腾 golang 的时候，发现一直没装 mingw 的 gcc。  
> 下载的空档时间里玩儿了会儿 dosbox 下的 turboc2.0 ，发现绘图时报错: `BGI Error:Graphics not initialized` 。 解决方法写成了: xx.md  
> 于是搜了一下，找到了一篇可以参考的 csdn 博客文章，想存下来跟 turboc2.0 放在一起，以备后查。  
> 结果保存下来的 html 文件再打开的时候过两三秒就会自动跳转到 csdn 首页去，忧伤。  

## 0x01 解决过程  

* 既然不是立即跳转，而是有着明显的停顿，那显然与 **时延** 有关，以‘html自动跳转’为关键词搜了一下，找到一篇 [HTML页面3秒后自动跳转的三种常见方法](https://www.cnblogs.com/lishaohua/p/6291701.html) 具体的就不全搬过来了可以走链接去看。简单列一下:
    1. META http-equiv="refresh"
    2. window.setTimeout(,3000)
    3. setInterval(go, 3000)  

* 剩下的就很简单了。用记事本打开 html ，拿以上三种作为关键字搜索，把找到的代码块儿删了就行了


