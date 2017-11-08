---
layout: post
title: git的使用
category : git
tags : [git, tag1, tag2]
stickie: true
---
## git的使用

#### git常用命令表格

| 行为 | 命令 | 备注 |
| :-- | :-- | :-- |
| 初始化 | [init](#init) | 在本地的当前目录里初始化git仓库 |
|  | [clone 地址](#clone) | 从网络上某个地址拷贝仓库(repository)到本地 |
| 查看当前状态 | [status](#status) | 查看当前仓库的状态。碰到问题不知道怎么办的时候，可以通过看它给出的提示来解决问题 |
| 查看不同 | [diff](#diff) | 查看当前状态和最新的commit之间不同的地方 |
|  | diff 版本号1 版本号2 | 查看两个指定的版本之间不同的地方。这里的版本号指的是commit的hash值 |
| 添加文件 | [add -A](#add) | 这算是相当通用的了。在commit之前要先add |
| 撤回stage的东西 | [checkout -- .](#checkout) | 这里用小数点表示撤回所有修改，在`--`的前后都有空格 |
| 提交 | [commit -m "提交信息"](#commit) | 提交信息最好能体现更改了什么 |
| 删除未tracked | [clean -xf](#clean) | 删除当前目录下所有没有track过的文件。不管它是否是.gitignore文件里面指定的文件夹和文件 |
| 查看提交记录 | [log](#log) | 查看当前版本及之前的commit记录 |
|  | [reflog](#reflog) | HEAD的变更记录 |
| 版本回退 | [reset --hard 版本号](#reset) | 回退到指定版本号的版本，该版本之后的修改都被删除。同时也是通过这个命令回到最新版本。需要reflog配合 |

#### 常用步骤
增删改》add》commit》pull 


# Edit hello.py and main.py
git add hello.py
git commit
 
# Realize you forgot to add the changes from main.py
git add main.py
git commit --amend --no-edit



永久删除git库中的所有大文件或者机密文件
//看.git空间大小
$du -sh .git

[ jonny@wheezy ~ ]
$ du .git -lsh 
126M .

删除匹配*.db的所有文件
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch *.db' --prune-empty --tag-name-filter cat -- --all

git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch *.zip' --prune-empty --tag-name-filter cat -- --all
立刻回收空间
rm -rf .git/refs/original/ 
git reflog expire --expire=now --all
git gc --prune=now
git gc --aggressive --prune=now

[ jonny@wheezy ~ ]
$ rm -rf .git/refs/original/ 
$ git reflog expire --expire=now --all
$ git gc --prune=now
$ git gc --aggressive --prune=now


最后把改动强制推送到远端
git push origin --force --all

//checkout用法1从暂存区取出来，也就是把最后一次git add 的内容取回，回到最近一次git commit或git add时的状态。
git checkout -- first.txt
//checkout用法2创建分支并切换
git checkout -b dev
//checkout用法3切换分支
git checkout dev


//删除文件
rm first.txt
//版本库中删除
git rm first.txt