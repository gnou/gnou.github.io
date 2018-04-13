---
layout: post
title:  "About ReactiveCocoa"
date:   2014-10-16
categories: reactivecocoa rac
---

#### 通俗解释
ReactiveCocoa 中有两个最重要的对象：signals & subscribers。Signals 可以理解成信号塔，真实世界里的信号塔负责把数据发送给他的接收方，这里的接收方就是subscribers。一个signal也可以作为另一个signal的subscriber。

#### 概述
ReactiveCocoa signals 会把「事件流」(stream of events)发送给他们的subscibers。「事件」(event)分三种：
* **next**
* **error**
* **completed**

RACSignal中有很多methods可以subscribe不同的event，每个method使用一个或多个blocks，当这个event发生时，block中的代码会执行。比如，`subscribeNext:`会提供一个block用来处理*next* event。

#### 重点

##### rac_textSignal
ReactiveCocoa框架为标准UIKit controls增加了很多signals，比如*UITextField*中增加的`rac_textSignal`属性，我们可以使用他们来subscribe这些UIKit control的events

```objective_c
...
@property (weak, nonatomic) IBOutlet UITextField *usernameTextField;
...
- (void)viewDidLoad {
    ...
    [self.usernameTextField.rac_textSignal subscribeNext:^(id x) {
        NSLog(@"%@", x);
    }];
}
```

上面的代码中，*usernameTextField*中的text发生变化时，会在终端中打印出当前的text。

##### filter
假定只有username大于3个字符的时候我们才感兴趣，可以使用**filter**来实现
    ```objective_c
    [[self.usernameTextField.rac_textSignal filter:^BOOL(id value) {
        NSString *text = value;
        return [text length] > 3;
    }] subscribeNext:^(id x) {
        NSLog(@"%@", x);
    }];
    ```
我们用上面的代码建立了一个**管道**，如下图。这其实就是「响应式编程」(Reactive Programming)的本质，即：不断的传递和处理数据流
![Fileter pipeline]({{site.url}}/assets/FilterPipeline.png)

注意上面的图，*filter*也是*管道*，它也可以是*RACSignal*，我们可以把他们分离成下面这样：
```objective_c
    RACSignal *usernameSource = self.usernameTextField.rac_textSignal;
    RACSignal *filteredName = [usernameSource filter:^BOOL(id value) {
        NSString *text = value;
        return [text length] > 3;
    }];
    [filteredName subscribeNext:^(id x) {
        NSLog(@"%@", x);
    }];
    ```

##### map
如果我们感兴趣的不是textField中输入的字符，而是字符的个数，可以使用**map**来改变*管道*中的数据流，如下：
```objective_c
    [[[self.usernameTextField.rac_textSignal
     map:^id(NSString *username) {
         return @([username length]);
     }] filter:^BOOL(NSNumber *length) {
         return [length integerValue] > 3;
     }] subscribeNext:^(NSNumber *length) {
         NSLog(@"%@", length);
     }];
     ```
这样一来，只有在usernameTextField中的字符个数大于3的时候，终端中才会打印**字符个数**。
同样是*管道*，这个例子和之前的不同之处在于 管道中的数据发生了变化，如下图：
![Map pipeline]({{site.url}}/assets/FilterAndMapPipeline.png)

##### Combine
把多个信号合并成一个，例子如下：

```objective_c
    RACSignal *signUpActiveSignal = [RACSignal combineLatest:@[validPasswordSignal, validUsernameSignal] reduce:^(NSNumber *passwordValid, NSNumber *usernameValid){
        return @([usernameValid boolValue] && [passwordValid boolValue]);
    }];
    ```

`RACObsever(target, key)` : 创建一个RACSignal，当target.key有新的value时，把这个值发送出去
`-subscribeNext:` : 收到signal时执行，类似的还有`-subscribeCompleted:`/`-subscribeError:`

```objective_c
[RACObserve(self, username) subscribeNext:^(NSString *newName) {
    NSLog(@"%@", newName);
}];
```

`-filter:` : 返回一个RACSignal，只有当block的运行结果为YES时才会把数据发送出去

```objective_c
[[RACObserve(self, username)
    filter:^(NSString *newName) {
        return [newName hasPrefix:@"j"];
    }]
    subscribeNext:^(NSString *newName) {
        NSLog(@"%@", newName);
    }];
```

`RAC()`是一个*宏*，会让binding(结合)看起来好看一些，用于把signal的输出赋给一个对象的属性

```objective_c
// validUsernameSignal是数字形式的布尔值，代表输入的用户名是否通过了验证
    RAC(self.usernameTextField, backgroundColor) = [validUsernameSignal map:^id(NSNumber *usernameValid) {
        return [usernameValid boolValue] ? [UIColor clearColor] : [UIColor yellowColor];
    }];
    ```

> **注意** : 布尔值貌似不能在管道中传递，如果需要布尔值，则使用*NSNumber*形式，然后通过@(bool) / [num boolValue] 进行转换
`+combineLatest:reduce:` : 注意，这是RACSignal的类方法，'combineLatest:'后面是一个RACSignal的array，当array中的任意一个signal变化时，'reduce:'后的block会执行，然后返回一个新的RACSignal把block产生的值送出去。

```
RAC(self, createEnabled) = [RACSignal
    combineLatest:@[ RACObserve(self, password), RACObserve(self, passwordConfirmation) ]
    reduce:^(NSString *password, NSString *passwordConfirm) {
        return @([passwordConfirm isEqualToString:password]);
    }];
```

##### RACCommand ???????
这里的command就是代表actions(通常是UI actions，例如按下button)的signal
`RACCommand` : 创建一个,还可以和与这个action相关的操作绑定
`-rac_command` : 类似于UITextField的`rac_textSignal`，当UI actions发生时发出这个信号

```objective_c
// 按钮被按下时，输出一则信息
self.button.rac_command = [[RACCommand alloc] initWithSignalBlock:^(id _) {
    NSLog(@"button was pressed!");
    return [RACSignal empty];
}];
```

```objective_c
// ????????
// Hooks up a "Log in" button to log in over the network.
//
// This block will be run whenever the login command is executed, starting
// the login process.
self.loginCommand = [[RACCommand alloc] initWithSignalBlock:^(id sender) {
    // The hypothetical -logIn method returns a signal that sends a value when
    // the network request finishes.
    return [client logIn];
}];

// -executionSignals returns a signal that includes the signals returned from
// the above block, one for each time the command is executed.
[self.loginCommand.executionSignals subscribeNext:^(RACSignal *loginSignal) {
    // Log a message whenever we log in successfully.
    [loginSignal subscribeCompleted:^{
        NSLog(@"Logged in successfully!");
    }];
}];

// Executes the login command when the button is pressed.
self.loginButton.rac_command = self.loginCommand;
```
