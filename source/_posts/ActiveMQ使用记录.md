---
title: ActiveMQ使用记录
comments: false
date: 2016-12-15 13:14:16
tags:
  - 读书笔记
  - ActiveMQ
categories:
  - 读书笔记
  - ActiveMQ
---
　　今天简单的浏览了一些招聘需求，其中对消息队列所需的技术要求一般都是"熟悉ActiveMQ"。虽然之前做项目，使用过celery+rabbitmq的异步消息机制。这里还是为了给自己的简历贴一些金，来上手练一下ActiveMQ的使用。
# 安装
### 开发运行环境
```bash
> lennybai@lennybai~$ lsb_release -a
> No LSB modules are available.
> Distributor ID:	Ubuntu
> Description:	Ubuntu 16.04.1 LTS
> Release:	16.04
> Codename:	xenial
```
　　对于工具的使用，用心去研读一下官方文档还是很有必要的。在参考文献中我列出了官网的链接，感兴趣的朋友可以阅读一下。ActiveMQ的环境要求，在官方文档中指出为：JRE和JDK。当然我是个懒汉，在这里不太希望用源码去安装ActiveMQ。我是这么干的:
1. 安装java
> sudo add-apt-repository ppa:webupd8team/java  
> sudo apt-get update  
> sudo apt-get install oracle-java8-installer

2. 安装ActiveMQ
> [官网下载](http://activemq.apache.org/download.html)并解压，在bin目录下运行自己系统相同的activemq程序，执行./activemq start

　　在web控制台打开[http://127.0.0.1:8161/admin/](http://127.0.0.1:8161/admin/)。初始用户名:admin 密码:admin。[activemq_install_dir]/data/activemq.log"中可以查看运行日志。netstat -an | grep 61616可以查看端口运行情况。
　　activemq我认为比较好的有三点:开源，漂亮控制台和没有繁琐的配置。

# 试用


***
# 3 参考文献
- [1]: [ActiveMQ Document](http://activemq.apache.org/version-5-getting-started.html#Version5GettingStarted-DocumentOrganization)

# 使用
