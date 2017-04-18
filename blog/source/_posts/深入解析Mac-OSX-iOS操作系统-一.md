---
title: 深入解析Mac-OSX-iOS操作系统(一)
date: 2017-04-04 15:07:30
tags: [Mac, iOS, 操作系统]
---

## 第一章 达尔文主义：OS X的进化史
### iOS和OS X对比
* iOS内核和二进制文件编译的目标架构是基于ARM的架构，而不是x86。
* iOS的内核源码依然闭源。
* iOS内核的编译稍有不同，关注的是嵌入式特性和一些新的API。
* iOS的系统GUI是SpringBoard，触屏应用加载器；而OS X中的GUI是Aqua，鼠标驱动，为窗口系统设计，Lion中以LaunchPad的形式移植了SpringBoard。
* iOS的内存管理更为严格。
* 系统的限制更严格。

## 第二章 合众为一：OS X和iOS的架构
### 2.1 OS X架构概述
* 用户体验层
* 应用框架层
* 核心框架层
* Darwin


### 2.2 用户体验层
提供用户界面
OS X用户体验层中的组件：

* Aqua
* Quick Look
* Spotlight
* Accessibility


iOS的架构在用户体验层之下基本一致，但是在用户体验层很大区别。SpringBoard完全负责所有的用户界面的任务。

#### Aqua
Aqua是OS X的GUI。主要介绍Aqua任何支持GUI的相关特性。
系统的第一个用户态进程launchd负责启动GUI。支持GUI工作的主进程是WindowServer，且WindowServer是Core Graphics框架的一部分，而Core Graphics位于另一个框架Application Services中。因此，WindowServer的路径是：

```
/System/Libary/Frameworks/ApplicationServices.framework/Frameworks/CoreGraphic.framework/Resources/WindowServer
```
WindowServer启动时传入 -daemon 开关参数。WindowServer的代码实际上不完成任何实际的工作，所有的工作都是CoreGraphics框架中的CGXServer函数完成的。

CGXServer会检查自己是否以后台服务程序或者以主控制台getty的方式运行。然后在后台fork出自己的子进程。当子进程准备好之后，LoginWindow进程（同样由launchd启动的）启动交互式登录过程。
#### QuickLook
QuickLook允许在Finder中快速预览多种不同类型的文件，不需要双击鼠标打开文件，只需要按下空格即可。

QuickLook采用的是可扩展的架构，使得大部分工作是由插件完成。这些插件是后缀为 .qlgenerator 的bundle，只需要将这些bundle文件拖放到QuickLook目录（系统范围的QuickLook目录为／system／Library／QuickLook，针对个人的胃～／Libaray／QuickLook）中即可完成插件的安装。

插件不是独立可执行程序，没有传统的main()入口点，而是实现了QuickLookGeneratorPluginFactory入口点。另外有一个配置文件 负责将插件和对应的文件类型关联起来。文件类型通过UIT表示，即逆DNS表示法。

quicklookd是系统的"QuickLook服务器"，是通过以下文件

```
/System/Library/LaunchAgents/com.apple.quicklook.plist 

```
在登录时启动的。这个后台服务程序本身在QuickLook框架中，而且不带GUI。

qlmanage命令的作用是维护插件，并且控制后台服务程序。

#### Spotlight
spotlight背后的核心是一个索引服务器mds，mds在MetaData框架中，而这个框架是系统核心服务的一部分

```
／System／Library/CoreServices.frmework/Frameworks/Metadata.framework/Suppot/mds
```

mds是一个没有GUI的后台服务程序。每当有任何文件操作（创建／修改／删除）发生时，内核会发送通知給mds，这个通知机制称为fsevents。

当mds收到通知时，mds会通过mdworker进程将各种元数据信息导入数据库。mdworker进程可以加载一个具体的Spotlight Importer从文件中提取元数据信息。

导入器路径（系统：/system/Library/Spotlight   用户：/Library/Spotlight），和QuickLook类似，这些importer是实现了固定API的插件。

### 2.3 Darwin-UNIX核心
OS X中的Darwin是一个完全成熟的UNIX实现。

#### Shell
Terminal默认打开的shell是/bin/bash

OS X和iOS中的命令行工具还可以通过telnet和SSH远程访问。OS X和iOS默认禁止，OS X打开方式如下，iOS需要jailbreak。

```
sudo bash
/System/Library/LaunchDaemons/telnet.plist
/System/Library/LaunchDaemons/ssh.plist
key:Disabled ->NO
```

#### 文件系统
OS X和iOS采用的都是HFS+的文件系统，HFS+总是保存大小写的区别。
iOS采用HFSX，默认对大小写敏感。OS X则未采用。

### UNIX的系统目录
OS X是符合UNIX标准的系统，具有标准的UNIX目录结构：

* /bin：UNIX中的二进制程序，常用UNIX命令所在。
* /sbin：系统程序
* ／usr: User目录。第三方的软件安装在这里。目录中包含 bin／sbin／lib。lib用于存放共享的目标文件。
* ／etc：系统配置文件。
* ／dev：BSD设备文件。
* ／tmp：OS X上指向／private／tmp，系统上唯一所有人都有可写权限的目录。
* ／var：OS X上指向／private／var，存储杂项文件，日志，打印队列，邮件存储等。

#### OS X特有的目录
* ／Applications：系统中所有应用程序的默认目录
* ／Developer：所有开发者工具的默认安装位置
* /Libary: 系统应用的数据文件／帮助和文档等数据都放在这个目录。
* ／Network：用于邻居节点发现和访问的虚拟目录。
* ／System：系统文件目录。其中只包含一个Library子目录，几乎包含了系统中的所有重要的组件。
* ／Users：所有用户的主目录所在的目录。每一个用户在这里都会创建一个自己的目录。
* ／Volumes：可移动媒体和网络文件系统的mount点所在目录
* ／Cores：如果启动了core dump，那么合格目录保存core dump文件。第四章详细讨论。

#### iOS文件系统的区别
* HFSX大小写敏感，且文件系统是部分加密的。
// TODO 这里后续有了更多了解再补充

### bundle
每一个bundle都带有相同的目录：

```
Contents/
CodeResources/
Info.plist
MacOS/  包中的二进制文件内容
PkgInfo    包的8字节标识符
Resources/
Version.plist
CodeSignature/
```

### 应用程序和App
OS X对应用程序的处理方式是较为整洁地包装在bundle中，应用程序的bundle包含运行这个应用程序所需要的大部分文件。

在iOS中，app的结构没有这么整洁。iOS会区分苹果提供的默认的应用程序（／Application目录下），越狱情况和通过App store购买的app（／var／mobile／Application目录下）。
通过Store购买的app安装在一个表示128位GUID的目录下。

当iOS app在运行时，会被chroot到自己的应用目录，即名字为GUID的目录，应用程序将自己GUID名字的目录当作根目录，因此／tmp指向的是GUID／tmp。

从store购买应用时，应用以.ipa文件的形式打包，这种文件实际上就是一个.zip文件，这个文件在Payload／目录下压缩了应用程序的目录内容。

#### info.plist
一个标准的info。plist文件包含以下条目：

* CFBundleDevelopmentRegion:默认语言
* CFBundledisplayName:显示給用户的bundle名称
* CFBundleDocumentType：bundle关联的文件类型。是一个字典，value指定了bundle能够处理的文件扩展名。还指定了关联文档显示的图标。
* CFBundleExecutable：实际的可执行文件，位于Contents／MacOS目录。
* CFBundlePackageType：表示一个4字母的代码，例如APPL = Application，FRMW = Framework， BNDL = Bundle。
* CFBundleURLTypes：这个bundle关联的URL。是一个字典，value指定了这个bundle处理的URL scheme以及处理方式。

#### CodeResources
CodeResources实际上是一个指向_CodeSignature/CodeResources的链接。这个文件是一个属性列表，包含bundle中所有其他文件的列表。这个属性列表只有一项files，是字典。key对应文件名，value是Base64格式的散列值。
CodeResources文件可以用与判断一个应用程序是否完好无损，能够防止不小心修改或损坏资源文件。

### 框架
framework bundle和app bundle的不同：内建的版本化机制：框架可以包含多个版本的代码，不同版本并排放在不同的子目录中。

#### 查找框架

* ／System/Libary/Frameworks 包含苹果提供的框架
* /network/libary/frameworks 
* ／Library/Frameworks 保存第三方框架（iOS上为空）
* ~/Library/Frameworks 保存用户提供的框架
* /System/Library/Private/Framworks 私有框架

#### 顶层框架

* Carbon
* Cocoa

Cocoa框架包含了其他3个框架：AppKit，CoreData和Foudation。封装了其他框架的框架通常称为 保护伞框架（umbrella framework）.

// TODO 本章还有些后续内容，更为底层，以后了解更深时过来交作业～



