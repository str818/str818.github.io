---
layout: article
title: Redis - 持久化
tags: Redis
 
lang: zh-Hans
key: Redis_Persistence
pageview: true
toc: true
show_subscribe: false
---

Redis 是内存型数据库，一旦服务器进程退出，Redis 中的数据就会丢失，为了解决这一问题，Redis 提供了两种持久化的方案，将内存中的数据保存到磁盘中，避免数据丢失。