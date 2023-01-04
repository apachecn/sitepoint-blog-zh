# iOS 应用程序和 WatchKit 扩展之间的通信

> 原文：<https://www.sitepoint.com/passing-messages-between-ios-apps-and-watchkit-extensions/>

需要股票、天气、体育和交通信息等数据更新的 Apple Watch 应用程序在父 iOS 应用程序和 WatchKit 扩展之间引入了通信方法。

在本文中，我将展示如何创建这种通信，并介绍它们之间的实时消息传递。

让我们看一下 iOS 应用程序与其 WatchKit 扩展之间的通信概述，以及当您需要共享数据时，iOS 应用程序和 WatchKit 扩展可能处于的不同状态。

## openParentApplication:回复:

在 WatchKit beta 2 中，苹果引入了`openParentApplication:reply:`和相应的`application:handleWatchKitExtensionRequest:reply` UIApplicationDelegate 方法。在任何 WatchKit 接口控制器内部都可以调用`openParentApplication:reply:`方法，在 iOS 应用的 AppDelegate 文件内部会调用`application:handleWatchKitExtensionRequest:reply`。

`openParentApplication:reply:`可以将字典传递给主应用程序，该字典可以包含父 iOS 应用程序需要响应的任何数据。这允许 WatchKit 扩展从 Apple Watch 唤醒包含它的 iOS 应用程序，如果它被挂起或没有运行的话:

```
[WKInterfaceController openParentApplication:@{@"action" : @"actionName"} reply:^(NSDictionary *replyInfo, NSError *error) {
    if (error) {
        NSLog(@"An error happened while opening parent application: %@", error);
    } else {
         //do something..
    }
}];
```

iOS 应用程序启动后，`application:handleWatchKitExtensionRequest:reply:`传递与`openParentApplication:reply:`一起发送的字典。在`handleWatchKitExtensionRequest:reply:`中，你需要设置 iOS 应用程序应该运行的任何动作。比如发送一个 RESTful HTTP 请求，或者回复 WatchKit 扩展所需的任何东西。当您完成时，您应该调用`reply`块，让系统知道您已经完成了完成响应所需执行的任何任务:

```
- (void)application:(UIApplication *)application handleWatchKitExtensionRequest:(NSDictionary *)userInfo reply:(void(^)(NSDictionary *replyInfo))reply {
        //do some stuff
        //send back a dictionary of data to the watchkit extension
        reply(@{@"dataToReturn": @"dataToReturnValue"})
}
```

`reply`块允许 iOS 应用程序向 WatchKit 扩展返回数据。

例如，如果您正在开发一个将在后台运行的音乐应用程序，并且希望从 Apple Watch 控制它，这种方法会有所帮助。您将使用`openParentApplication:reply:`并仅使用字典来传递暂停、停止或恢复命令。

一个重要的注意事项是，如果父 iOS 应用程序尚未运行，它将在您回复请求后终止。除非，例如，您触发了后台模式来启动位置更新或音频。如果需要的话,`openParentApplication:reply:`方法将启动应用程序，但是它不会将它带到前台。该应用程序在后台状态下启动，这意味着如果它还没有出现在屏幕上，就不可能强制用户界面出现。

## NSUserDefaults

在 iOS 应用程序和它的 WatchKit 扩展之间创建通信的另一种方法是使用`NSUserDefaults`。NSUserDefaults 是一个位于 plist 文件之上的 API。随着 iOS 8 中扩展的发布，引入了<q>应用组</q>的概念。父 iOS 应用程序和扩展可以通过公共沙箱共享数据，方法是将两者都作为同一个组的一部分。NSUserDefaults 有一个构造函数，`initWithSuite:`,它允许您使用 NSUserDefaults API 在可执行文件之间保存和读取数据。

除了改变`init`方法，您习惯的 NSUserDefaults 没有任何变化。在 iPhone 应用程序中，您可以设置需要存储在 NSUserDefaults 中的数据:

```
NSUserDefaults *defaults = [NSUserDefaults initWithSuite:@"appGroupName"];
[defaults setInteger:4 forKey@"myKey"];
[defaults synchronize];
```

在 WatchKit 扩展中，您可以随时读取数据:

```
NSUserDefaults *defaults = [NSUserDefaults initWithSuite:@"appGroupName"];
NSInteger myInt = [defaults integerForKey@"myKey"];
```

这是在 WatchKit 扩展和其父 iOS 应用程序之间传递数据的一个很好的方法，特别是如果您不需要唤醒父应用程序来查看数据。请注意，您可以将任何数据类型序列化为 NSUserDefaults。这非常适合将数据共享到 Today Widget，该 Widget 没有打开父应用程序并请求数据的选项。

## 达尔文通知中心

在某些情况下，当值改变时需要通知，而以前的方法不能做到这一点。当用户使用 WatchKit 应用程序时，父 iOS 应用程序上的数据可能会发生变化。对于需要实时数据更新的情况，苹果推荐使用[达尔文通知中心](https://developer.apple.com/library/mac/documentation/Darwin/Conceptual/MacOSXNotifcationOv/DarwinNotificationConcepts/DarwinNotificationConcepts.html)。

我用了一个流行的库来处理这种情况，它叫做 [MMWormhole](https://github.com/mutualmobile/MMWormhole) 。MMWormhole 支持[CFNotificationCenter](https://developer.apple.com/library/ios/documentation/CoreFoundation/Reference/CFNotificationCenterRef/)Darwin 通知，支持实时变更通知。MMWormhole 使用 NSKeyedArchiver 作为序列化媒介，因此任何符合 NSCoding 的对象都可以作为消息工作。消息可以作为存档文件发送和保存，并在应用程序或扩展唤醒时读取。无论包含应用程序是否运行，MMWormhole 都会工作，但只有当应用程序在后台处于唤醒状态时，才会在包含应用程序中触发通知。这使得 MMWormhole 非常适合包含它的应用程序已经通过某种形式的后台模式运行的情况。

MMWormhole 依赖于有兴趣的各方能够监听并被通知来自其他各方的消息。效果是当一个消息通过虫洞被发送时，两边几乎是即时更新的。使用 MMWormhole 很像使用 NSUserDefaults，它使用相同的<q>应用组</q>配置。

假设我们有一个 iOS 应用程序更新公交车到达的剩余时间，您也需要在 Apple Watch 应用程序上即时更新相同的数据。在 iOS 应用程序中，我们可以设置一个虫洞，并在剩余时间发生变化时更新它:

```
self.wormhole = [[MMWormhole alloc] initWithApplicationGroupIdentifier:@"myAppGroup" optionalDirectory:@"wormhole"];
//whenever the time changes...
[self.wormhole passMessageObject:currentSpeed identifier:@"currentRemainingTime"];
```

使用 WatchKit 扩展，我们可以在`init`方法中更新当前剩余时间值。与 iOS 版 UIKit 不同，当在`WKInterfaceController`中调用`init`时，视图会被加载和验证:

```
- (instancetype)init {
    if (self = [super init]) {
      self.wormhole = [[MMWormhole alloc] initWithApplicationGroupIdentifier:@"myAppGroup" optionalDirectory:@"wormhole"];
      NSNumber *currentRemainingTime = [self.wormhole messageWithIdentifier:@"currentRemainingTime"];
      [self.remainingTimeLabel setText:[NSString stringWithFormat:@"%@ Seconds", currentRemainingTime];
    }
    return self;
}
```

这与我们使用 NSUserDefaults 完成事情的方式非常接近。有些情况下，Apple Watch 会在用户不与它交互时关闭屏幕(以节省电量)。这将使我们的 Apple Watch 应用程序停用。当用户再次点击我们的应用程序并开始使用它时，Apple Watch 的应用程序状态会再次激活。在这种情况下，我们应该再次开始监听任何传入的消息，因为在上面的示例中，我们已经在`init`方法中完成了工作，但是在`init`之后，数据可能会在幕后发生变化。对于这种情况，使用`WKInterfaceController`上的`willActivate`和`didDeactivate`方法来知道我们应该何时更新数据。通过这些方法，我们可以进行任何更改，但前提是我们的视图在屏幕上并且手表屏幕打开:

```
- (void)willActivate {
    // This method gets called when watch view controller is about to be visible to user
    [super willActivate];
    [self.wormhole listenForMessageWithIdentifier:@"currentSpeed" listener:^(id messageObject) {
    [self.topSpeedLabel setText:[NSString stringWithFormat:@"%@ MPH", (NSNumber *)messageObject];
    }];
}

- (void)didDeactivate {
    // This method gets called when watch view controller is no longer visible
    [super didDeactivate];
    [self.wormhole stopListeningForMessageWithIdentifier:@"currentSpeed"];
}
```

这意味着当屏幕关闭时(我们的应用程序可能仍在运行，但屏幕是空白的)，我们不想浪费时间与父 iOS 应用程序通信并更新用户看不到的 UI。

在这个简短系列的下一部分中，我将从头开始编写一个小的待办事项列表应用程序，实现上面解释的不同通信方法。同时，如果你有任何问题，请在下面的评论中告诉我。

## 分享这篇文章