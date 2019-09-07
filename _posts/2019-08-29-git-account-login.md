---
layout: article
title: Git - 一台电脑绑定两个账号
tags: Git

lang: zh-Hans
key: Git_Account_Login
pageview: true
toc: true
show_subscribe: false
---

## 一、思路

ssh 方式链接到 Github，需要唯一的公钥，如果想同一台电脑绑定两个 Github 帐号，需要两个条件:

1. 能够生成两对私钥/公钥
2. push 时，可以区分两个账户，推送到相应的仓库

## 二、操作方法

1. 查看已有密钥

Mac 下输入命令 `ls ~/.ssh/`，看到 id_rsa 与 id_rsa_pub 则说明已经有一对密钥。

2. 生成新的公钥

```
ssh-keygen -t rsa -f ~/.ssh/id_rsa_home -C "yourmail@xxx.com"
```

并命名为 id_rsa_home (保证与之前密钥文件名称不同即可)。

3. 编辑 config

在 .ssh 文件夹下新建 config 文件并编辑，另不同 Host 实际映射到同一 HostName，但密钥文件不同。Host 前缀可自定义，例子中 ieit。

```
# default                                                             
Host github.com
HostName github.com
User git
IdentityFile ~/.ssh/id_rsa
# two                                                                  
Host ieit.github.com
HostName github.com
User git
IdentityFile ~/.ssh/id_rsa_home
```

4. 拷贝公钥

将生成的 id_rsa.pub，id_rsa_home.pub 内容 copy 到对应的仓库。

5. 设置 push 账号

将常用的账号设置为全局的，将不常用的账号设置到指定仓库下，设置之前要初始化一下 git。

```
git config user.email “xxxx@xx.com”
git config user.name “xxxx”
```

## 参考

- [一台电脑绑定两个 Git 帐号(GitHub和GitLab)](https://blog.csdn.net/jifaliwo123/article/details/79126785)