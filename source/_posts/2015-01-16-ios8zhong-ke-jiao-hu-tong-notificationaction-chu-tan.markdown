---
layout: post
title: "iOS8中可交互通知(NotificationAction)初探"
date: 2015-01-16 15:30:39 +0800
comments: true
categories: 
---

iOS8中，可交互推送/通知可以在**锁屏界面/通知中心/推送消息Banner**上以按钮形式与App交互。

注册可交互推送
------

```objc
UIMutableUserNotificationAction *acceptAction =[[UIMutableUserNotificationAction alloc] init];
// 回调时按钮的IDacceptAction.identifier = @"ACCEPT_IDENTIFIER";
// 按钮title(Facebook使用了emoji)acceptAction.title = @"Accept";
/* UIUserNotificationActivationModeBackground * APP会在后台启动
* UIUserNotificationActivationModeForeground
* APP会在前台启动*/acceptAction.activationMode = UIUserNotificationActivationModeBackground;

// YES为红色，NO为蓝色
acceptAction.destructive = NO;
// 是否需要解锁
acceptAction.authenticationRequired = YES;


UIMutableUserNotificationCategory *category = [[UIMutableUserNotificationCategory alloc] init];

// 消息的ID(区分服务器推送过来时消息的种类)
category.identifier = @"INVITE_CATEGORY";

// Alert形式展现的推送
[category setActions:@[acceptAction] forContext:UIUserNotificationActionContextDefault];
// Banner/锁屏形式展现的推送
[category setActions:@[acceptAction] forContext:UIUserNotificationActionContextMinimal];

UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:
UIUserNotificationTypeAlert | UIUserNotificationTypeBadge | UIUserNotificationTypeSound categories:[NSSet setWithObject:category]];
[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
```

#### Discussion:

* 当Contenxt为`UIUserNotificationActionContextMinimal`时，最多展现两个按钮。
* 当Contenxt为`UIUserNotificationActionContextDefault`时，最多展现四个按钮。
* 当Contenxt为`UIUserNotificationActionContextMinimal`时，第一个按钮在右边，第二个按钮在左边。
* 当Contenxt为`UIUserNotificationActionContextMinimal`时，若两个按钮的`destructive`属性都为NO，则第一个为蓝色，第二个为灰色。
* 当Contenxt为`UIUserNotificationActionContextDefault`时，按钮顺序由上到下排列。

Schedule推送
------

<!--more-->
#### Remote Notifications
```json
{	"aps" : {       "alert" : "You’re invited!",       "category" : "INVITE_CATEGORY",	} }
```

#### Local Notifications

```objc
UILocalNotification *notification = [[UILocalNotification alloc] init]; 
///...notification.category = @"INVITE_CATEGORY";
[[UIApplication sharedApplication] scheduleLocalNotification:notification];
```

推送处理
------

```objc
// 当收到推送时，会调用这两个方法
-(void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forRemoteNotification:(NSDictionary *)userInfo completionHandler:(void (^)())completionHandler;
-(void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler;

// 当执行完事件处理的代码后，调用`completionHandler()`，通知系统处理完成
```

#### Discussion:
* 虽然在WWDC视频中，提到事件处理的时间限定在几秒钟，但是实际上在子线程执行一次网络请求等异步操作也是可以的，甚至连网络超时事件都可以拿到。只需要在网络请求结束后再调用`completionHandler()`即可。（猜测: 几秒钟的限制应该是主线程的执行时间）
* 我是否只能调用静态方法: 经过简单的实验，当App在后台，但进程未被**Terminate**时，是可以读取App内的数据的，但不会调用`applicationDidBecomeActive`。若App进程被**Terminate**后，用户点击按钮后会先加在App，调用`application: didFinishLaunchingWithOptions`。