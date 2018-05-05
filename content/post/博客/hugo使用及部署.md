---
title: "Hugo使用及部署"
date: 2018-05-05T22:02:50+08:00
draft: false
tags: 
- hugo
- Travis CI
categories:
- 博客搭建
---

# 参考

* [Hugo借助Travis CI实现GitHub Pages自动部署实践](https://miaocode.tk/hello-hugo-and-more/)
* [hugo官方文档](https://gohugo.io/documentation/)
* [Hexo遇上Travis-CI：可能是最通俗易懂的自动发布博客图文教程](https://juejin.im/post/5a1fa30c6fb9a045263b5d2a)

# hugo安装及使用

## 目录结构

为了便携化,特将所需的文件放入一个单独的目录中并设置环境变量,基本目录结构如下:

    hugo\
    |--bin\   # hugo执行文件目录
    |--blog\  # 博客文件目录
    |--cmder\ # cmder软件目录
    |--blog-cmd.bat #便携化使用脚本

## hugo安装

从[官方网站](https://github.com/gohugoio/hugo/releases)下载windows下的可执行文件放入bin目录下。

## 便携化

我使用了两种使携化的方法，最简单的方法是使用bat脚本开起一个命令行。

``` bash
@echo off
set currentdir=%~dp0
set bin=%currentdir%bin
set site=%currentdir%blog
set path=%path%;%bin%;%site%
cd .\blog
start /b cmd
exit
```

由于使用windows命令行时运行`hugo sever`按`ctl-C`无法退出，我添加了[cmder](http://cmder.net/)作为命令行工具。

在"cmder\config\user-profile.cmd"中添用如下命令以实现便携化，以后直接运行cmder.exe即可。

``` bash
set HUGO_ROOT=%CMDER_ROOT%\..
set HUGO_BIN_ROOT=%HUGO_ROOT%\bin
set HUGO_SITE_ROOT=%HUGO_ROOT%\blog
set "PATH=%HUGO_BIN_ROOT%;%HUGO_SITE_ROOT%;%PATH%"
cd /D %HUGO_SITE_ROOT%
```

## hugo使用

1. 打开命令行后会切换到目录"hugo\blog"下，使用`hugo new site .\`生成站点。
2. 运行`git init`初始化git仓库。
3. 运行`git checkout --orphan source`建立分支（该分支使用参灵--orphan表示无父节点，保持结构独立）。
4. 运行`git submodule add https://github.com/olOwOlo/hugo-theme-even themes/even`添加主题。
5. 复制“hugo/blog/themes/even/exampleSite“目录下的文件到”hugo/blog“目录下。
6. 运行`hugo new post/my-first-post.md`添加自已的文章。
7. 运条`hugo server -D`来查看生成的站点。


# Travis CI自动部署

1. 在github上生成token。
2. 将token添加到travic CI的环境变量中。
3. 在“hugo/blog”目录下添加“.travix.yml”文件并作适应性修改。
4. 推送仓库到github。

```yml
language: go

go:
  - 1.7.3

branches:
  only:
    - source

env:
 global:
   - GH_REF: github.com/xiayu0813/xiayu0813.github.io.git


sudo: required

git:
  submodules: false

# Use sed to replace the SSH URL with the public URL, then initialize submodules
before_install:
  - sed -i 's/git@github.com:/https:\/\/github.com\//' .gitmodules
  - git submodule update --init --recursive

install:
  - export HUGO_VERSION=0.40.2
#  - sudo pip install Pygments
  - wget https://github.com/spf13/hugo/releases/download/v$HUGO_VERSION/hugo_${HUGO_VERSION}_Linux-64bit.tar.gz
  - tar xzf hugo_${HUGO_VERSION}_Linux-64bit.tar.gz
  - chmod a+x hugo

script:
  - ./hugo

after_script:
  - cd ./public
  - git init
  - git config user.name "xiayu0813"
  - git config user.email "xia_yu0813@126.com"
  - git add .
  - git commit -m "Update docs"
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master
```


``` bash
git add .
git commit
git remote add https://github.com/xiayu0813/xiayu0813.github.io.git
git push
```

# 备注

* 自定义域名位于"blog/static/CNAME"文件
* 图片位于“Hugo/blog/static/images”文件夹