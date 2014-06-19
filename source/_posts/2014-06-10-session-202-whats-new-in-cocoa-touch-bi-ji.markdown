---
layout: post
title: "Session 202 What's New in Cocoa Touch 笔记"
date: 2014-06-10 15:24:54 +0800
comments: true
categories: [iOS, WWDC]

---


Session 202

Luke Hiesterman


Core idea iOS8 -> Adaptivity

<!--more-->

## Adaptive Layout

Orientations, sizes, and margins

### Adaptive Interface Orientation

Layout 不再关心设备的方向以及设备的类型，而只关心屏幕画布的尺寸

范例: UITraitCollection
成员属性: 

* horizontalSizeClass
* displayScale
* userInterfaceIdiom
* verticalSizeClass

UIViewController Conforms to `UITraitEnviroment`
当屏幕尺寸变化（设备方向改变）时，会调用

``` objc
-(void)traitCollectionDidChange:(UITraitCollection *)previousTraitCollection
{
	// 使用Layout设置CollectionView
    UITraitCollection *currentTraits = self.traitCollection;
    UICollectionViewLayout *newLayout = currentTraits.horizontalSizeClass == UIUserInterfaceSizeClassCompact ? self.squaresLayout : self.rectanglesLayout;
    [self.collectionView setCollectionViewLayout:newLayout animated:YES];
}
```

### Adaptive Margins

UIViewController 的属性:

* bottomLayoutGuide
* topLayoutGuide
* leftLayoutGuide
* rightLayoutGuide

可以确定四个边界（不用计算Navigation、ToolBar的高度）

See More: [Building Adaptive Apps with UIKit](http://epingwang.github.io/blog/2014/06/10/session-216-building-adaptive-apps-with-uikit/)


### Adaptive View Controllers

#### Rotation Deprecations

``` objc
-(void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration;

-(void)willAnimateFirstHalfOfRotationToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration;

-(void)didRotateFromInterfaceOrientation:(UIInterfaceOrientation)fromInterfaceOrientation;

-(BOOL)shouldAutomaticallyForwardRotationMethods;

-(UIInterfaceOrientation)interfaceOrientation;

-(UIView *)rotatingFooterView;

-(UIView *)rotatingHeaderView;
```

取而代之的新方法:

``` objc
- (void)viewWillTransitionToSize:(CGSize)size withTransitionCoordinator:(id <UIViewControllerTransitionCoordinator>)coordinator NS_AVAILABLE_IOS(8_0);
```

### Adaptive View Controller Hierarchies

#### UISplitViewController

Primary - Secondary (TableView - Detail) 视图

现在可以用`Split View Controller - Detail View Controller`

* 全设备支持
* 处理 `primary-secondary` 样式的视图交互
* 增强的可定制性

See more: [View Controller Advancements in iOS8]()

### Adaptive Presentations

适应不同设备的 `弹出视图(Popovers)`、`SearchBar`、`警告(Alerts)`

#### Popovers

iPad上不再需要`UIPopOverController`，`Popover`以后做为UIViewController的Style使用
在iPhone上显示为PresentViewController样式

#### Search Results

`UISearchDisplayController` replaced by

`UISearchController` -> `UIViewController` subclass

* 可以使用UIViewController的一切特性

* 可以定制UISearchController的UI

#### Alerts

`UIAlertController` replaces `UIAlertView` and `UIActionSheet`

UIAlertController 也是 UIViewController 的 subclass

See More: [A Look Inside Presentation Controllers]()

## Testing with the iOS Simulator

Simulator 可以直接调整 Size

* Resizeable iPhone
* Resizeable iPad

## Visual Effects

UIVisualEffectView

可以接收`UIEffect`，来影响View及Subview上的Layer

* UIBlurEffect
* UIVibrancyEffect

## Image Assets

直接返回正确的图片
```
+ (UIImage *)imageNamed:(NSString *)name
               inBundle:(NSBundle *)bundle
compatibleWithTraitCollection:(UITraitCollection *)traitCollection
```

## Condensing Bars

当用户滚动Scroll时，Navigation Bar 变小

See More: [Creating Custom iOS User Interfaces]()

## Self-sizing Table Cells

** cell的 `height` 将无需在 `heightForRowAtIndexPath:` 中实现 **

cell 可以自己计算它的尺寸

``` objc
- (instancetype)initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier
{
    self = [super initWithStyle:style reuseIdentifier:reuseIdentifier];
    if (self) {
        [self.contentView addConstraints:[NSLayoutConstraint
                                          constraintsWithVisualFormat:@"V:|-myTextView-|"
                                          options:0
                                          metrics:nil
                                            views:NSDictionaryOfVariableBindings(_myTextView)]];
        
        [self.contentView addConstraints:[NSLayoutConstraint
                                          constraintsWithVisualFormat:@"H:|-myTextView-|"
                                          options:0
                                          metrics:nil
                                            views:NSDictionaryOfVariableBindings(_myTextView)]];
    }
    return self;
}
```

See More: [What's new in Table and Collection Views]()

## App Extensions

可以写系统增强插件，例如在`Photo`中调用我们写的`Filter软件`或者`Share Extention`

* Photo
* Sharing
* Notification Center Widgets
* Action without UI
* Document providers (向其他应用提供内容的Extension)
* Custom keyboards

See More: [Creating Extensions for iOS and OS X]()

## Notification Updates

* 不需要Show UI的Notification，不再需要用户的Permission
* Notification可以进行用户交互
* 可以进行基于地理位置的Notification
* payload size (256 bytes -> 1k)

See More: [What's New in iOS Notifications]()

## Document Picker

UIDocumentPickerViewController

System UI for selection documents

* Local documents
* iCloud documents
* Third-party document providers

See more: [Building a Document-based App]()

## SDK Modernization

* NS_DESIGNATED_INITIALIZER
* id -> instancetype
* Additional @properties

## Handoff

* 通过Handoff用户可以在多设备之间共享操作
* UIKit 和 AppKit 均支持 Handoff

See More: [Adopting Handoff on iOS and OS X]()

## More Goodies in iOS

## Photos

* Photo Library 的读写权限
* Custom CoreImage Filters

See More: [Introducing the Photos Frameword]()


## CloudKit

* 更多的API，控制iCloud上的数据
* 创建一个 C/S App，无需搭设服务器

See More: [Introducing CloudKit]()

## HealthKit

See More: [Introducing HealthKit]()

## HomeKit

See More: [Introducing HomeKit]()

## Local Authentication
开放的指纹识别系统

* TouchID

See More: [Keychain and Authentication with Touch ID]()

## SceneKit

跨设备的3D渲染——For iOS

## Core Location

* 可以获得 Indoor Location，获得精确的楼层信息
* 更省电
* 当使用时获得用户的授权













