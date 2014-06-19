---
layout: post
title: "Session 216 Building Adaptive Apps with UIKit"
date: 2014-06-10 17:32:04 +0800
comments: true
categories: [iOS, WWDC]

---

Session 216

Jacob Xiao

使用UIKit 创建 Universial App

<!--more-->

## Summary

* Adaptive Concepts
* View Controllers
* Interface Builder


### Size Classes

之前区分设备方向，使用 `UIInterfaceOrientation`(iPhone) 和 `UIUserInterfaceIdiom`(iPad)

现在用 `Size Classes` 取代

在iPad上: 

* Potrait: Regular Horizontal & Regular Vertical
* Landscape: Regular Horizontal & Regular Vertical

在iPhone上:

* Potrait: Compact Horizontal & Regular Vertical
* Landscape: Compact Hotizontal & Compact Vertical


### UITraitCollection

#### 成员: 

``` objc
@property (nonatomic, readonly) UIUserInterfaceSizeClass horizontalSizeClass;
@property (nonatomic, readonly) UIUserInterfaceSizeClass verticalSizeClass;
@property (nonatomic, readonly) CGFloat displayScale;
@property (nonatomic, readonly) UIUserInterfaceIdiom userInterfaceIdiom;
```

#### Trait Environments

* UIScreen
* UIWindow
* UIViewController
* UIView
* UIPresentationController

所有层级共享同样的`UITraitCollection`


当`TraitCollection`发生改变时，触发

``` objc
-(void)traitCollectionDidChange:(UITraitCollection *)previousTraitCollection;
```
重写这个方法来响应方向变化事件

#### Trait Collections
可以自己创建`UITraitCollection`

可适用于横屏播放器之类的界面

可用于UITraitCollection的比较

``` objc
	UITraitCollection *collection = self.traitCollection;
    UITraitCollection *newCollection = [UITraitCollection traitCollectionWithHorizontalSizeClass:UIUserInterfaceSizeClassCompact];
    [collection containsTraitsInCollection:newCollection];
```

可以合并`UITraitCollection`

``` objc
+ (UITraitCollection *)traitCollectionWithTraitsFromCollections:(NSArray *)traitCollections;
```
其中 `Compact` + `Regular` = `Regular`

### Appearance Proxy

``` objc
+(instancetype)appearanceForTraitCollection:(UITraitCollection *)trait;
```

* Customize views for different traits
* UIImage 可以响应这个方法，返回different version of image
* UIImageAssets

### Adaptive View Controllers

#### Split View Controller

现在Split View Controller 是Universal的

#### 生命周期

Setup开始时:

``` objc
-(void)willTransitionToTraitCollection:(UITraitCollection *)newCollection withTransitionCoordinator:(id<UIViewControllerTransitionCoordinator>)coordinator;
```
在这个方法可以自定义动画，并执行completion block，其顺序在下面方法之后。

Setup结束时:

``` objc
-(void)traitCollectionDidChange:(UITraitCollection *)previousTraitCollection;
```

#### Showing View Controllers

``` objc
[self.navigationController pushViewController:...];
```
这个机制被废弃

取而代之的是

``` objc
- (void)showViewController:(UIViewController *)vc sender:(id)sender;

- (void)showDetailViewController:(UIViewController *)vc sender:(id)sender;
```

这两个方法总是保证，无论当前ViewController被包含在`NavigationController`, `SplitViewController`, 还是没有被包含在任何ViewController中，都会有正确的表现。


