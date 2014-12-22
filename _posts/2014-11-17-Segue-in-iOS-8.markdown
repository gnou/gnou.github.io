---
layout: post
title:  "Segue in iOS 8"
date:   2014-11-17
categories: segue 
---

## Segue种类
iOS 8 中出现了变化，原来的segue都被替换，新的种类如下：

|Name|Description|
|:------|:-------------|
|Show|在master或detail区域展现内容(典型的如iPad的设置界面，左侧是master，右侧是detail)，究竟是在哪个区要取决于屏幕上的内容，如果不分master/detail，就单纯的把新的内容push到当前view controller stack的顶部|
|Show Detail|在detail区域展现内容，如果不分master/detail，新的内容取代当前view controller stack的顶部|
|Present Modally|模态展示内容|
|Present as Popover|在当前的view上出现一个小窗口来展示内容，无处不在的“选中文字后出现 复制/翻译 按钮就是这个|
|Custom|自定义的|

## 两个Views之间的传值
为了从*ViewControllerB*往回传值到*ViewControllerA*，我们需要使用**协议**(**Protocols**)和**代理**(**Delegates**)。
为了实现这个过程，我们需要设置ViewControllerA为ViewControllerB的代理。这样可以使ViewControllerB能够发送消息到ViewControllerA，同样也能使我们将数据回传。
ViewControllerA作为ViewControllerB的代理必须要遵从我们在ViewControllerB中定义的协议(Protocols)，这能够告诉ViewControllerA有哪些方法是必须要实现的。

1. 在ViewControllerB.h中，在#import和@interface之间（就是代码位置），我们像下面这样定义我们的协议及协议方法：

    {% highlight objective-c %}
    #import ...
    ...
    @classViewControllerB;// Important
    @protocol ViewControllerBDelegate <NSObject>
    - (void)addItemViewController:(ViewControllerB *)controller didFinishEnteringItem:(NSString *)item;
    @end
    
    @interface ...
    ...
    {% endhighlight %}

    注：(NSString *)item是我们现在要回传的数据类型，也可以是其他类型，如字典、数组等

2. 仍然是在ViewControllerB.h中，设置一个delegate属性，同时在ViewController.m中synthesize
    {% highlight objective-c %}
    @property (nonatomic, weak) id <ViewControllerBDelegate>delegate;
    {% endhighlight %}
 
3. 在ViewControllerB中，我们在将要从导航控制器中弹出该视图的时候向代理发送消息(消息中含有我们要传递的值)

    {% highlight objective-c %}
    NSString *itemToPassBack = @"Pass this value back to ViewControllerA";
    [self.delegate addItemViewController:self didFinishEnteringItem:itemToPassBack];
    {% endhighlight %}

4. 以上就是所有要在ViewControllerB中进行的操作，接下来就是ViewControllerA的操作。首先我们要在ViewControllerA.h中导入ViewControllerB，并遵从它的协议：

    {% highlight objective-c %}
    #import "ViewControllerB.h"
    @interface ViewControllerA :UIViewController <ViewControllerBDelegate>
    {% endhighlight %}
 
5. 在ViewControllerA.m中实现协议方法：

    {% highlight objective-c %}
    - (void)addItemViewController:(ViewControllerB *)controller didFinishEnteringItem:(NSString *)item
    {
        NSLog(@"This was returned from ViewControllerB %@",item);
    }
    {% endhighlight %}
 
6. 最后，在我们将ViewControllerB压入堆栈之前，我们需要告诉ViewControllerB，ViewControllerA是它的代理(delegate)：

    {% highlight objective-c %}
    ViewControllerB *viewControllerB = [[ViewControllerB alloc] initWithNib:@"ViewControllerB" bundle:nil];
    viewControllerB.delegate = self
    [[self navigationController] pushViewController:viewControllerB animated:YES];
    {% endhighlight %}
演示project:
https://github.com/gnou/Ratings