---
layout: post
title: "cocoapods提速手册"
date: 2014-12-24 11:09:14 +0800
comments: true
categories: 
---

转自 [CodingTime](http://www.codingtime.info/post/posts/leng-zhi-shi/2014_07_28_speed_up_cocoapods)

>前言
>
>相信大家在使用cocoapods的时候，都感到在墙内速度怨念把。今天po主我在家更新代码时执行'pod install
居然花了2个小时，各种怒从中来，遂整理了一下cocoapods提速的各种方法，给大家参考。

### 1.cocoapods安装提速

我们在使用'gem install cocoapods'来安装cocoapods时，是不是感觉都奇慢无比？都是gem官方源被墙惹的祸！将gem的默认官方镜像更换成淘宝镜像可以解决这一问题。在命令行中执行

```
gem sources --remove https://rubygems.org/
gem sources -a https://ruby.taobao.org/
gem sources -l
```

若显示

```
*** CURRENT SOURCES ***

https://ruby.taobao.org
```

则淘宝源替换成功。这是再执行gem install cocoapods会发现安装速度明显变快。

```
注意：
更换淘宝gem镜像只是提高cocoapods安装速度和更新速度，不会影响任何pod命令的速度。＝  ＝
```

### 2. 获取specs文件提速

cocoapods的spec文件都放在github中，而国内访问github总有些不顺畅，导致每次更新cocoapods spec文件都时候速度都比较慢。将cocoapods的spec版本库改成国内可以提升spec文件的更新速度。

在命令行中执行

```
pod repo remove master
pod repo add master https://gitcafe.com/akuandev/Specs.git
pod repo update
```

默认的cocoapods master仓库会被替换成国内gitcafe仓库。

### 3. pod install提速

每次执行`pod install`和`pod update`的时候，cocoapods都会默认更新一次spec仓库。这是一个比较耗时的操作。在确认spec版本哭不需要更新时，给这两个命令加一个参数跳过spec版本库更新

```
pod install --verbose --no-repo-update
pod update --verbose --no-repo-update
```

可以明显提高这两个命令的执行速度。

### 4. 终极提速大法

在将gem、cocoapods spec文件都改成国内镜像之后，剩下的还能提速的步骤就是将各个pod的源码也拖到国内了。不过将pod list里5000多个pod全部镜像下来并一一修改spec文件，这工程量完全不是我等小屌丝能够完成的。目前似乎也没有哪个牛逼大厂完成这个壮举。所以所谓的终极提速大法，就是无论干什么最终都得有个好用的梯子。