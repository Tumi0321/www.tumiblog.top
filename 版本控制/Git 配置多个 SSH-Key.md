---
title: Git 配置多个 SSH-Key
date: 2021-1-8
tags:
  - git
categories:
  - 版本控制
---

[gitee](https://gitee.com/help/articles/4229#article-header0) 上有详细教程

## 多次推送

关联项目

> git remote add gitee 码云项目地址
>
> git remote add github github 项目地址

也可以通过修改配置文件（.git）来关联项目

```
[remote "gitee"]
    url = gitee仓库地址
    fetch = +refs/heads/*:refs/remotes/gitee/*
[remote "github"]
    url = github仓库地址
    fetch = +refs/heads/*:refs/remotes/github/*
```

推送命令

> git push github master
>
> git push gitee master

推送分支

> git push -u gitee branch
>
> git push -u github branch

## 统一推送

将远程仓库添加到已有的 remote 下

> git remote set-url --add origin 项目地址

也可以修改配置文件（.git）

```
[remote "origin"]
    url = github仓库地址
    url = gitee仓库地址
    fetch = +refs/heads/*:refs/remotes/origin/*
```

推送命令

> git push

推送分支

> git push -u origin branch
