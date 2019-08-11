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
4. 查看当前分支的最近几次提交：`git relog`

## 撤销

1. 回退版本，重置当前分支的 HEAD 为指定的 commit，同时重置暂存区和工作区，与指定 commit 一致：`git reset --hard <commit>`
2. 将文件在工作区的修改全部撤销，一键还原，回到最近一次 `git commit` 或 `git add` 时的状态：`git checkout -- <file>`
3. 将暂存区的修改回退到工作区：`git reset HEAD <file>`
4. 当手头工作没有完成时，先把工作现场 `git stash` 一下，然后去修复 bug，修复后，再 `git stash pop`，回到工作现场

## 分支

1. 列出所有本地分支：`git branch`
2. 列出所有远程分支：`git branch -r`
3. 列出所有本地分支和远程分支：`git branch -a`
4. 创建 + 切换分支：`git checkout -b <branch>`
5. 切换分支：`git checkout <branch>`
6. 合并某分支到当前分支：`git merge <branch>`
7. 删除分支：`git branch -d <branch>`，强行删除：`git branch -D <branch>`
8. 查看分支合并图：`git log --graph`

当 Git 无法自动合并分支时，必须首先解决冲突，之后再提交，合并完成。解决冲突就是把 Git 合并失败的文件手动编辑为希望的内容，再提交。

合并分支时，如果没有冲突，Git 会用 Fast forward 模式（直接把 master 指向当前分支）。这种模式下，删除分支后，会丢掉分支信息。合并分支时，加上 `--no-ff` 参数就可以用普通模式合并，合并后的历史有分支，能够看出来曾经做过合并，而 Fast forward 合并就看不出来曾经做过合并。

## 代码合并 - rebase

1. 合并多次 commit：将所修改的这次提交与前一次提交合并成一次提交记录，`git rebase -i HEAD~4`，查看最近四次的提交，将 pick 改为 squash，保存退出，再新弹出的窗口中编辑 commit message。
2. 合并分支：`git rebase <branch> `，将当前分支的每个 commit 取消掉，并将其临时保存为 patch，存放在 `.git/rebase` 目录中，然后将当前分支更新为最新的 `<branch>` 分支，最后把保存的这些补丁应用到当前分支上。
3. 解决合并分支的冲突后，用 `git add` 更新索引，无需执行 `git commit` 命令，只需要执行 `git rebase --continue`。
4. 终止 rebase：`git rebase --abort`，当前分支会回到 rebase 开始前的状态。

推荐阅读：[git rebase 使用笔记](https://www.jianshu.com/p/cca69cb695a6)

## 远程同步

1. 关联远程仓库：`git remote add <remote> <url>`，`<remote>` 远程库一般叫做 `origin`
2. 上传本地指定分支到远程仓库：`git push <reomte> <branch>`，一般为 `git push origin master`
3. 查看远程库信息：`git remote`，详细信息：`git remote -v`

## 多人协作工作模式

1. 首先，可以试图用 `git push origin <branch-name>` 推送自己的修改
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用 `git pull` 试图合并
3. 如果合并有冲突，则解决冲突，并在本地提交
4. 没有冲突或者解决掉冲突后，再用 `git push origin <branch-name>` 推送就能成功
