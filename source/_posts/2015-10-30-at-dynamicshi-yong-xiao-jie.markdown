---
layout: post
title: "@dynamic使用小结"
date: 2015-10-30 16:45:33 +0800
comments: true
categories: iOS, objc
---

### @dynamic的作用
@dynamic与@synthesize对应，写在@implementation里。意思是告诉编译器Getter和Setter方法由由开发者实现。

### 使用场景

```objc
@interface ClassA : NSObject

@end

@interface Father : NSObject

@property (nonatomic, strong) ClassA *obj;

@end

@interface ClassB : ClassA

@end

@interface Son : Father

@property (nonatomic, strong) ClassB *obj;

@end

@implementation Son

// 变量obj的getter和setter方法，由父类Father实现
@dynamic obj;

@end
```

或

```objc
@interface ClassA : NSObject

@end

@interface Father : NSObject

@property (nonatomic, strong) ClassA *obj;

@end

@interface ClassB : ClassA

@end

@interface Son : Father

@property (nonatomic, strong) ClassB *obj;

@end

@implementation Father

// 变量obj的getter和setter方法，必须由子类实现，否则会在运行时抛出异常（unrecognized selector）
@dynamic obj;

@end
```