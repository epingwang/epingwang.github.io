---
layout: post
title: "ScrollsToTop 使用小结"
date: 2014-10-23 17:17:19 +0800
comments: true
categories: 
---

我们经常会看到，很多App中点击StatusBar，tableView就会滚回顶部。

在`UIScrollView`中，`scrollsToTop`这个属性可以实现这个效果。

iOS官方注视中是这样解释的

>
  When the user taps the status bar, the scroll view beneath the touch which is closest to the status bar will be scrolled to top, but only if its `scrollsToTop` property is YES, its delegate does not return NO from `shouldScrollViewScrollToTop`, and it is not already at the top.
>
  On iPhone, we execute this gesture only if there's one on-screen scroll view with `scrollsToTop` == YES. If more than one is found, none will be scrolled.

简单翻译一下:

>
  当用户点击status bar, 靠近status bar的scroll view会滚动到顶端。但只有 `scrollsToTop`属性设为YES，代理中`shouldScrollViewScrollToTop`方法返回YES，并且scroll view没有在顶端时会生效。
  >
  在iPhone中，只有当前屏幕中只有一个scroll view的`scrollsToTop`属性为YES时，该手势才会执行。如果有多个，任何一个scroll view都不会滚动。
  
在实际使用中，我们可能会用到很多UIScrollView的派生类，例如:

* UITableView
* UICollectionView
* UITextView
* UIWebView中也有一个UIScrollView

我写了一个小工具来检测当前View中，包含的UIScrollView

```objc
@implementation UIView (Utility)

-(void)checkConflictScrollViews
{
    for (UIView *subView in self.subviews) {
        [subView checkConflictScrollViews];
        if ([subView isKindOfClass:[UIScrollView class]] && [(UIScrollView *)subView scrollsToTop]) {
            NSLog(@"%@", subView);
        }
    }
}

@end
```
