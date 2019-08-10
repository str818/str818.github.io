---
layout: article
title: Git - 常用命令总结
tags: Git

lang: zh-Hans
key: Git_Common_Commands
pageview: true
toc: true
show_subscribe: false
---

初始化 Git 仓库：

`git init`，自动生成 `.git` 文件，该文件时隐藏的，用 `ls -ah` 命令可以看到。

添加文件到 Git 仓库：

1. 使用命令 `git add <file>`，注意，可反复多次使用，添加多个文件；
2. 使用命令 `git commit -m <message>`，完成。
