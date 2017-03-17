---
layout: post
title: "UIWebView与Javascript交互"
date: 2015-12-18 17:12:21 +0800
comments: true
categories: 
---

> 现在越来越多的App使用H5页面作为App的一部分，尤其是一些运营的需求，需要快速上线下线，只能通过网页前端完成。那么网页前端与原生App的交互就显得尤为重要。

### 原生对网页的调用

iOS原生App对网页前端的调用非常简单，只需要使用

```objc
- (nullable NSString *)stringByEvaluatingJavaScriptFromString:(NSString *)script;
```

这个方法，调用相应的JavaScript方法、传参就可以了。

例如：

```objc
[webView stringByEvaluatingJavaScriptFromString:@"myFunction();"];
```

### 网页对原生的调用

网页对原生App调用相对复杂一些。在安卓App中，我们可以注册handler来监听某个`JS Function`的调用。

```java
webView.addJavascriptInterface(new Handler(), "SomeObject")
```

在iOS中实现类似的功能，就要动一番脑筋了。

首先我们来看一下`UIWebView`能给我们提供哪些回调方法：

```objc
@protocol UIWebViewDelegate <NSObject>

@optional
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType;
- (void)webViewDidStartLoad:(UIWebView *)webView;
- (void)webViewDidFinishLoad:(UIWebView *)webView;
- (void)webView:(UIWebView *)webView didFailLoadWithError:(nullable NSError *)error;

@end
```

其中，`- (void)webViewDidStartLoad:(UIWebView *)webView;`是在页面开始加载时调用，这个时候我们前端的页面和脚本都没有加载，Pass掉。
`- (void)webViewDidFinishLoad:(UIWebView *)webView;`是在加载完成时调用一次，不满足时序要求，Pass掉。
`- (void)webView:(UIWebView *)webView didFailLoadWithError:(nullable NSError *)error;`这个更不用说，Pass。

那么只有`- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType;`这个方法可以用了。

于是我们的思路确定了：__在webview中注入一个JS Function，调用这个function时进行重定向，利用重定向的URL进行传参。__

#### 编写JS脚本

```javascript
var %@ = {};
%@.%@ = function(cmd, param) {
	if (!param) {
		param = cmd;
		cmd = null;
	}
	var url = "objcjsbridge://" + cmd + "?" + escape(param);
	document.location = url;
};
```

#### 运行JS脚本

需要在每个页面加载完成时运行

```objc

@interface UIWebView (JavaScriptInject)

- (void)injectJavaScriptObject:(NSString *)objectName function:(NSString *)functionName;

@end

@implementation UIWebView (JavaScriptInject)
- (void)injectJavaScriptObject:(NSString *)objectName function:(NSString *)functionName {
    NSString *jsPath = [[NSBundle mainBundle] pathForResource:@"ObjcJSBridge" ofType:@"js"];
    NSError *error = nil;
    NSString *originJs = [[NSString alloc] initWithContentsOfFile:jsPath encoding:NSUTF8StringEncoding error:&error];
    NSString *formattedJs = [NSString stringWithFormat:originJs, objectName, objectName, functionName];
    if (error) {
        NSLog(@"%@", error);
    }
    [self stringByEvaluatingJavaScriptFromString:formattedJs];
}

@end

// 注入JS脚本
- (void)webViewDidFinishLoad:(UIWebView *)webView {
    [super webViewDidFinishLoad:webView];
    [self.webView injectJavaScriptObject:@"LETV_APP_Function" function:@"callBack"];
}


```

#### 监听回调

```objc
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {
    NSURL *url = request.URL;
    if ([url.scheme.lowercaseString isEqualToString:@"objcjsbridge"]) {
        NSString *command = url.host;
        NSString *parameter = url.query;
        
        if ([self.delegate respondsToSelector:@selector(webView:shouldStartLoadWithRequest:navigationType:)]) {
            [self.delegate webviewInjecter:self didRecieveJSCall:command parameter:[parameter stringByReplacingPercentEscapesUsingEncoding:NSUTF8StringEncoding]];
        }
        // 这里return NO，保证不会重定向到这个无法访问的URL
        return NO;
    }
    return YES;
}
```