---
layout: post
title:  "Objective-C Foundation"
date:   2014-08-29
categories: basic objective-c
---

### `+ (NSString *)description`
NSObject method, override这个method会对log输出等非常有帮助，比如，可以把*Card*这个类的*description* method写成 输出Card content，这样以来，使用`NSLong("%@", objCard)`时输出地信息会更有帮助。

### 关于集类
在Objective-C中，集类(NSArray, NSSet, NSDictionary)用于管理对象组，所以添加到集类中的任何项目都必须是Objective-C类的实例，也就是说，你不能把int/float/double/BOOL/enums等添加到里面，必须使用他们对应的Objective-C中的值对象，如NSNumber/NSValue等

### NSArray
不可变有序集合，使用`@[obj1, obj2, ...]`创建
NSArray有很多可用的初始化和工厂方法，如下：
{% highlight objective-c %}
+ (id)arrayWithObject:(id)anObject:
+ (id)arrayWithObjects:(id)firstObject, ...
- (id)initWithObjects:(id)firstObject, ...
{% endhighlight %}
其中，`arrayWithObjects:`和`initWithObjects:`方法都采用了以**nil**结束且数量可变的参数，所以在使用这两种方法的时候，必须以*nil*为最后一个值。例如：
{% highlight objective-c %}
NSArray *someArray = [NSArray arrayWithObjects:someObject, someString, someNumber, someValue, nil];
{% endhighlight %}

不过，下面这种创建array的方法不能加nil:
{% highlight objective-c %}
NSArray *someArray = @[firstObejct, secObject, thirdObject];
{% endhighlight %}

其他有用的方法有：
* - (NSUInteger)count;
    返回obj个数
* - (BOOL)containsObject:(id)anObject
    这个array是否包涵anObject
* - (id)objectAtIndex:(NSUInteger)index;
    返回index处的obj，若index超出count，crash!
* - (id)lastObject;
    返回最后一个obj，若arry为空，返回NULL
* - (id)firstObject;
    返回第一个obj，若arry为空，返回NULL
* - (NSArray *)sortedArrayUsingSelector:(SEL)aSelector;

    根据aSelector提供的方法排序
* - (void)makeObjectsPerformSelector:(SEL)aSelector withObject:(id)selectorArgument;
    Sends a message specified by a given selector to each object in the set.
* - (NSString *)componentsJoinedByString:(NSString *)separator;
    返回一个NSString，这个NSString由array中的各个元素加separator组成

###NSMutableArray
NSArray的子类，拥有NSArray的所有method。
* 创建
    1. `alloc/init`
    2. `+ (id)arrayWithCapacity:(NSUInteger)numItems;`
        这里的提供的数字会对性能有所帮助，**绝非**代表array中的元素数量，因为这是**mutable** array
    3. `+ (id)array`
        `[NSMutableArray array]` == ``[NSMutable alloc] init]`
* `- (void)addObjec:(id)object;`
    在array末尾增加object
* `- (void)insertObject:(id)object atIndex:(NSUinteger)index;`
    在index处插入object
* `- (void)removeObjectAtIndex:(NSUinteger)index`
    移除index处的object

###NSNumber
把int/float/double/BOOL/enums等包装了一下，一般是为了把它放进Arry或Dictionary中，原始类型(int/float/double/BOOL/enums)是不能放进去的。

###NSSet
查找效率很高

###NSDcitionary
`NSDictionary *dic = @{key:value, key:value, ...};`
*key*需要hash/isEqual，所以NSString是最好的选择
* `- (NSUInteger)count;`
* `- (id)objectForKey:(id)key;`
    等同于`dic[key]`

###Property List
A collection of collections

###Type for View


####CGFloat
浮点型，会根据CPU是32/64位而变化


####CGPoint
坐标点，C语言结构体(c struct)类型，包括两个CGFloat：x 和 y.
{% highlight objective-c %}
CGPoint p = CGPointMake(34.5, 22.0);
p.x += 20;  //右移20个单位
{% endhighlight %}


####CGSize
大小，C语言结构体类型，包括两个CGFloat：width 和 height
{% highlight objective-c %}
CGSize s= CGSizeMake(100.0, 200.0);
s.height += 50; //高度增加50
{% endhighlight %}

###CGRect
矩形，C语言结构体，由 CGPoint origin 和 CGSize size 组成
{% highlight objective-c %}
CGRect aRect = CGRectMake(45.0, 75.5, 300, 500);
aRect.size.height += 45;    //高度加45
aRect.origin.x += 30;   //右移30
{% endhighlight %}