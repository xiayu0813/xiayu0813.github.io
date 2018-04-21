---
title: git常用命令
tags:
  - git
categories:
  - wiki
  - git
toc: true
date: 2014-08-28 15:35:04
---

```
- git clone <url>   克隆远程版本库
- git init          初始化本地版本库

- git add .         跟踪所有改动过的文件
- git commit -m "commit message"    提交所有更新过的文件

- git branch        显示所有本地分支
- git branch <new-branch> 创建新分支
- git branch -d <branch>  删除本地分支
- git checkout <branch>   切换到指定的分支
- git tag <tagname> 基于最新提交创建标签

- git remote -v     查看远程版本库信息
- git remote show <remote>       查看指定远程版本库信息
- git remote add <remote> <url>  添加远程版本库
- git fetch <remote>    从远程库获取代码
- git pull <remote> <branch>    下载代码及快速合并
- git push <remote> <branch>    上传代码及快速合并
- git push <remote> :<branch/tag-name>  删除远程分支或标签
- git push --tags   上传所有标签
```

[一个成功的分支管理策略](http://www.juvenxu.com/2010/11/28/a-successful-git-branching-model/)
![git分支模型](/images/git-branch-1.png)
