---
layout: post
title:  "UIKit Dynamics"
date:   2014-11-18
---


* **UIDynamicAnimator**
    UIKit物理引擎，跟踪各种behaviors。创建时要增加一个参照view
* **UIGravityBehavior**
    模拟重力
* **UICollisionBehavior**
    碰撞效果+边界
* **UIDynamicItemBehavior**
    改变item(这里的主角)的elasticity(弹力)/friction(摩擦力)/density(密度)/resistance(类似摩擦力，但作用于所有直线移动)/angularResistance/allowsRotation(非物理作用，决定是否允许旋转)
* **UIAttachmentBehavior**
    ？？可以把两个itme连接到一起

> animator创建时要指定view作为参照系，各种behaviors创建时要指定要作用的item(比如view)

```Objective-C

@implementation ViewController
#import "ViewController.h"
@interface ViewController () <UICollisionBehaviorDelegate>
@end
@implementation ViewController
{
    UIDynamicAnimator *_animator;
    UIGravityBehavior *_gravity;
    UICollisionBehavior *_collisoin;
    BOOL _firstContact;
}
- (void)viewDidLoad {
    [super viewDidLoad];
    // 创建subview用以演示效果
    UIView *square = [[UIView alloc] initWithFrame:CGRectMake(100, 100, 100, 100)];
    square.backgroundColor = [UIColor grayColor];
   [self.view addSubview:square];
    // 增加障碍物
    UIView* barrier = [[UIView alloc]
                       initWithFrame:CGRectMake(0, 300, 130, 20)];
    barrier.backgroundColor = [UIColor redColor]; [self.view addSubview:barrier];
    // 创建UIDynamicAnimaotr的实例，管理所有behaviors
    _animator = [[UIDynamicAnimator alloc] initWithReferenceView:self.view];
    // 增加重力效果
    _gravity = [[UIGravityBehavior alloc] initWithItems:@[square]];
    [_animator addBehavior:_gravity];
    // 增加边界和碰撞效果
    _collisoin = [[UICollisionBehavior alloc] initWithItems:@[square]];
    _collisoin.collisionDelegate = self;
    _collisoin.translatesReferenceBoundsIntoBoundary = YES;
    // 障碍物的上边界也是碰撞边界
    CGPoint rightEdge = CGPointMake(barrier.frame.origin.x + barrier.frame.size.width, barrier.frame.origin.y);
    [_collisoin addBoundaryWithIdentifier:@"barrier" fromPoint:barrier.frame.origin toPoint:rightEdge];
    [_animator addBehavior:_collisoin];
    // 增加弹力效果
    UIDynamicItemBehavior *itemBehavior = [[UIDynamicItemBehavior alloc] initWithItems:@[square]];
    itemBehavior.elasticity = 2;
    [_animator addBehavior:itemBehavior]; 
}
- (void)collisionBehavior:(UICollisionBehavior *)behavior beganContactForItem:(id<UIDynamicItem>)item withBoundaryIdentifier:(id<NSCopying>)identifier atPoint:(CGPoint)p {
    NSLog(@"Boundary contact occurred - %@", identifier);
    UIView *view = (UIView *)item;
    view.backgroundColor = [UIColor yellowColor];
    [UIView animateWithDuration:0.3 animations:^{
        view.backgroundColor = [UIColor grayColor];
    }];
    
    if (!_firstContact) {
        _firstContact = YES;
        UIView *square = [[UIView alloc] initWithFrame:CGRectMake(30, 0, 100, 100)];
        square.backgroundColor = [UIColor grayColor];
        [self.view addSubview:square];
        [_collisoin addItem:square];
        [_gravity addItem:square];
        UIAttachmentBehavior *attachment = [[UIAttachmentBehavior alloc] initWithItem:view attachedToItem:square];
        [_animator addBehavior:attachment];
    }
}
@end
```
