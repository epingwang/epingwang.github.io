---
layout: post
title: "验证你的XCode"
date: 2015-09-23 16:07:07 +0800
comments: true
categories: 
---

苹果官方提供了验证XCode合法性的方法

Terminal中运行

```
spctl --assess --verbose /Applications/Xcode.app
```

几分钟后会得到验证结果，以下为正确验证

```
/Applications/Xcode.app: accepted
source=Mac App Store

/Applications/Xcode.app: accepted
source=Apple

/Applications/Xcode.app: accepted
source=Apple System
```