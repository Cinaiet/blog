---
title: git常用命令
date: 2018-12-06 14:42:55
categories: 千百度
tags: 花满楼
---

#### 从远端服务器拉取分支到本地
```
$ git checkout -b <branchName> origin/<branchName>
```
#### 删除本地分支
```
$ git branch -D <branchName>
```
<!-- more -->

#### 删除远端分支
```
$ git push origin --delete <branchName>
```

#### 丢弃当前工作区里修改的内容
```
$ git checkout --<branch> // -- 必须有，不然就是切换分支命令
$ git checkout . // 为丢弃当前所有修改的内容
```
#### 在不更改本地代码的情况下代码回退一个版本
```
$  git reset --soft HEAD~1       // 在不更改本地代码的前提(soft)下代码回退一个版本 HEAD~100 就是回退100个版本
$ git push --force               // 强制覆盖掉远程上一个版本
```
#### 将本地分支代码指定push到远端某一个分支
```
$  git push origin <branchName>:<branchName>
```
#### stash
在切换分支前当前分支代码已经做出改动，需要先将代码暂存才能切换分支进行操作。
  ##### 暂存
  ```
  $ git stash
  ```
  ##### 查看当前缓存区列表
  ```
  $ git stash list 
  ```
  ##### 还原当前分支缓存区内容
  ```
  $ git stash pop stash@{0} // stash@{0} 为list里的第一个内容
  ```
  ##### 删除本地暂存内容
  ```
  $ git stash drop stash@{0}  // stash@{0} 为list里的第一个内容
  ```
#### 重返未来。
记录每一次的命令，用于确定要回到未来的哪个版本
```
$ git reflog
```
#### 回退到指定版本
```
$ git reset --hard HEAD~1 // HEAD~1表示回退一个版本
```
#### 代码合并
比方说现在有个任务分支dev，并且已经提到了远端，现想合并到master分支
```
$ git checkout master
$ git merge origin/dev 
```
或者
```
$ git checkout master 
$ git pull origin/dev
```
#### 合并指定的某一次提交到功能分支
```
$ git checkout dev // 切换到想合并的分支
$ git log // 查看提交记录
$ git checkout master // 切换到功能分支
$ git cherry-pick xxx // xxx 为某一次的提交记录的commit-id
```
#### 以上
