---
layout: post
title: "UITextField的默认Placeholder颜色"
date: 2014-07-10 14:11:44 +0800
comments: true
categories: 
---


### 色值为

```
R:0
G:0
B:0.0980392
A:0.22
```

### 查看方式
```objc

NSRange range = NSMakeRange(0, 1);
NSDictionary *dict = [someTextField.attributedPlaceholder attributesAtIndex:0 effectiveRange:&range];
NSLog(@"%@", [dict objectForKey:NSForegroundColorAttributeName]);

```

