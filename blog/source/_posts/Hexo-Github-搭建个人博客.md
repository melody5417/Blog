---
title: Hexo + Github 搭建个人博客
date: 2017-03-16 21:04:45
tags: [Hexo,Github,博客]
---
# Hexo + Github 搭建个人博客
最近工作遇到了一些挑战，不是来源于技术，但是根源还是自己的能力不能被他人认可。遂下定决心开始有规划，有持续性地总结和记录。第一步就从搭建这个博客开始～

## 准备工作
搭建博客主要分为以下步骤，其中 accessory 是可选的。

* Github
* Hexo
* 配置Hexo
* 发布博客
* Accessory(optional)

### Github
1. 注册Github账号
2. 建立Public Repo，名称为 your-github-name.github.io（your-github-name 为你github的名称）。
3. 配置SSH-Key，本步骤可选。

### Hexo
1. 安装 Node.js 
  直接到[Node.js 官网](https://nodejs.org/en/)下载最新版本即可。 或者通过 Homebrew 安装。
  若想卸载旧版本的Node.js，可运行以下命令。
  
  ```
  $ brew uninstall nodejs #通过brew安装
  $ sudo rm -rf /usr/local/{bin/{node,npm},lib/node_modules/npm,lib/node,share/man/*/node.*} #官网下载pkg安装包
  ```
2. 安装 Hexo

  ```
  $ npm install -g hexo-cli
  ```

### 配置Hexo
本地初始化博客文件夹，例：~/Blog

```
$ cd ~/Blog
$ hexo init
$ npm install
```

修改站点配置文件 ~/Blog/_config.yml， 关联Github信息

```
title: 博客名
subtitle: 博客副标题
description: 
author: 作者
language: zh-Hans
timezone: Asia/Shanghai

deploy:
type: git
repo: https://github.com/your-github-name/your-github-name.github.io.git
branch: master
```
注意：每一项的“:”后面都要保留一个空格，deploy部分的参数前面保留两个空格。

### 发布博客
```
$ hexo server #本地发布
```
[http://localhost:4000/](http://localhost:4000/) 可看到搭建好的博客，确认前面操作是否成功

```
$ npm install hexo-deployer-git --save
$ hexo deploy
```
此时可以在Github上对应的Repo看见本地 ~/Blog/public 文件的内容，打开博客主页 your_github_name.github.io 即可看到搭建的博客的主页啦。


```
$ hexo new "first page test"
$ hexo generate
$ hexo deploy
```
以上命令即可创建一篇博文并提交到Git上，~/Blog/Source下存储每篇博文的 .md 文件。删除该 .md 文件即可删除对应博文。再次运行以下命令即可生成静态网页并发布。

```
$ hexo generate
$ hexo deploy
```
若刷新博客时发现问题，则可运行以下命令清理已生成的静态网页。

```
$ hexo clean
```

### Accessory
Hexo可以选择多种主题，集成评论，数据统计和搜索等第三方功能。推荐一个经典主题：[Next](https://github.com/iissnan/hexo-theme-next)。其配套的相关文档详细介绍了集成第三方服务的步骤。

## Hack On
用这样一句经典的话结束第一篇博文，望自己 Hack On～
