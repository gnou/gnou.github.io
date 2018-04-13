---
layout: post
title:  "相等 VS 相同"
date:   2014-12-28
categories: equal equality hash
---

在Objective-C中，判断两个object相等或是相同是让很多人困惑的事，涉及到的函数包括`isEqualTo:`/`isEqualTo__ClassName__`/`hash`。  

`isEqualTo__ClassName__:`对两个对象内的值进行对比，如果值相同，即返回TRUE  
`isEqual:`先对比两个对象的指针，如果相同，返回TRUE，否则调用上面的函数对比对象内的值。实际操作中还要加入两个object的class对比，如果两者根本不是同种class，就没有必要进一步进行对比  
`hash`针对object内部的值生成一个hash值，在实际应用中没必要生成特别复杂的数值，只要保证独一无二即可  

如果两个对象相等，其hash值一定相等，但如果两个对象hash值相同，他们不一定相等，他们还可能根本不是同一种class，只是恰好有相同的内部值而已。

实例：  

```objective_c
@interface Person
@property NSString *name;
@property NSDate *birthday;

- (BOOL)isEqualToPerson:(Person *)person;
@end

@implementation Person

- (BOOL)isEqualToPerson:(Person *)person {
  if (!person) {
    return NO;
  }

  BOOL haveEqualNames = (!self.name && !person.name) || [self.name isEqualToString:person.name];
  BOOL haveEqualBirthdays = (!self.birthday && !person.birthday) || [self.birthday isEqualToDate:person.birthday];

  return haveEqualNames && haveEqualBirthdays;
}

#pragma mark - NSObject

- (BOOL)isEqual:(id)object {
  if (self == object) {
    return YES;
  }

  if (![object isKindOfClass:[Person class]]) {
    return NO;
  }

  return [self isEqualToPerson:(Person *)object];
}

- (NSUInteger)hash {
  return [self.name hash] ^ [self.birthday hash];
}

```

参考资料：[Equality](http://nshipster.com/equality/)
