---
layout: post
title:  "View Controller Lifecycle"
date:   2014-09-30
categories: viewController
---

#### Summary
![View Controller Lifecycle]({{site.url}}/assets/VCLifecycle.png)

#### `viewDidLoad`
`viewDidLoad`可以说是这些methods中最重要的，之所以很重要，是因为这时候storyboard的对象的**outlets都已经设好了**，但他们还都没有显示到屏幕上，所以就可以进一步操作，比如更改属性，让UILabel显示想要的文字、改变按钮的外观，相当于是更接近目标状态的「初始化」。
但是，view's bounds 还没有确定，这时候程序还不知道这是iPhone 5 还是 iPhone 6，所以geometry(位置)相关的操作不能在这里执行。
在整个view的生命周期里，`viewDidLoad`只会被调用一次。对应的是`viewWillAppera`，它会被多次调用。

#### `viewWillAppear`
`viewWillAppear`会在整个生命周期中被多次调用，所以不要把一次性的代码(比如初始化相关的)放在这里。一个App会有多个MVC，如果这个MVC在后台，而Model中的value改变了，在这个MVC回到前台之前，`viewWillAppear`会被调用，我们最好在这里同步这些改变，这就是`viewWillAppear`最重要的用途。
（事实上，这个时候geometry也已经定下来了，但最好不要在这里进行geometry相关的操作，因为横竖屏改变时这个方法不会被调用）

#### `viewWillLayoutSubviews`
`viewWill/DidLayoutSubviews`才是geometry相关的代码存放的地方，无论view是第一次加载，或是view's bounds 发生改变，这两个方法会被调用。
「自动布局」发生在`viewWillLayoutSubviews`之后，在`veiwDidLayoutSubviews`之前，如果autolayout之后还要需要手动调整，那就放在`viewDidLayoutSubviews`中

#### 实例化与初始化
实例化一个view controller有两种方法，一是从storyboard中解冻，二是使用alloc/init创建。
从storyboard中实例化出来之后，`awakeFromNib`会被调用。而使用alloc/init创建后，`initWithNibName: bundle:`会被调用。他们都在controller被load之前被调用，如果有些代码等不到`viewDidLoad`，最好由他们执行，因为两者执行的内容其实是相同的，所以可以使用下面这个代码模板，把代码放到`setup`中，然后由两个方法调用：

```objective_c
- (void)setup {};   // do something which can't wait until viewDidLoad
- (void)awakeFromNib { [self setup]; }
- (instancetype)initWithNibName: (NSString *)name bundle:(NSBundle *)bundle {
    self = [super initWithNibName:name bundle:bundle];
    if (self) { [self setup]; }
    return self;
}
```
