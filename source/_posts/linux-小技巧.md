---
title: linux 小技巧
comments: false
date: 2016-11-22 09:27:34
tags:
  - linux
  - 系统相关
categories:
  - 操作系统
  - 使用技巧
---

##　命令行对错误信息的处理
　　在使用控制台的时候，往往在得到正确结果的同时，也会跟随者一些并不重要的错误信息，比如:

![find mysql](http://ogelzbmrh.bkt.clouddn.com/findmysql.png)

　　我们发现，当我们使用find命令去搜索mysql相关的内容的时候，除了我们想要的路径信息外，还有很多不必要的"permission denied"错误信息。这时候我们就可以通过将错误重定向到/dev/null来解决这个问题:
```bash
find / -name mysql 2>/dev/null
```
