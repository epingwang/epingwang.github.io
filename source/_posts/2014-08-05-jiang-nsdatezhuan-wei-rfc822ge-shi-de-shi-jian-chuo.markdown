---
layout: post
title: "将NSDate转为RFC822格式的时间戳"
date: 2014-08-05 16:55:43 +0800
comments: true
categories: 
---


HTTP/1.1 Protocol中要求的时间戳格式有以下三个:

      Sun, 06 Nov 1994 08:49:37 GMT  ; RFC 822, updated by RFC 1123
      Sunday, 06-Nov-94 08:49:37 GMT ; RFC 850, obsoleted by RFC 1036
      Sun Nov  6 08:49:37 1994       ; ANSI C's asctime() format
      
其中应用最广泛的是的RFC822格式

转换方法如下:
```objc
	NSDateFormatter *df = [[NSDateFormatter alloc] init];
    df.locale = [NSLocale localeWithLocaleIdentifier:@"en-us"];
    df.timeZone = [NSTimeZone timeZoneWithAbbreviation:@"GMT"];
    df.dateFormat = @"EEE',' dd MMM yyyy HH':'mm':'ss 'GMT'";
    
    NSString *dateString = [df stringFromDate:[NSDate date]];
```