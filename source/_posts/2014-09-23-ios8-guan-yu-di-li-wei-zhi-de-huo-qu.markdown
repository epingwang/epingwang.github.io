---
layout: post
title: "iOS8 关于地理位置的获取"
date: 2014-09-23 17:32:39 +0800
comments: true
categories: 
---

### 在iOS8之前
当我们调用

``` objc
CLLocationManager *locationManager = [[CLLocationManager alloc] init];
[locationManager startUpdatingLocation];
```

这个方法时，会自动弹出警告，请求用户通过地理位置权限

### iOS8中

获取地理位置的权限默认为`kCLAuthorizationStatusNotDetermined`，即未确定权限。当调用`startUpdatingLocation`时，不会自动请求获取权限。

Apple文档中的解释

__- (void)requestWhenInUseAuthorization 中: __

> You must call this method or the requestAlwaysAuthorization method prior to using location services. If the user grants “when-in-use” authorization to your app, your app can start most (but not all) location services while it is in the foreground. (Apps cannot use any services that automatically relaunch the app, such as region monitoring or the significant location change service.) When started in the foreground, services continue to run in the background if your app has enabled background location updates in the Capabilities tab of your Xcode project. Attempts to start location services while your app is running in the background will fail. The system displays a location-services indicator in the status bar when your app moves to the background with active location services.

<!--more-->

__简单解释:__

当请求地理位置之前，必须先显式调用`-(void)requestWhenInUseAuthorization`或者`- (void)requestAlwaysAuthorization`方法，当用户通过请求时，前者可以在程序前台运行时获取地理位置，后者则可以在程序后台运行时继续获取位置，并可唤醒程序

#### 其他的事项

* 请求时和在设置中显示的文本:

	在`Info.plist`中修改，Key为`NSLocationWhenInUseUsageDescription`或`NSLocationAlwaysUsageDescription`
	

