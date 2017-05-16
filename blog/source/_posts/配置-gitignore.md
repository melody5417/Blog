---
title: 配置.gitignore
date: 2017-04-18 20:38:47
tags: [Git, gitignore]
---
## GitHub 官方
GitHub 官方已经准备了适用各种语言和开发环境的配置文件，[链接](https://github.com/github/gitignore)。选择下载对应的语言，再结合自定义就可以配置好用的 .ignore 啦。

## 忽略文件的原则

* 忽略操作系统自动生成的文件，比如缩略图等；
* 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库；
* 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

## .gitignore 文件规范
1. 所有空行或者以注释符号 ＃ 开头的行都会被 Git 忽略。 
2. 可以使用标准的 glob 模式匹配。 
3. 匹配模式最后跟反斜杠（/）说明要忽略的是目录。 
4. 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。

## glob 模式
1. *:任意个任意字符。
2. []:匹配任何一个在方括号中的字符。
3. ?:匹配一个任意字符。
4. [0-9]:匹配字符范围内所有字符。

## 配置 .gitignore
可以在对应的Git目录下，创建.gitignore文件。在忽略项中加入.DS_Store。

* 创建 .gitignore 文件

```
$ touch .gitignore 
```

* 编辑 .gitignore 文件

```
$ .DS_Store
$ */.DS_Store
```

保存即可生效，目前已完成忽略了当前目录的 .DS_Store 以及其子目录的 .DS_Store。但是怎样才能达到全局有效呢，这里就需要全局配置，让忽略文件对多有Git目录都生效，这里就需要用到git config。

## 配置 Git Config
Git Config实际上是一个文件，位于用户根目录，名称是 .gitconfig。

```
$ touch .gitignore_global
// 编辑 .gitignore_global 文件，添加以下代码，引入.gitignore_global文件
[core]  
excludesfile = /Users/reon/.gitignore_global 
```

把从 GitHub 上下载的自定义修改后的 .gitignore 文件 copy 到该路径即可。

## 添加 .gitignore 后不生效怎么办
* you must pay attention to the global gitignore file which sometimes may influence your gitignore.
* When you add something into .gitignore file。First commit your current changes, or you will lose them.

```
$ git rm -r --cached .
$ git add .
$ git commit -m "fixed untracked files"  
```
* When you remove something from .gitignore file.The above steps will not work for you. First commit your current changes, or you will lose them.

```
git add -f "filetype"
git commit -m "Refresh removing filetype from .gitignore file."
// the "filetype" means the file or filetype you want to remove from the .gitignore file. 
// You want to make the filetype be tracked again.
```

