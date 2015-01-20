---
layout: post
title: "iOS7以上系统中，UITextView输入滚动错位的bug"
date: 2014-12-05 18:20:23 +0800
comments: true
categories: 
---

在iOS7中，`UITextView`多了一个`textContainer`属性用来描述一些Layout。

在用
```objc
UITextView *textView = [[UITextView alloc] init];
```

这个方法时，并没用初始化textContainer，会导致输入中文时页面滚动出现bug。修复这个bug，用以下的方法初始化。

```objc

@implementation SomeTextView

-(id)init
{
    if (IOS7) {
        NSTextStorage* textStorage = [[NSTextStorage alloc] init];
        NSLayoutManager* layoutManager = [NSLayoutManager new];
        [textStorage addLayoutManager:layoutManager];
        NSTextContainer *textContainer = [[NSTextContainer alloc] initWithSize:CGSizeMake(someWidth, someHeight)];
        textContainer.widthTracksTextView = YES;
        textContainer.heightTracksTextView = YES;
        [layoutManager addTextContainer:textContainer];
        if (self = [super initWithFrame:CGRectZero textContainer:textContainer]) {
            
        }
    }
    else {
        if (self = [super init]) {        
            
        }
    }
    
    return self;
}

- (id)initWithFrame:(CGRect)frame
{
    if (IOS7) {
        NSTextStorage* textStorage = [[NSTextStorage alloc] init];
        NSLayoutManager* layoutManager = [NSLayoutManager new];
        [textStorage addLayoutManager:layoutManager];
        NSTextContainer *textContainer = [[NSTextContainer alloc] initWithSize:CGSizeMake(frame.size.width, kDeviceHeight)];
        textContainer.widthTracksTextView = YES;
        textContainer.heightTracksTextView = YES;
        [layoutManager addTextContainer:textContainer];
        if (self = [super initWithFrame:frame textContainer:textContainer]) {
            
        }
    }
    else {
        if (self = [super initWithFrame:frame]) {
            
        }
    }
    
    return self;
}

@end
```