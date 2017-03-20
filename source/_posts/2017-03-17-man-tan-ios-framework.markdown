---
layout: post
title: "漫谈iOS Framework"
date: 2017-03-17 11:21:11 +0800
comments: true
categories: 
---

### 前言

在iOS开发当中，无论是单个APP内，还是多个APP之间，代码的复用总是很常见的，经常需要将代码共享给项目中不同模块或者不同的项目使用。

一种共享代码的方式是直接提供源代码。简单地提供源文件，在版本管理、依赖管理上都存在一些问题，普遍的做法是使用CocoaPods进行依赖管理。如果在共享代码时，不想公开程序实现的细节，那就需要第二种共享代码的方式，使用静态库／Framework，进行代码共享。

### iOS Framework 构成

当一个iOS项目中引入了一个Framework，到底引入了什么？我们需要探究一下Framework的组成。

Framework由三部分组成：

* Mach-O格式二进制文件
* .h头文件
* 资源文件

二进制文件是源文件编译后的.o文件（格式为Mach-O Object）和外部符号，通过链接器（ld）合并而成。

.h文件顾名思义，是Framework对外公开的头文件。

资源文件一般以bundle形式提供，bundle是一个文件夹，里面存放项目所需的所有文件资源，包括图片、音视频、本地化字符串、编译后的nib/storyboard、js脚本等。

Framework分为静态库和动态库，iOS／Mac系统提供了很多的Framework，这些Framework都是以动态库的形式存在的。

{% img center /images/2017/03/WX20170306-153809@2x.png %}

由于App Store审核政策的原因，iOS App中添加Framework，只能添加静态库。

{% img center /images/2017/03/WX20170306-161936@2x.png %}

### iOS Framework 项目结构

Xcode6提供了一个制作Framework的项目模版，替我们省去了很多制作Framework项目的麻烦，在这之前，创建一个Framework的步骤就可以写一片非常长的文章，参考[Ray Wenderlich](https://www.raywenderlich.com/65964/create-a-framework-for-ios)。

首先，我们使用Cocoa Touch Framework模版创建一个项目。

{% img center /images/2017/03/WX20170306-164153@2x.png %}

创建完成之后，我们可以看到这个项目非常简单，只有一个.h文件和一个Info.plist文件，项目文件中有一个Framework Target。

{% img center /images/2017/03/WX20170306-170001@2x.png %}

我们可以编译一下这个Target，在Products中输出了一个FrameworkTest.framework。其中FrameworkTest是二进制文件（包含ARM或x86架构），Headers文件夹存放头文件，Info.plist存放bundleId、版本号等信息 //TODO: Modules文件夹。

{% img center /images/2017/03/WX20170306-171654@2x.png %}

#### 创建一个类

我们新建一个`FrameworkTestViewController`类，并且将FrameworkTestViewController.h的属性设置为public

{% img center /images/2017/03/WX20170306-174015@2x.png %}

#### 添加资源文件

首先，新建一个文件夹，命名为`FrameworkTestResources.bundle`。添加一张图片，并且指定Target为之前创建的Bundle。并且在FrameworkTestViewController中引用它。

```objc
- (void)viewDidLoad {
    [super viewDidLoad];

    NSString* bundleImageName = [NSString stringWithFormat: @"FrameworkTestResources/%@", @"somepic"];
    UIImage *image = [UIImage imageNamed:bundleImageName];

    UIImageView *imageView = [[UIImageView alloc] initWithFrame:CGRectMake(10, 10, 100, 100)];
    imageView.image = image;

    [self.view addSubview:imageView];
}
```

#### 添加多语言支持

将Project的语言设置为简体中文+英文，创建Localizable.strings并加入到FrameworkTestResources.bundle中，记得在Build Phases中的Copy Bundle Resources中取消勾选Localizable.strings（因为在bundle中已经包含）。我们可以通过Bundle读取它。

```objc
NSString *path = [[NSBundle mainBundle] pathForResource:@"FrameworkTestResources.bundle" ofType:nil];
NSBundle *resourceBundle = [NSBundle bundleWithPath:path];
NSString *text = [resourceBundle localizedStringForKey:@"Hello Framework" value:@"" table:nil];

UILabel *label = [[UILabel alloc] initWithFrame:CGRectMake(10, 120, 100, 20)];
label.text = text;

[self.view addSubview:label];
```

在壳项目中，也需要设置好多语言选项。

#### Build Settings

我们还要对项目做一些设置，在Build Settings中找到Mach-O Type，将其设为Static Library。

### iOS Framework Demo壳项目

现在我们的Framework已经见到雏形了，但是还没有办法直观的看到代码运行的结果。我们需要一个可执行的项目来引用这个Framework。

首先创建一个WorkSpace，将FrameworkTest项目添加进来。

{% img center /images/2017/03/WX20170307-111929@2x.png %}

然后在这个WorkSpace中，再创建一个Single View Application项目。完成后项目结构如下：

{% img center /images/2017/03/WX20170307-112638@2x.png %}

#### 壳项目对Framework在开发环境中的引用

我们发现Demo项目中还无法引入Framework的头文件。这时我们可以把WorkSpace的目录添加到Demo项目的Header Search Paths，这样我们就可以通过`#import <FrameworkTest/FrameworkTest.h>`来引用了。

在Link Binary With Libraries中添加FrameworkTest.framework，将FrameworkTestResources.bundle的引用添加到Demo项目中，在Project中开启多语言，我们的壳项目设置就完成了。

#### 第三方库

首先明确一点，Framework不应该直接把第三方库的代码编译进来，如果编译进来，会导致与集成方项目中的第三方库的符号冲突和版本冲突。我们应该以声明的方式引用第三方库。

具体的做法（以CocoaPods管理为例）：

1. 使用CocoaPods管理Demo项目，添加第三方依赖。
2. 在Framework项目的Header Search Paths中添加第三方库头文件的路径。
3. 记录所依赖的第三方库名称及版本号。

### iOS Framework Test

好的单元测试可以帮助我们在开发阶段发现bug，保证代码质量。SDK的项目规模相对较小，单元测试的效果也更加明显。在SDK项目中添加单元测试的方法与其他项目一致。

### iOS Framework的编译过程

当我们在Xcode中使用CMD+B编译Framework时，实际也是调用的xcodebuild工具进行编译的。我们可以在命令行中执行`xcodebuild -usage`打印这个工具的帮助。

我们可以编写脚本，使用xcodebuild工具进行自动化打包，生成可以对外发布的Framework。

```python
commandStriPhone = 'xcodebuild -target '+Scheme+' ONLY_ACTIVE_ARCH=NO -configuration Release -sdk iphoneos BUILD_DIR="' + BUILD_DIR + '"'

commsndStrSimulator = 'xcodebuild -target '+Scheme+' ONLY_ACTIVE_ARCH=NO -configuration Release -sdk iphonesimulator BUILD_DIR="'+BUILD_DIR+'"'

os.system(commandStriPhone)
os.system(commsndStrSimulator)
```

编译完成之后，我们得到了两个Framework，对可执行文件执行`file`命令，我们可以看到这两个可执行文件的CPU架构：其中一个是ARM架构(armv7+arm64，在iPhone/iPad中使用)，另一个是x86架构(i386+x86_64，在模拟器中使用)。

我们可以用`Lipo`命令，将这两个可执行文件合并成一个。

```python
lipoStr = 'lipo -create -output "'+BUILD_DIR+Scheme+'" "'+BUILD_DIR+'Release-iphoneos/'+Scheme+'.framework/'+Scheme+'" "'+BUILD_DIR+'Release-iphonesimulator/'+Scheme+'.framework/'+Scheme+'"'

os.system(lipoStr)
```

合并之后，可执行文件的体积明显增大了。这是否会影响到集成Framework项目的可执行文件体积呢？这点可以不用担心。在集成方项目编译时，会根据valid architectures自动选择合适的CPU架构。

### 版本控制

#### Semver

遵循[Semver](http://semver.org/)语义化版本号，以展示各版本的兼容情况。

> 版本格式：主版本号.次版本号.修订号，版本号递增规则如下：
>
> 1. 主版本号：当你做了不兼容的 API 修改，
> 
> 2. 次版本号：当你做了向下兼容的功能性新增，
> 
> 3. 修订号：当你做了向下兼容的问题修正。
> 
> 先行版本号及版本编译信息可以加到“主版本号.次版本号.修订号”的后面，作为延伸。

#### Beta

在开发过程中，难免会有bug出现，而Framework集成方众多，bug更是难以避免，所以我们应该提供Beta版本，这个版本可能会频繁更新，稳定性较stable版本略低。集成方在测试阶段尽可能的使用beta版本，发现bug后及时进行修复，而在发布时可以使用完全兼容的、不带beta后缀的稳定版本。

### CocoaPods

使用CocoaPods工具可以更方便的发布，这篇文章中就不赘述了，详情可以移步[如何创建Cocoapods的私有Spec](http://epingwang.me/blog/2015/05/11/ru-he-chuang-jian-cocoapodsde-si-you-spec/)

