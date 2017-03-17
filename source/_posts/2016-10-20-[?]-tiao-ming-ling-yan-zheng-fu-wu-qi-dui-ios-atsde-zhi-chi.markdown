---
layout: post
title: "一条命令验证服务器对iOS ATS的支持"
date: 2016-10-20 11:45:41 +0800
comments: true
categories: 
---

> 苹果在iOS10中增强了对ATS(App Transport Security)的要求。原先设置的`NSAllowsArbitraryLoads `可能会导致reject。因此需要服务端配置正确的SSL/TLS设置。

在命令行输入

```bash
$ nscurl --ats-diagnostics https://filename.hostname.net --verbose
```

控制台会打印出默认条件以及各种设置条件下，NSURLSession是否可以正常访问。可以根据`ATS Dictionary`调整客户端程序的设置，或者排查服务器设置。

__服务端需要设置的选项：__

* 加密Hash算法（SHA-256级别以上的加密）
* TLS版本（1.2及以上）
* TLS算法（需要提供Forward Secrecy）