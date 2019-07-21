---
layout: article
title: 计算机网络 — HTTPS 详解
tags: 计算机网络

lang: zh-Hans
key: Network_HTTPs
pageview: true
toc: true
show_subscribe: false
---

## 一、什么是 HTTPS

HTTPS 是在 HTTP 的基础上建立 SSL 加密层，并对传输数据进行加密，是 HTTP 协议的安全版。

HTTPS 能够对数据进行加密，建立一个信息安全通道，来保证传输过程的数据安全，同时能够对服务器进行真实身份认证。

HTTP 协议由于是明文传送，存在三大风险：

1. 被窃听的风险：第三方可以截获并查看你的内容
2. 被篡改的危险：第三方可以截获并修改你的内容
3. 被冒充的风险：第三方可以伪装成通信方与你通信

因为存在以上三大安全风险，所以才有了 HTTPS 的出现。

HTTPS 并不是新协议，而是让 HTTP 先和 SSL（Secure Sockets Layer）通信，再由 SSL 和 TCP 通信，也就是说 HTTPS 使用了隧道进行通信。

通过使用 SSL，HTTPS 具有了加密（防窃听）、认证（防伪装）和完整性保护（防篡改）。

## 二、加密方式

### 1. 对称加密

加密和解密使用同一密钥