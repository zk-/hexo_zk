---
title: github以及github page访问缓慢的解决办法
date: 2016-11-30 15:30:53
categories: [工具]
tags: [hosts]
---
如题，解决办法如下
修改hosts，加入这两行，其中zk-.github.io可以换成你的博客地址
```
151.101.100.133 zk-.github.io
192.30.253.113 github.com
```