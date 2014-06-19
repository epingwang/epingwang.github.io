---
layout: post
title: "小试Auto Layout——VFL篇"
date: 2014-06-13 18:47:42 +0800
comments: true
categories: [iOS, AutoLayout, 心得]

---

### 为什么要AutoLayout

从iOS6开始，移动端开始支持AutoLayout。与传统的Layout方法相比，AutoLayout不再使用Origin+Size的方式描述View的位置和大小，而是使用`Constraints`（约束）来描述。

AutoLayout的优点:

* iOS设备越来越多，iPhone4/5/6、iPad、甚至以后的AppleTV、iWatch，其分辨率都是不一样的，要做分辨率适配，旧的方法已经不再适合，AutoLayout提供了一个适配不同分辨率的方式。
* 在iOS8中，View的尺寸可以通过设备旋转、Universal适配、SplitView展开/折叠等途径不断改变，就要求subview的位置和尺寸适应parent view。
* 将View的布局计算交给View自己，而不需要在ViewController中控制，更加符合MVC。

### 为什么要用VFL

VFL，即Visual Format Language，是用code编码进行AutoLayout的方式。

VFL的优点:

* 编写简单，工作量少，代码可读性强，方便维护。
* 由于处理的是字符串，所以符合OC的动态特性。
* 程序员忍受得了去IB拖积木吗？

### VFL语法

<!--more-->

#### 创建一个VFL约束:
``` objc
@interface NSLayoutConstraint
+ (NSArray *)constraintsWithVisualFormat:(NSString *)format options:(NSLayoutFormatOptions)opts metrics:(NSDictionary *)metrics views:(NSDictionary *)views;
@end
```

其中:

* @param format: VFL语句
* @param opts: 约束方式，见后续
* @param metrics: 约束中固定值的字典，Value必须是NSNumber
* @param views: 约束语句中用到的view的字典
* @return 返回一个NSArray，其中Object为`NSLayoutConstraint`类型

其返回值用这个方法接收:

``` objc
@interface UIView (UIConstraintBasedLayoutInstallingConstraints)
- (void)addConstraints:(NSArray *)constraints NS_AVAILABLE_IOS(6_0);
@end
```

#### NSLayoutFormatOptions
在写VFL时，遇到一个问题，我很难描述一个View需要居中的约束。

通过计算左右边界与SuperView边界的距离是可以实现的，但很明显背离了VFL的初衷。

查阅文档后发现这些选项就在`opts`这个参数中，不但可以设置居中，也可以设计约束的参考位置，以及对其方式，可以用位运算选择多个方式。

####一行VFL语句:

```
(<orientation>:)?
(<superview><connection>)?
<view>(<connection><view>)*
(<connection><superview>)?
```
例如:
``` objc
@"V:|-vSpace-[_touchImageView(==avatarHeight)]-vPad-[_nameTextField(==textFieldHeight)]"
```

* orientation:

	```
V: 纵向约束
H: (默认)横向约束
```
* superview: `|`
* view: 

	`[<viewname>(<predicateListWithParens)?]`
* connection: 

	`-padding-`，其中padding可以使matrics字典中的某一项，也可以是一个数值，如`-20-`
* predicateListWithParens: 

	`(<predicate>(,<predicate>)*)`，一组predicate，用逗号隔开
* predicate: 

	`(<relation>)?(<objectOfPredicate>)(@<priority>)?`，例如(>=vPad@100)，意义为宽/高大于vPad（metrics字典中一项），优先级为100

#### 注意事项:
* 一行VFL中，所有被约束的View一定是添加约束View的SubView
* 被约束的View要设置

	```
	[someView setTranslatesAutoresizingMaskIntoConstraints:NO];]
	```
* 使用VFL之后，系统不再调用这个方法

	```
	-(void) setFrame:(CGRect)frame
	```
	
