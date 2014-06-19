---
layout: post
title: "Session 214 View Controller Advancements for iOS8 笔记"
date: 2014-06-11 15:52:27 +0800
comments: true
categories: [iOS, WWDC]

---

Session 214

Bruce D. Nilo

## OverView

* A brief discussion about UIKit's new Adaptive APIs
* New UISplitViewController features
* Some new ways to condense and hide bars
* Presentations and popovers
* New API that uses transition coordinators
* Coordinate spaces

<!--more-->

## Support for Adaptive User Interfaces

Universal App中，相同的页面会展示为不同的Layout

在iOS8之前，Layout取决于

* 设备类型 		Device type
* 设备方向 		Interface Orientation
* Window大小 	Size

iOS8之后:

* Traits and trait collections
* Size

### 什么是 "trait collection"?

Trait collection 是一个trait的集合

例如: 在一个竖屏的 iPhone5s 中

Trait | Value
--- | ---
horizontalSizeClass | Compact
verticalSizeClass | Regular
userInterfaceIdiom | Phone
displayScale | 2.0

### 什么是 "size class"?

* Size class 是简单定义`可用空间`的一个 `trait`
* 可以定义在Horizontal方向和Vertical方向是Compact or Regular

### Trait Environments

在iOS8中, `UIView`和`UIViewController`均 conforms `<UITraitEnvironment>`


``` objc
@protocol UITraitEnvironment <NSObject>
@property (nonatomic, readonly) UITraitCollection *traitCollection;

- (void)traitCollectionDidChange:(UITraitCollection *)previousTraitCollection;
```

并不是每个View都有自己的TraitCollection，当获取`Leaf View`的`traitCollection`时，会沿着View的hierarchy找到最近的traitCollection

## Split View Controller

UISplitViewControllers can now be used on the phone

新增了这个方法:

``` objc
@interface UISplitViewController : UIViewController
@property(nonatomic, readonly, getter=isCollapsed) BOOL collapsed  NS_AVAILABLE_IOS(8_0);
@end
```

#### Expanded split view controller


{% img center /images/2014/06/SplitViewController.jpg %}

#### Collapsed split view controller

{% img center /images/2014/06/SplitViewControllerCollapsed.jpg %}

### 在ViewController中添加SplitViewcontroller，并启用Collapse

``` objc
svc.preferredDisplayMode = UISplitViewControllerDisplayModeAllVisible;
```

### 控制分栏的宽度

通过以下几个属性

``` objc
@property(nonatomic, assign) CGFloat preferredPrimaryColumnWidthFraction NS_AVAILABLE_IOS(8_0);
@property(nonatomic, assign) CGFloat minimumPrimaryColumnWidth NS_AVAILABLE_IOS(8_0);
@property(nonatomic, assign) CGFloat maximumPrimaryColumnWidth NS_AVAILABLE_IOS(8_0);
@property(nonatomic,readonly) CGFloat primaryColumnWidth NS_AVAILABLE_IOS(8_0);
```

## Condesing Bars

现在隐藏NavigationBar和ToolBar变得相当简单，只需要以下几个属性

自动隐藏:

``` objc
@property (nonatomic, readwrite, assign) BOOL condensesBarsOnSwipe;
@property (nonatomic, readwrite, assign) BOOL condensesBarsWhenKeyboardAppears;
@property (nonatomic, readwrite, assign) BOOL hidesBarsWhenVerticallyCompact;
@property (nonatomic, readwrite, assign) BOOL hidesBarsOnTap;
@property (nonatomic, readonly, assign) UITapGestureRecognizer *barHideGestureRecognizer;
```

手动控制:

``` objc
@property (nonatomic, readwrite, assign, getter = isNavigationBarCondensed) BOOL navigationBarCondensed;
```




    

