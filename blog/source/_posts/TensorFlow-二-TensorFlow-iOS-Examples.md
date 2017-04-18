---
title: TensorFlow (二) - TensorFlow iOS Examples
date: 2017-03-19 19:02:48
tags: [TensorFlow,iOS]
---
# TensorFlow (二) - TensorFlow iOS Examples

## Build
编译可参考 TF 的 ios_examples 的 [ReadMe](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/contrib/ios_examples/README.md) 。

* 升级 Xcode 到 7.3 版本及更高
* 安装命令行工具。

```
$ xcode-select --install
```
* 安装 Homebrew, automake, libtool

```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
$ brew install automake
$ brew install libtool
```
* 执行 tensorflow/contrib/makefile 路径下的 build_all_ios.sh 脚本，编译大概一个半小时。

```
$ cd tensorflow/contrib/makefile
$ ./build_all_ios.sh
```
执行成功后在 makefile/gen/lib 下生成 libtensorflow-core.a 库。

## Run Examples
此时还差一步即可运行 TF 提供的Demo：添加数据模型。

执行下面命令，或者可以手动下载 [Inception](https://storage.googleapis.com/download.tensorflow.org/models/inception5h.zip)。然后copy到相应路径下。

```
$ mkdir -p ~/graphs
$ curl -o ~/graphs/inception5h.zip \
https://storage.googleapis.com/download.tensorflow.org/models/inception5h.zip \
&& unzip ~/graphs/inception5h.zip -d ~/graphs/inception5h
$ cp ~/graphs/inception5h/* tensorflow/contrib/ios_examples/benchmark/data/
$ cp ~/graphs/inception5h/* tensorflow/contrib/ios_examples/camera/data/
$ cp ~/graphs/inception5h/* tensorflow/contrib/ios_examples/simple/data/
```
现在可以运行三个实例工程啦～
