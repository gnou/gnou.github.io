---
layout: post
title:  "How to set tint color in iOS App"
date:   2014-11-28
categories: uiwebview 
---

## AppDelegate.m
```Objective-C
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    [self initAppearence];
    
    return YES;
}

- (void)initAppearence {
	 // 配置Tint Color
    UIColor *myTintColor = [UIColor colorWithRed:76/255.0f green:211/255.0f blue:235/255.0f alpha:1.0f];
    
    [[UITabBar appearance] setTintColor:myTintColor];
    
    [[UINavigationBar appearance] setBarStyle:UIBarStyleDefault];
    [[UINavigationBar appearance] setBarTintColor:myTintColor];
    
    [[UIToolbar appearance] setBarStyle:UIBarStyleDefault];
    [[UIToolbar appearance] setBarTintColor:myTintColor];
}
```