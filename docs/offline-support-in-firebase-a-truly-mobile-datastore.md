# Firebase 中的离线支持，一个真正的移动数据存储？

> 原文：<https://www.sitepoint.com/offline-support-in-firebase-a-truly-mobile-datastore/>

自从被 Google 收购以来，Firebase 已经发展成为一个强大的网络和移动应用平台。

Firebase 团队最近发布的公告处理了他们的定价结构，这一结构发生了巨大的变化。过去，有些人可能会觉得 Firebase 太贵，但现在它已经便宜很多了。成本降低的结果是，肯定会有一堆利用 Firebase 实时功能的酷项目。开发人员更感兴趣的是，Firebase 的开发团队在 5 月份的年度谷歌 I/O 大会上宣布了对该平台的全面离线支持。在本教程中，我打算找出这些新特性到底有多有用。

## 移动思维

Firebase 团队关于移动开发的意图从一开始就很明确。他们明白用户的移动体验有多重要，并在设计技术时一直牢记在心。有了离线支持，Firebase 的开发人员明白，设备在一天中即使不会丢失多次网络信号，也只会丢失一次。

该团队认为，即使在断开连接的情况下，来自应用程序的数据也应该持续存在。他们认为，仅仅因为你可能失去了服务，你不应该失去你的数据。牢记这一点，他们开发了 Firebase，以便在服务中断的情况下应用程序数据仍然存在。

## 通过本地 Firebase 实例进行离线数据同步

Firebase 在从客户机向服务器发送数据时使用同步。这不同于使用请求和响应系统的传统方法。因此，Firebase SDK 可以根据数据库的本地版本保存任何应用程序数据。为了实现这一点，Firebase 使用了一个本地读操作系统，该系统允许数据在本地保存，直到重新建立连接。一旦重新建立了连接，Firebase 将启动必要的功能并跟上服务器。

要触发 Firebase 的 Android 和 iOS SDKs 的离线支持，请将以下代码添加到您的应用程序中。

### ios

```
[Firebase defaultConfig].persistenceEnabled = YES;
```

### 机器人

```
Firebase.getDefaultConfig().setPersistenceEnabled(true);
```

通过在应用程序中定义这一点，您可以告诉 Firebase 持久化您的所有数据。

Firebase 在发布离线支持时包含了新的`keySynced`特性。当设置为 true 时，`keySynced`可以从您的应用程序中预取特定数据。

以下示例启用了此功能。

### ios

```
[ref keepSynced:YES];
```

### 机器人

```
ref.keepSynced(true);
```

当添加到您的应用程序中时，Firebase 允许开发人员指定他们希望用户访问该特性的数据。

## 离线 JavaScript 功能

Firebase 还为 JavaScript API 添加了功能。需要注意的是，对 JavaScript API 的离线支持主要是为了管理用户的在线状态。Firebase 为 JavaScript 开发人员提供了一个位于`.info/connected`的位置，用于明确检测用户的存在。使用时，该位置将返回一个布尔值，并告诉开发人员客户端是否已连接。下面是如何使用这个`/.info/connected`文件夹的例子。

```
var connectedRef = new Firebase("https://<YOUR-FIREBASE-APP>.firebaseio.com/.info/connected");
connectedRef.on("value", function(snap) {
  if (snap.val() === true) {
    alert("connected")
  } else {
    alert("not connected");
  }
});
```

由此，开发人员可以根据这些值记录有关连接的信息。Firebase 提供了`onDisconnect`类，它允许开发人员定义连接丢失时应该发生的特定操作。`onDisconnect`类可以通过以下方法使用:

*   `set()`
*   `remove()`
*   `update()`

当与`onDisconnect`配合使用时，这些方法允许我们在用户失去连接时从应用程序中写入或清除不同的数据。下面，我放了一些代码，展示了如何使用`onDisconnect`方法来记录连接丢失的时间。因此，一旦客户端从 Firebase 断开连接，时间戳就会被发送到`/lastConnected`目录。

```
var lastConnectedRef = new Firebase("https://.firebaseio.com/users/joe/lastConnected");
lastConnectedRef.onDisconnect().set(Firebase.ServerValue.TIMESTAMP);
```

## 结论

价格结构的这种变化加上离线支持，使得 Firebase 在选择处理应用程序数据的解决方案时成为更可行的选择。Firebase 目前已经在 190 多个开发人员的项目中实现，希望您现在可以亲自尝试这项服务。

如果你有任何问题，欢迎在下面评论，我会尽快回复你。

## 分享这篇文章