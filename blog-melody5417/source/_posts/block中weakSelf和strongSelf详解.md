---
title: block中weakSelf和strongSelf详解
date: 2017-04-11 21:12:52
tags: [block, objective-c]
---
最近在看SDWebImage的源码，其中对block中循环引用问题的处理值得学习。之前的项目也遇到过循环引用的问题，遂有必要研究总结。

## 当block直接或间接的被self持有时，需用weakSelf

```
dispatch_block_t completionBlock = ^{ 
// self未持有block，可直接用self 
NSLog(@"%@", self); 
} 
MyViewController *myController = [[MyViewController alloc] init...]; 
[self presentViewController:myController animated:YES completion:completionHandler];
```

```
__weak typeof(self) weakSelf = self;
self.completionHandler = ^{ 
// self 持有 block，直接用 self 會造成 retain cycle 
NSLog(@"%@", weakSelf);
} 
MyViewController *myController = [[MyViewController alloc] init...]; 
[self presentViewController:myController animated:YES completion:self.completionHandler];
```

## block中需多次访问self，需用strongSelf

只使用 weak 的问题在于，如果在block中必须多次使用到weakSelf会有危险。因为在多次执行下，weakSelf有可能变为nil。所以需要使用strongSelf，确保 self 的有效性。

```
__weak typeof(self) weakSelf = self; 
dispatch_block_t block = ^{ 
[weakSelf doSomething]; // weakSelf != nil 
// preemption, weakSelf turned nil 
[weakSelf doSomethingElse]; // weakSelf == nil 
};
```

```
__weak typeof(self) weakSelf = self;
myObj.myBlock = ^{ 
__strong typeof(weakSelf) strongSelf = weakSelf; 
if (strongSelf) { 
[strongSelf doSomething]; // strongSelf != nil 
// preemption, strongSelf still not nil 
[strongSelf doSomethingElse]; // strongSelf != nil 
} else { 
// Probably nothing... return; 
} 
};
```

