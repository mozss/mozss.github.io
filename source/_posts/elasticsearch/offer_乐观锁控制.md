---
title: 乐观锁控制
tags:
  - elasticsearch
categories:
  - it
  - it-java
abbrlink: 14801
date: 2020-04-12 20:16:20
---
  
[![jTavVK.jpg](https://s1.ax1x.com/2022/07/19/jTavVK.jpg)](https://imgtu.com/i/jTavVK)

<!--more-->

elasticsearch是分布式的, 因此也就需要一种方式来**确保旧版本的文档永远不会覆盖新版本的问题**

为了确保文档的就版本不会覆盖教新的版本, 对文档执行的每个操作都会有协调该更改的主分片**分配一个序列号**. 每次操作都会增加序列号, 因此新操作可以保证比旧操作具有更高的序列号. 