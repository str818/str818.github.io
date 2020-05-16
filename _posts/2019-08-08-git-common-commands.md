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

## 概念

**工作区** 是指当前能看到的目录与文件。

工作区中有一个隐藏的目录 `.git` ，是 Git 的版本库，里面存放了一个 **暂存区（stage）**，`git add` 是将文件修改添加到暂存区，`git commit` 是将暂存区的所有内容提交到当前分支。

<div align="center"> <img src="https://s1.ax1x.com/2020/05/16/Y69OJJ.png" width="90%"/> </div> 

`HEAD` 表示当前版本，`HEAD^` 表示上一个版本，`HEAD^^` 表示上上一个版本，往上第 100 个版本表示为 `HEAD~100`。

Git 跟踪并管理的是修改而非文件。

## 新建代码库

1. 初始化 Git 仓库：`git init`，初始化后自动生成一个 `.git` 目录，默认是隐藏的，使用 `ls -ah` 可以看到。
2. 下载一个项目：`git clone [url]`
3. 添加文件到 Git 仓库：
   - `git add <file>`
   - `git commit -m <message>`

## 增加/删除文件

1. 添加文件到暂存区：`git add <file>`
2. 添加当前目录的所有文件到暂存区：`git add .`
3. 删除工作区文件，并将这次删除放入暂存区：`git rm <file>`

## 查看信息

1. 显示所有变更的文件：`git status`
2. 显示当前分支的历史版本：`git log`
3. 查看暂存区和工作区的差异：`git diff`
4. 查看当前分支的最近几次提交：`git relog`，能查看所有操作记录，包括 reset 和已经删除的分支
5. 查看文件的修改人：`git blame -L 160,+10 <file>`

## 撤销

1. 回退版本，重置当前分支的 HEAD 为指定的 commit，同时重置暂存区和工作区，与指定 commit 一致：`git reset --hard <commit>`
2. 回退版本，不重置暂存区，`git reset --soft <commit>`
3. 将文件在工作区的修改全部撤销，一键还原，回到最近一次 `git commit` 或 `git add` 时的状态：`git checkout -- <file>`
4. 将暂存区的修改回退到工作区：`git reset HEAD <file>`
5. 当手头工作没有完成时，先把工作现场 `git stash` 一下，然后去修复 bug，修复后，再 `git stash pop`，回到工作现场
6. 清空工作区修改过的文件：`git checkout .`
7. 清空工作区新建的文件和文件夹：`git clean -d`

## 分支

1. 列出所有本地分支：`git branch`
2. 列出所有远程分支：`git branch -r`
3. 列出所有本地分支和远程分支：`git branch -a`
4. 创建 + 切换分支：`git checkout -b <branch>`
5. 从远程拉取某一分支到本地：`git checkout -b <本地分支名> origin/<远程分支名>`
6. 切换分支：`git checkout <branch>`
7. 合并某分支到当前分支：`git merge <branch>`
8. 删除分支：`git branch -d <branch>`，强行删除：`git branch -D <branch>`
9. 查看分支合并图：`git log --graph`

当 Git 无法自动合并分支时，必须首先解决冲突，之后再提交，合并完成。解决冲突就是把 Git 合并失败的文件手动编辑为希望的内容，再提交。

合并分支时，如果没有冲突，Git 会用 Fast forward 模式（直接把 master 指向当前分支）。这种模式下，删除分支后，会丢掉分支信息。合并分支时，加上 `--no-ff` 参数就可以用普通模式合并，合并后的历史有分支，能够看出来曾经做过合并，而 Fast forward 合并就看不出来曾经做过合并。

## 代码合并 - rebase

1. 合并多次 commit：将所修改的这次提交与前一次提交合并成一次提交记录，`git rebase -i HEAD~4`，查看最近四次的提交，将 pick 改为 squash，保存退出，再新弹出的窗口中编辑 commit message。
2. 合并分支：`git rebase <branch> `，将当前分支的每个 commit 取消掉，并将其临时保存为 patch，存放在 `.git/rebase` 目录中，然后将当前分支更新为最新的 `<branch>` 分支，最后把保存的这些补丁应用到当前分支上。
3. 解决合并分支的冲突后，用 `git add` 更新索引，无需执行 `git commit` 命令，只需要执行 `git rebase --continue`。
4. 终止 rebase：`git rebase --abort`，当前分支会回到 rebase 开始前的状态。
5. `git pull --rebase` 先更新代码，基于最新代码进行开发

推荐阅读：[git rebase 使用笔记](https://www.jianshu.com/p/cca69cb695a6)

## 远程同步

1. 关联远程仓库：`git remote add <remote> <url>`，`<remote>` 远程库一般叫做 `origin`
2. 上传本地指定分支到远程仓库：`git push <reomte> <branch>`，一般为 `git push origin master`，第一次推送要加上 `-u` 参数，`git push -u origin master`
3. 查看远程库信息：`git remote`，详细信息：`git remote -v`
4. 下载远程仓库的代码：`git fetch`，下载并合并远程仓库的代码：`git pull`

## 多人协作工作模式

1. 先拉取一下最新的远程代码 `git pull origin <branch-name> --rebase`
2. 有冲突就在本地解决冲突，解决好之后再 `git push origin <branch-name>` 推送自己的修改

## 标签

1. 新建标签：`git tag <tagname>` ，默认为 `HEAD`，也可以指定一个 commit id
2. 新建并指定标签信息：`git tag -a <tagname> -m "..."`
3. 查看所有标签：`git tag`
4. 删除标签：`git tag -d <tagname>`
5. 删除一个远程标签：`git push origin :refs/tags/<tagname>`
6. 推送本地标签到远程：`git push origin <tagname>`
7. 推送全部本地标签到远程：`git push origin --tags`

## 配置

1. `git config --global user.name <yourname>`
2. `git config --global user.email <youremail>`
3. 查看当前 Git 的配置：`git config --list`

## 忽略特殊文件

在 Git 工作区的根目录下创建一个特殊的 `.gitignore` 文件，然后把要忽略的文件名填进去，Git 就会自动忽略这些文件 `.gitignore` 文件本身需要放到版本库里，并且可以对 `.gitignore` 进行版本管理。

忽略文件的原则：

1. 忽略操作系统自动生成的文件
2. 忽略编译生成的中间文件、可执行文件等
3. 忽略带有敏感信息的配置文件，比如存放口令的配置文件

## 合并代码，提交到线上

1. 添加工作区内容到缓存区：`git add .`
2. 提交 commit 到分支：`git commit -m 'xx'`
3. 获取远端最新版本到本地：`git fetch`
4. 合并远端最新版本：`git rebase origin/xx`，或者直接将 3.4 合并成 `git pull origin/xx --rebase`
5. 如果出现冲突，修改冲突，然后 `git add` 更新内容索引，之后继续执行 `git rebase --continue`
6. 提交 review：`git push origin HEAD:refs/for/master%r=reviewer, r=reviewer`
7. 二次提交 review：`git add .`
8. 修补提交：`git commit --amend`，不会产生新的 change id，如果 commit 信息有误也可以在这里修改
9. 提交 review：`git push origin HEAD:refs/for/master`
10. 多次提交重复 7 - 9

## Gerrit

Gerrit 实际上是一个 Git 服务器，它为在其服务器上托管的 Git 仓库提供一系列权限控制，以及一个用来做 Code Review 是 Web 前台页面，主要功能是用来做 Code Review 的。

Gerrit 相对 Git 提供了一个特有的命名空间 `refs/for/<branch-name>` 用来定义我们的提交上传到哪个 branch，且可以用来区分 commit 是提交到 Gerrit 进行审核还是直接提交到 Git 仓库。

`git push origin HEAD:refs/for/master`

## GitLab

GitLab 是一个数据库支持的 web 应用，所以相比于其他 Git 服务器，它的安装过程涉及到更多的东西，比如自带的 CI 等等。

## 参考

- [代码检视工具Gerrit的日常使用](https://www.jianshu.com/p/b77fd16894b6)
- [常用 Git 命令清单](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)
- [Git 教程](https://www.liaoxuefeng.com/wiki/896043488029600)
