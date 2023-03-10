# 为 iOS 10 开发推送通知

> 原文：<https://www.sitepoint.com/developing-push-notifications-for-ios-10/>

虽然它们经常被过度使用，但通知是引起用户注意并通知他们需要采取的更新或操作的有效方式。iOS 10 带来了通知的更新，如新消息、优惠和时间表更改。在本教程中，我将向您展示如何在您的 iOS 应用程序中使用通知，并展示 iOS 10 引入的新功能。要开发 iOS 10 的推送通知，你需要最新版本的 *Xcode* 、 *Xcode 8 beta* ，可以在[下载页面](https://developer.apple.com/download/)上找到。

你可以从 [GitHub](https://github.com/sitepoint-editors/ios-pushnotifications) 下载完整的项目。

## 入门指南

在 *Xcode* 中启用推送通知很容易，但是你需要采取几个步骤。

创建一个新项目，给它一个惟一的名称和*包标识符*。

创建项目后，进入*项目设置*并选择*功能*选项卡。打开*推送通知*，如下图所示。

**注意**:如果你是苹果开发者计划的**付费**会员，你将只能在功能部分看到推送通知。

![Push notification capability](img/8512f7db5455de15e5d870feb549f851.png)

前往*开发者账户*部分，查看 Xcode 自动做出的更改。从左侧菜单中选择*证书、id&配置文件*，在*标识符*部分选择*应用 id*。找到创建的应用程序的名称，并选择它以显示服务列表。请注意，推送通知旁边有两个*可配置的*状态。

![Developer Account](img/08b252a4f40024eef8554e4f01fb98c3.png)

先不要关闭这个屏幕，你很快就会返回。

## 发送通知

在本文中，我将使用 [Pusher](https://github.com/noodlewerk/NWPusher/releases/tag/0.7.0) 来发送推送通知。你也可以使用其他解决方案，如[休斯顿](https://github.com/nomad/houston)。无论哪种方式，要发送通知，您都需要证书。

要创建证书，请打开*钥匙串访问*并选择*钥匙串访问- >证书助理- >向证书颁发机构申请证书*菜单项。

![Generate Certificate](img/a6f46f9f485933a76a2b49c0d877a6c5.png)

![Generate Certificate](img/fbd5a86ed255a44543e5480fc65787e6.png)

填写表格，点击*继续*。确保您选择了*保存到磁盘*。

![Generate Certificate](img/d08eade3447e4e1a821cefd47c091dc0.png)

返回到*开发者账户*屏幕。您可以在 *App IDs* 中生成用于开发或分发的证书。选择正确的应用程序后，在底部点击*编辑*。在*推送通知*部分，点击*创建证书*进行开发。

![Generate Certificate](img/c6559f6031e0a22d8dc8664362d08ce2.png)

需要时，上传从*钥匙圈*生成的证书请求并继续。

![Create Certificate](img/ff64872abe5bc983090b839132f48689.png)

您现在已经创建了证书，可以下载它了。要安装它，请打开下载的文件。

![Create Certificate](img/ad63dc87906742e984049a1206708e0c.png)

下载并运行 Pusher。窗口顶部的组合框需要一个*推送证书*。由于它位于您的*钥匙链*中，OS X 将在推送者试图访问证书时询问是否允许访问。

![Pusher](img/95b5a6b803aed2d92ac37a288b51717a.png)

![Pusher](img/fe79c0822cf783c5eefc3f57e5be8a1f.png)

第二个字段需要您将在下一步中获得的设备令牌。

## 获取通知

是时候编码了。接收通知的设备必须注册到苹果推送通知服务(APNS)。您可以通过发送应用程序启动时生成的唯一令牌来实现这一点。

打开 *AppDelegate.swift* ，添加如下方法。

**注**:该代码包含 Swift 3.0 的变更。语法可能看起来和你习惯的不一样。

```
func registerPushNotifications() {
  DispatchQueue.main.async {
    let settings = UIUserNotificationSettings(types: [.badge, .sound, .alert], categories: nil)
    UIApplication.shared().registerUserNotificationSettings(settings)
  }
} 
```

在设置中，你指定应用程序将接收的通知类型，我将在后面解释。在应用程序启动时在同一个文件中调用此方法。

```
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
  registerPushNotifications()
  return true
} 
```

此时，应用程序将自动显示一个弹出窗口，请求用户允许发送通知。

![Permission request](img/61866281489636551ee2ff0f66a6ec02.png)

只有在用户批准的情况下，才能注册和发送通知。`UIApplicationDelegate`方法处理响应。

```
func application(_ application: UIApplication, didRegister notificationSettings: UIUserNotificationSettings) {
  if notificationSettings.types != UIUserNotificationType() {
    application.registerForRemoteNotifications()
  }
} 
```

首先检查用户是否授予了权限，然后调用方法注册远程通知。当请求完成时，后者将调用另一个委托方法。响应由一个设备令牌组成，您可以打印该令牌以供调试。在发送推送通知以识别设备时，您将需要此令牌。

```
func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
  let chars = UnsafePointer<CChar>((deviceToken as NSData).bytes)
  var token = ""

  for i in 0..<deviceToken.count {
    token += String(format: "%02.2hhx", arguments: [chars[i]])
  }

  print("Registration succeeded!")
  print("Token: ", token)
} 
```

如果出错，调用下面的方法。

```
func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: NSError) {
  print("Registration failed!")
} 
```

**注意**:在应用程序启动时调用`registerUserNotificationSettings`很重要，因为用户可以在他们的设置中更改权限。此外,`registerForRemoteNotifications`也很重要，因为有些情况下设备令牌会改变，通知将不再发送。

到目前为止，这个设置足以接收示例通知。

## 通知有效负载

应用程序可以通过不同的方式接收通知负载中指定的通知，该负载包含有关应用程序如何通知用户的信息，或者您可以随通知一起发送的自定数据。

通知以 JSON 字典的形式发送给用户，该字典本身包含一个带有关键字`aps`的字典。在第二个字典中，您指定有效负载的键和值。

最常见的有:

*   **显示给用户的通知消息**。这可以是一个简单的字符串，也可以是一个包含标题、正文等关键字的字典。
*   **设备收到通知时将播放的声音**。可以是 app 的自定义声音，也可以是系统声音。
*   **数字**应用程序将在其图标的一角显示为一个徽章。将此项设置为 0 会移除徽章。
*   `content-available`:与值 1 一起使用，向用户发送静默通知。它不会播放任何声音，或设置任何徽章号码，但它会唤醒应用程序，以便它可以与服务器通信。

本教程剩余部分的通知负载示例如下:

```
{
  "aps": {
    "alert": {
      "title":"Hello! :)",
      "body":"App closed..."
    },
    "badge":1,
    "sound":"default"
  }
} 
```

## 应用程序生命周期

将 Xcode 控制台中打印的设备令牌复制到 Pusher。然后将上面的 JSON 对象复制到有效负载文本字段。

![Pusher](img/1d0c806105777883281a5bad08ab9acd.png)

尝试推送第一次通知。如果设备的屏幕被锁定，它将如下所示，但是当用户选择*视图*时，什么也不会发生。

![Push Notification](img/1c53098af0431ee9781e65bdb5f7aa8c.png)

要处理通知，您需要添加新方法:

```
private func getAlert(notification: [NSObject:AnyObject]) -> (String, String) {
  let aps = notification["aps"] as? [String:AnyObject]
  let alert = aps?["alert"] as? [String:AnyObject]
  let title = alert?["title"] as? String
  let body = alert?["body"] as? String
  return (title ?? "-", body ?? "-")
} 
```

如果结构相同，这将返回收到的通知的标题和正文。

```
func notificationReceived(notification: [NSObject:AnyObject]) {
  let viewController = window?.rootViewController
  let view = viewController as? ViewController
  view?.addNotification(
    title: getAlert(notification: notification).0,
    body: getAlert(notification: notification).1)
  } 
```

该方法将在应用程序主视图中的`UITableView`中添加一行([参见完整的项目](https://github.com/sitepoint-editors/ios-pushnotifications/blob/master/PushNotificationsApp/ViewController.swift)中的`ViewController`代码)。

我在三种情况下测试了推送通知:

### 当应用程序关闭时

如果用户从通知中打开应用程序，调用`didFinishLaunchingWithOptions`方法，更新如下:

```
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
  // Override point for customization after application launch.

  application.applicationIconBadgeNumber = 0; // Clear badge when app launches

  // Check if launched from notification
  if let notification = launchOptions?[UIApplicationLaunchOptionsRemoteNotificationKey] as? [String: AnyObject] {
    window?.rootViewController?.present(ViewController(), animated: true, completion: nil)
    notificationReceived(notification: notification)
    } else {
      registerPushNotifications()
    }
    return true
  } 
```

假设用户已经看到通知，则徽章被清除。然后，检查应用程序是通过图标还是通过通知打开的。在第一种情况下，调用`registerPushNotifications()`方法，流程像前面一样继续。如果应用程序从通知运行，调用自定义的`notificationReceived`方法并添加行。

### 当应用程序在前台时

如果用户正在使用该应用程序，这意味着该应用程序在前台，请使用以下方法处理通知。它将通知添加到`tableView`:

```
func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject]) {
  notificationReceived(notification: userInfo)
} 
```

**注意**:这种情况下，通知不会播放声音。

### 当应用程序在后台时

在这种情况下，我添加了一个方法来清除徽章号码。通知的处理方式与应用程序在前台时相同。

```
func applicationWillEnterForeground(_ application: UIApplication) {
  application.applicationIconBadgeNumber = 0; // Clear badge when app is or resumed
} 
```

最后，该表将有三行，包含所有通知的内容。

![TableView](img/95e880ba7d4f1ea5763d31af3c64d628.png)

## 向前推

对于开发者来说，iOS10 通知是一个更有趣的机会，提供了前所未有的交互性。我希望这篇应用程序通知入门指南能帮助你更好地理解它们是如何工作的。

祝您发送通知愉快。*愿法典与你同在！*

## 分享这篇文章