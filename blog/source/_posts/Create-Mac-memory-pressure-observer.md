---
title: Create Mac memory pressure observer
date: 2017-05-16 12:01:52
tags: [iOS,Mac,MemoryPressure]
---
Mac的资料真心少，先来看看iOS是怎么做的。

## memory pressure observer on iOS

### how to observe

* 可以监听 UIApplicationDidReceiveMemoryWarningNotification 通知。

```
[[NSNotificationCenter defaultCenter] addObserver:self
selector:@selector(XXX)
name:UIApplicationDidReceiveMemoryWarningNotification 
object:nil];
```
* 实现 UIViewController 自身的方法didReceiveMemoryWarning，该方法在 App 接收到 memory warning 会被调用，可以在方法中释放当前不再使用的资源。

```
- (void)didReceiveMemoryWarning;
```
### how to mock

* 模拟器菜单
hardware -> simulate memory warning

* 模拟发通知 UISimulatedMemoryWarningNotification

```
CFNotificationCenterPostNotification(CFNotificationCenterGetDarwinNotifyCenter(), (CFStringRef)@"UISimulatedMemoryWarningNotification", NULL, NULL, true);
```
* 私有 API

```
[[UIApplication sharedApplication] performSelector:@selector(_performMemoryWarning)];
```

## memory pressure observer on Mac
终于要切入正题，按照iOS调研的节奏开始查资料～

### how to observe
以下来自 [Github](https://github.com/mhuusko5/M5ApplicationMemoryWarning)

```
static dispatch_source_t source = nil;
source = dispatch_source_create(DISPATCH_SOURCE_TYPE_MEMORYPRESSURE, 0,  DISPATCH_MEMORYPRESSURE_WARN | DISPATCH_MEMORYPRESSURE_CRITICAL, dispatch_get_main_queue());

dispatch_source_set_event_handler(source, ^{
dispatch_source_memorypressure_flags_t pressureLevel = dispatch_source_get_data(source);
[NSNotificationCenter.defaultCenter postNotificationName:DidReceiveMemoryWarningNotification
object:nil
userInfo:@{ @"pressure": @(pressureLevel) }];
});

dispatch_resume(source);
```
其中 dispatch_source_create 的解释：

Creates a new dispatch source to monitor low-level system objects and automatically submit a handler block to a dispatch queue in response to events.

可以监控的 system objects 很多，包括 Timer，Signal， MemoryPressure 等。

其中 memoryPressure level 的定义如下： 

```
#define DISPATCH_MEMORYPRESSURE_NORMAL		0x01
#define DISPATCH_MEMORYPRESSURE_WARN		0x02
#define DISPATCH_MEMORYPRESSURE_CRITICAL	0x04
```

### how to mock
目前找到的[方法](https://bugs.chromium.org/p/chromium/issues/detail?id=463604),
在 terminal 输入：

```
sudo memory_pressure -S -l critical -s 15
```

-S causes the system to simulate the specified memory pressure (warn | critical), and -s tells how many seconds to hold that simulated pressure.

