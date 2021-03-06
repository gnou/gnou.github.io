---
layout: post
title:  "Block"
date:   2014-09-29
categories: block
---

block由C语言实现，所以在所有以C为基础的语言中都可以使用，如Objective-C/C++

#### Format

```objective_c
<returntype> (^blockname)(list of arguments) = ^(arguments){body;};
```

> Xcode右侧的Code snippet lib中的*"C Inline Blocks as Variables"*(completionShortCut: *inlineBlock*)


例如：

```objective_c
int (^square_block)(int number) = ^(int number){return (number*number);};
int result = square_block(5);
printf("Result = %d\n", result);
```

#### Avoid Strong Reference Cycles when Capturing self
Blocks会对所有捕获到的对象保持strong引用，包括self，很容易造成*strong reference cycle*，例如：

```objective_c
@interface XYZBlockKeeper : NSObject
// 定义一个block property, "copy"在这里只是为了让我们自己意识到block会被copy
// ARC中，即使不指定，编译器也会帮我们自动"copy"，但写上才是好的编程习惯
@property (copy) void (^block)(void);
@end

@implementation XYZBlockKeeper
- (void)configureBlock {
    self.block = ^{
        [self doSomething]; // capturing a strong reference to self,
                            // will end up with strong reference cycle
    }
}
...
@end
```
为了避免这种问题，应该在block中使用weak引用，如下：

```objective_c
- (void)configureBlock {
    XYZBlockKeeper *__weak weakSelf = self;
    self.block = ^{
        [weakSelf doSomething];
    }
}
```
如果这个object已经被销毁，也就是self被销毁了，block中的weakSelf会被指向nil，正常结束。错误用法会导致self永远不能被销毁。

为了防止self半路上直接变nil，上面的代码又发展成了这样：

```objective_c
- (void)configureBlock {
    XYZBlockKeeper *__weak weakSelf = self;
    self.block = ^{
        XYZBlockKeeper *__strong strongSelf = weakSelf;
        [strongSelf doSomething];
    }
}
```

为了让代码更简洁，有大神给出了更好的方法：

1. install [**libextobjc**](https://github.com/jspahrsummers/libextobjc)  (如果在用ReactiveCocoa，里面集成了这个lib，*ReactiveCocoa/RACEXTScope.h*)
2. `#import <EXTScope.h>`  (or `#import <ReactiveCocoa/RACEXTScope.h`)

3. 然后这样：

	```objective_c
	- (void)configureBlock {
    	@weakify(self)
    	self.block = ^{
        	@strongify(self)
        	[self doSomething];
    	}
	}
	```
Done! 简洁、易读、易懂！
