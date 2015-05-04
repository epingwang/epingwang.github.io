---
layout: post
title: "一些有意思的问题"
date: 2015-05-04 11:02:14 +0800
comments: true
categories: 
---

> 前几天一个朋友问了一些蛮有意思的问题，在这里总结一下

### @property 中 NSString 的属性为什么要用 copy
---

NSString 是 NSMutableString 的基类，在赋值时，对这两个情况要分开讨论：

#### 当传入 NSString 对象时

```
2015-05-04 11:09:04.270 StringTest[540:218144] content = 0x10010e4a8
2015-05-04 11:09:04.271 StringTest[540:218144] self.content = 0x10010e4a8
```
赋值操作直接把传入的 NSString 的内存地址赋给 self.content

#### 当传入 NSMutableString 对象时

```
2015-05-04 11:09:04.271 StringTest[540:218144] mutable content = 0x17406ec40
2015-05-04 11:09:04.271 StringTest[540:218144] self.content = 0x17403b4a0
```
赋值操作创建了一个新的 NSString 并赋值给 self.content

#### 这样做的意义：

NSString 是不可变对象，用 copy 属性可以保证传入 NSMutableString 后，其引用的值始终不变。

### ViewController 中的一些调用问题
---

#### loadView 在什么时候调用

```objc
@property(nonatomic,retain) UIView *view; // The getter first invokes [self loadView] if the view hasn't been set yet. Subclasses must call super if they override the setter or getter.
- (void)loadView; // This is where subclasses should create their custom view hierarchy if they aren't using a nib. Should never be called directly.
```

这里注释解释的比较清楚，在调用`someViewController.view`时，如果`view == nil`，则首先调用`[self loadView]`

那么可以推断出，`[self viewDidLoad]`的调用是在`[self loadView]`之后。

#### viewDidLoad 可以调用几次

最开始想当然的认为只能调用一次。

实际上在iOS6之前，当 Application 收到 Memory warning 之后，会将 hierarchy 中不在前台显示的 viewController.view 设为 nil，并调用`- (void)didReceiveMemoryWarning`, `- (void)viewWillUnload;`, `- (void)viewDidUnload`方法。

在iOS6之后，默认不会卸载view，但是也可以在`- (void)didReceiveMemoryWarning`中手动卸载掉。

当被卸载的view进入屏幕时，会重新加载，并调用`[self viewDidLoad]`。
