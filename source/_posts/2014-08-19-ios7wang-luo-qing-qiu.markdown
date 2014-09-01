---
layout: post
title: "iOS7网络请求"
date: 2014-08-19 18:20:37 +0800
comments: true
categories: 
---

### NSURLSession

NSURLSession，提供后台链接，当APP关闭时也可以下载/上传

```objc
// 创建
NSURLSessionConfiguration *conf = [NSURLSessionConfiguration backgroundSessionConfiguration:@"identifier"];
NSURLSession *session = [NSURLSession sessionWithConfiguration:conf delegate:self delegateQueue:nil];

// 使用
NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:@"Some URL"]];
    NSURLSessionDownloadTask *task = [session downloadTaskWithRequest:request];
    [task resume];
```

当Session下载/上传完成后，如果APP不在前台，iOS会自动启动APP，并且调用这个方法:

```objc
- (void)application:(UIApplication *)application handleEventsForBackgroundURLSession:(NSString *)identifier completionHandler:(void (^)())completionHandler
```
session的代理会收到回调

```objc
- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didFinishDownloadingToURL:(NSURL *)downloadURL;

其中downloadURL中的Data就是下载完成的Data

```