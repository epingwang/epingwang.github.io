---
layout: post
title: "如何优雅的本地化xib/storyboard"
date: 2016-05-03 15:19:52 +0800
comments: true
categories: 
---

> 最近需要做一个项目的国际化（本地化），项目中用到了大量的xib，但是官方指南的方法不是很好用。经过一番研究，想到一个比较优雅的方法，分享给大家。

现有方法的问题
---

我们都知道，代码中的String可以用`NSLocalizedString()`，从`Localizable.strings`取得本地化的字符串。然而在xib/storyboard中，我们需要给每个xib添加strings文件。这样翻译会分散在项目各处，产生很多重复的字符串，维护起来非常麻烦。而且这样做会过度依赖系统的语言环境，难以定制一些奇怪的功能（比如某些情况，在中文环境中也显示英文）。

Localizable UI
---

在xib中，带默认文字、需要Localization的控件主要就是以下几个：

* UILabel
* UIButton
* UITextField
* UIBarButtonItem
* UISearchBar

我们以常见的`UILabel `/`UIButton `/`UITextField `为例。

#### 创建Localizable Subclass

```objc
@interface LTLSLocalizableLabel : UILabel

@end

@implementation LTLSLocalizableLabel

- (void)awakeFromNib {
    if (self.text.length) {
        self.text = LTLSLocalizedString(self.text, self.text);
    }
}

@end

```

```objc
@interface LTLSLocalizableTextField : UITextField

@end

@implementation LTLSLocalizableTextField

- (void)awakeFromNib {
    if (self.placeholder.length) {
        self.placeholder = LTLSLocalizedString(self.placeholder, self.placeholder);
    }
}

@end
```

```objc
@interface LTLSLocalizableButton : UIButton

@end

@implementation LTLSLocalizableButton

- (void)awakeFromNib {
    NSArray *stateArray = @[@(UIControlStateNormal), @(UIControlStateSelected), @(UIControlStateHighlighted), @(UIControlStateDisabled)];
    for (NSNumber *stateNum in stateArray) {
        UIControlState state = [stateNum unsignedIntegerValue];
        NSString *title = [self titleForState:state];
        if (title.length) {
            [self setTitle:LTLSLocalizedString(title, title) forState:state];
        }
    }
}

@end
```

然后将xib中相应的控件的Class替换为LocalizableClass即可

{% img center /images/2016/05/LocalizableClass.png %}