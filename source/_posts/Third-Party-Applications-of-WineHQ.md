---
title: 'Third Party Applications of WineHQ '
comments: false
date: 2016-12-28 10:05:18
tags:
categories:
---

# 序
　　工作需要，简单调研一下WineHQ相关的内容。主要是摘要记录，不做更多思考。

# 一、概述
　　Wine （“Wine Is Not an Emulator” 的首字母缩写）是一个能够在多种 POSIX-compliant 操作系统（诸如 Linux，Mac OSX 及 BSD 等）上运行 Windows 应用的兼容层。 Wine 不是像虚拟机或者模拟器一样模仿内部的 Windows 逻辑，而是將 Windows API 调用翻译成为动态的 POSIX 调用，免除了性能和其他一些行为的内存占用，让你能够干净地集合 Windows 应用到你的桌面。[1]

<!--more-->

　　Wine 项目由 Bob Amstadt 于 1993 年发起，旨在寻求一种在 Linux 上运行 Windows 3.1 程序的办法。不久之后，Alexandre Julliard 开始接手领导 Wine 的开发，从此由他管理这个项目。 多年以来，随着 Windows API 和应用为了适应新硬件及软件而不断演变，Wine 也一直不断发展来支持新的特性，移植到更多其他系统，并且更加稳定，提供着更好的用户体验。[1]

　　为了让WINE变得更强大并且更容易被使用，有很多第三方的应用对它进行支持和扩展，本文主要对官网列举出来的部分第三方应用进行简略说明。

# 二、Wine第三方应用
　　在wine的官网列举出两大类三方软件:仍在维护的和不再更新的。在仍在维护的三方软件中，我们最耳熟能详的当然就是crossover。不过我们关注的重点是除它之外的软件，看能否发现一些独特的特性。

## gnome-exe-thumbnailer
　　在ubuntu软件包中我们可以找到这个软件。同样可以通过apt-get gnome-exe-thumbnailer来进行安装。这个三方应用主要的作用是将windows的exe按照原有的图标进行显示。详细的说，比如我们常用的qq图标是一个小企鹅。而ubuntu默认会将exe识别为同一种类型文件而显示默认的类型图标。安装这个三方应用后，它会提取原有的window图标加以显示。更多可以查看[这个视频](https://www.youtube.com/watch?v=9YXhNNCSwv8).

## q4wine
　　q4wine是一个用来安装和管理wine程序的kde工具。我们可以用q4wine去安装程序。同样可以去管理wine安装的应用程序。更多效果可以查看[这个视频](https://www.youtube.com/watch?v=951nVQQJhks&t=244s).

## Winetricks
　　winetricksWinetricks is a helper script to download and install various redistributable runtime libraries needed to run some programs in Wine. These may include replacements for components of Wine using closed source libraries.
　　winetricks整体上看起来更像是一个包依赖管理器。通过运行winetricks脚本，为wine应用提供相应的动态库支持。更多效果可以查看[这个视频](https://www.youtube.com/watch?v=tqrVR1QjDPQ).

# 三、其他
　　这些三方库都可以在github或者google code上找到相应的源码，如果想要深入了解，可以进行源码阅读。
　　令人遗憾的是，这些三方库的参考文档并不丰富，所以想要了解，还是需要看看别人的使用视频或者亲身感受一下。

　　
***
# 3 参考文献

- [1]: [wine中文社区](http://www.oschina.net/p/wine/?fromerr=eLEvCq6E)
- [2]: [WINEHQ](https://wiki.winehq.org/Third_Party_Applications)
