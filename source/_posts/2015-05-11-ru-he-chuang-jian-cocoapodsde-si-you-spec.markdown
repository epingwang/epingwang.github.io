---
layout: post
title: "如何创建Cocoapods的私有Spec"
date: 2015-05-11 09:56:40 +0800
comments: true
categories: Cocoapods
---

> 很多项目使用 CocoaPods ([http://cocoapods.org/]()) 管理项目依赖，非常方便的引入了大量的第三方开源代码。CocoaPods 项目本身也是开源的，这意味着可以很方便的通过 CocoaPods 管理私有/本地项目。

### 主要分为以下几步

1. 创建一个私有 Spec Repo
2. 创建并初始化适合 CocoaPods 管理的项目
3. 创建并发布该项目的 podspec 描述文件
4. 项目协作，Podfile编写

### 参考资料

* [http://guides.cocoapods.org/making/private-cocoapods.html]()
* [http://guides.cocoapods.org/making/using-pod-lib-create.html]()
]

<!--more-->

Step 1: 创建一个私有 Spec Repo
---

Spec Repo 是存放 CocoaPods 项目描述文件（.podspec）的仓库，其 master branch 由 CocoaPods 维护 ([https://github.com/CocoaPods/Specs]())。

创建私有 Spec Repo，我们不需要 fork master repo，只需要按照以下结构创建这样一个 Git 项目，并上传到 Git

```
.
├── Specs
    └── [SPEC_NAME]
        └── [VERSION]
            └── [SPEC_NAME].podspec
```
上传完成后，执行以下命令，将 Spec Repo 添加到本地。

```
$ pod repo add REPO_NAME SOURCE_URL
```

Step 2: 创建并初始化适合 CocoaPods 管理的项目
---

我们可以手动创建一个开源项目，也可以通过 CocoaPods 的模版创建。

```
$ pod lib create DemoLibrary
```

在 Bash 中执行该命令，创建一个名为 DemoLibrary 的模版，我们可以看到项目结构如下:

{% img center /images/2015/05/20150511-1@2x.png %}

在 Podspec Metadata 文件夹中，提供了非常重要的三个文件:

* .podspec 是该项目的描述文件
* README.md 大家都懂的
* LICENSE 默认提供了一个 MIT 许可协议

我们将需要创建的 Class 放入`./Pod/Classes`中，将资源文件放入`./Pod/Assets`中，将项目 push 到你的 Git Repo。

Step 3: 创建并发布该项目的 podspec 描述文件
---

我们在 Demo 项目中，已经生成了一个`DemoLibrary.podspec`文件。如果没有使用模版生成项目，我们需要使用

```
$ pod spec create [NAME|https://github.com/USER/REPO]
```

命令来创建`.podspec`文件。

打开该文件，根据注释编写自己的 podspec 即可。

在提交之前，我们需要使用

```
$ pod spec lint DemoLibrary.podspec
```
来验证 podspec 的合法性。

如果验证通过，我们可以用

```
$ pod repo push REPO_NAME SPEC_NAME.podspec
```
提交这个 podspec

#### Note:

我们在使用`$ pod spec lint`和`$ pod repo push`命令时，会收到各种 warning 或者 error，导致提交失败。

使用`--allow-warnings`来忽略一些 warning。

Step 4: 项目协作，Podfile编写
---

首先，如果需要你的小伙伴们也使用你的项目，需要他们安装 CocoaPods，并执行

```
$ pod repo add REPO_NAME SOURCE_URL
```

在他们项目的 Podfile 中，加入你的开源项目，例如：

```
platform :ios, ‘8.0’

target 'DemoApp' do

source 'https://github.com/CocoaPods/Specs.git'
pod 'GPUImage', '~> 0.1.6'

source 'git@git.github.com:YourPrivateRepo/specs.git'
pod 'CocoaHelpers', '~> 1.0.0'

end
```

注意: 在 CocoaPods 0.36.0版本之后，需要显示指定 source，否则会出现`Unable to find a specification for XXX`错误。

执行`pod install`，大功告成！
