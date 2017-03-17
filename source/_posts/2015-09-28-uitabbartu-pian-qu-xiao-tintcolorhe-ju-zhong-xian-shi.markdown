---
layout: post
title: "UITabBar图片取消TintColor和居中显示"
date: 2015-09-28 10:58:43 +0800
comments: true
categories: 
---

### 选中的图片取消TintColor

```
for (UITabBarItem *item in tabBarController.tabBar.items) {
	UIImage *image = item.selectedImage;
	
	UIImage *correctImage =[image imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];
	
	item.selectedImage = correctImage;
}
```

### 图片居中显示

```
for (UITabBarItem *item in tabBarController.tabBar.items) {
	item.imageInsets = UIEdgeInsetsMake(6, 0, -6, 0);
}
```