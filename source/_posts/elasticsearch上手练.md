---
title: elasticsearch上手练
comments: false
date: 2016-12-02 16:50:07
tags:
  - 程序设计
  - elasticsearch
categories:
  - 程序设计
  - elasticsearch
---

# 概述
　　elasticsearch现在在企业应用中很是火爆。快速日志检索，简单高校的分布式支持等等都是它的优势所在。ELK的组合使用也是经典的使用场景。
<!--more-->

# 参考
　　elasticsearch的官方文档非常清晰，不论是对其原理的介绍，还有api的讲解。希望大家还是耐心阅读一番。除此之外，这里还有一个我看到的中文版本的连接，有作者的自己思考，也很不错。
1. [elasticsearch官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)
2. [Mastering Elasticsearch(中文版)](http://udn.yyuap.com/doc/mastering-elasticsearch/index.html)

# 我的实践
　　[实践代码](https://github.com/geeklenny/elasticsearch-restful-javaee-spring-django-rabitmq)
　　这里我提供了一个简单的elasticsearch的实现。主要是通过spring实现了一套restful的后台接口。后台使用elasticsearch进行持久化。同时，使用django提供了一套前端系统。实现比较粗糙，跪在理解和使用练习。
