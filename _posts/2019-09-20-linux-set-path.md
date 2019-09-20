---
layout: article
title: Linux - 设置 Path
tags: Linux

lang: zh-Hans
key: Linux_Set_Path
pageview: true
toc: true
show_subscribe: false
---

## 一、查看现有 Path

```
echo $PATH
```

## 二、修改 Path

### 1. 临时修改，关闭连接后失效

```
export PATH=/usr/local/bin:$PATH
```

### 2. 永久修改当前用户

```bash
vim ~/.bashrc 
// 在最后一行添上：
export PATH=/usr/local/bin:$PATH
```

关闭保存，执行 `source ~/.bashrc` 命令生效

### 3. 全局修改，所有用户生效

```bash
vim /etc/profile
// 在最后一行添上：
export PATH=/usr/local/bin:$PATH
```

关闭保存，执行 `source /etc/profile` 命令生效

## 三、每次启动自动加载 .bashrc

在 `.bash_profile` 文件中自动加载 `.bashrc` 文件。

```bash
vim ~/.bash_profile
```

在文件内部输入：

```bash
# 加载.bashrc文件
if test -f .bashrc ; then
source .bashrc 
fi
```


## 参考

- [Linux 修改 PATH 环境变量](https://www.jianshu.com/p/9d680bdbc170)
- [解决 .bashrc 文件每次打开终端都需要 source 的问题](https://www.jianshu.com/p/c4946024b946)