---
title: TensorFlow (一)
date: 2017-03-16 21:57:36
tags: [TensorFlow,Mac]
---
# TensorFlow (一)
深度学习已经🔥的不要不要的了，作为码农，现在还不学习TF真的是要out了。

## clone and install Github 的 TF repo

### clone
[TF repo](https://github.com/tensorflow/tensorflow) 

### install
参考官方的 [install guide](https://www.tensorflow.org/install/) 即可安装成功。
我是通过 virtualenv 安装的，之后每次使用 TF 时都要 activate virtualenv environment。可使用以下两个命令中的任意一个。

```
$ source ~/tensorflow/bin/activate      # bash, sh, ksh, or zsh
$ source ~/tensorflow/bin/activate.csh  # csh or tcsh
```
成功后，终端的 prompt 会变成如下，则可以使用 TF 了。

```
(tensorflow)$
```
当结束TF后，需要 deactivate virtualenv environment。运行以下命令：

```
(tensorflow)$ deactivate
```
