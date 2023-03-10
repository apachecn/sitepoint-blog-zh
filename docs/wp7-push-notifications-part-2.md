# WP7 推送通知第 2 部分

> 原文：<https://www.sitepoint.com/wp7-push-notifications-part-2/>

到目前为止，我们已经了解了 Windows Phone 平台上通知的[重要性，以及您的应用程序可以使用的不同](https://www.sitepoint.com/personalise-windows-phone-7-live-tiles/)[类型的通知](https://www.sitepoint.com/push-notifications-in-windows-phone/)。在这篇文章中，我们将介绍如何设置你的应用程序来接收通知，以及如何发送通知。

配置您的应用程序以接收通知的第一步是向 Microsoft hosted Push Notification Service 注册。这是通过创建 HttpNotificationChannel 类的实例并调用`Open`方法来实现的。`Open`方法是一种异步方法，它将在推送通知服务和运行在特定设备上的应用程序之间建立通信通道。一旦通道被打开，通道 Uri(可以认为是通道的惟一标识符)就会通过`HttpNotificationChannel`实例返回给应用程序。在`HttpNotificationChannel`实例上引发一个`ChannelUriUpdated`事件，允许您的应用程序检测通道何时建立。

下面的代码演示了如何创建 HttpNotificationChannel，连接`ChannelUriUpdated`事件，然后打开通道。一旦通道建立完毕，`Open`方法将立即返回，`ChannelUriUpdated`事件随即被引发。请注意，`ChannelName`是您为应用程序分配给通道的任意名称。

```
private const string ChannelName = "MyApplicationChannel";

public HttpNotificationChannel Channel { get; set; }
public bool ChannelIsActive { get; set; }

private void EstablishNotificationChannel() {
    Channel = new HttpNotificationChannel(ChannelName);
    Channel.ChannelUriUpdated += HttpChannelChannelUriUpdated;
    Channel.Open();
}

private void HttpChannelChannelUriUpdated(object sender, NotificationChannelUriEventArgs e) {
    UpdateChannelIsActive();
}

private void UpdateChannelIsActive() {
    ChannelIsActive = Channel != null &amp;&amp; Channel.ChannelUri != null &amp;&amp;
                                      !string.IsNullOrEmpty(Channel.ChannelUri.OriginalString);
}
```

一旦打开，即使应用程序关闭或被删除，通道也可能继续存在。这是为了确保当您的应用程序不活动时，设备能够接收到磁贴和 toast 通知。如果您的应用程序每次运行时都试图创建一个新通道，则会引发异常，因为您一次只能有一个活动通道。要解决这个问题，在尝试创建新的`HttpNotificationChannel`之前，您需要调用静态的`Find`方法来查看是否已经有可用的通道，如下面的代码所示。

```
private void EstablishNotificationChannel() {
    Channel = HttpNotificationChannel.Find(ChannelName);
    if (Channel == null) {
        Channel = new HttpNotificationChannel(ChannelName);
        Channel.ChannelUriUpdated += HttpChannelChannelUriUpdated;
        Channel.Open();
    }
    else {
        Channel.ChannelUriUpdated += HttpChannelChannelUriUpdated;
    }

    UpdateChannelIsActive();
}
```

你应该注意到，即使`HttpNotificationChannel`存在，你仍然应该连接`ChannelUriUpdated`事件，因为存在这样的场景:一个`HttpNotificationChannel`实例将被返回，但是通道还没有机会初始化。在这些情况下，一旦建立了通道，就会引发`ChannelUriUpdated`事件。

到目前为止，我们所做的只是建立了接收通知的通道。然而，我们还没有告诉应用程序或设备，当收到通知时该做什么。让我们依次看一下每种类型的通知。

### 原始通知

通道已经建立，为了让应用程序接收通知，您不需要做任何其他事情来配置通道。然而，为了处理收到的通知，您需要向`HttpNotificationChannel`上的`HttpNotificationReceived`事件添加一个事件处理程序。图 4 说明了该事件的事件处理程序；通知对象的 Body 属性是一个流，您的应用程序可以从中读取，它表示通过原始通知发送的原始字节。
￼

[![Push Notifications Figure 1](img/89cf1543988a998c528f7bc2480216b5.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/pushnotifications1.png)

图 1

发送任何类型的通知都是对应用程序实例的`ChannelUri`执行 HTTP POST 操作。您需要设置一些额外的头，并且有效负载必须根据您发送的通知类型进行适当的格式化。对于原始通知，有效负载是非结构化的。

下面的代码为提供的`channelUri`建立一个新的 web 请求，设置适当的头，打开请求流，写入`notificationMessage`，然后完成请求。这段代码还演示了如何解析响应来提取关于通知、通道和设备连接状态的状态信息。

```
private static Guid SendNotification(byte[] notificationMessage, Uri channelUri, 
                                                                  int notificationType, string targetHeader = null) {
    var request = (HttpWebRequest)WebRequest.Create(channelUri);
    request.Method = "POST";
    request.Headers = new WebHeaderCollection();
    var messageId = Guid.NewGuid();
    request.Headers["X-MessageID"] = messageId.ToString();
    request.Headers["X-NotificationClass"] = notificationType.ToString();
    if (!string.IsNullOrEmpty(targetHeader)) {
        request.ContentType = "text/XML";
        request.Headers["X-WindowsPhone-Target"] = targetHeader;
    }

    request.BeginGetRequestStream((result) => {
        var req = result.AsyncState as HttpWebRequest;
        using (var requestStream = req.EndGetRequestStream(result)) {
            requestStream.Write(notificationMessage, 0,
                                notificationMessage.Length);
        }
        req.BeginGetResponse((responseResult) => {
            var responseRequest = result.AsyncState as HttpWebRequest;
            using (var response = responseRequest.EndGetResponse(responseResult))
            {
                string notificationStatus = response.Headers["X-NotificationStatus"];
                string notificationChannelStatus = response.Headers["X-SubscriptionStatus"];
                string deviceConnectionStatus = response.Headers["X-DeviceConnectionStatus"];
            }
        }, req);
    }, request);

    return messageId;
}
```

现在您需要做的就是用适当的参数调用`SendNotification`方法。对于原始通知，您可以进行以下调用。3 表示通知类型是应立即发送的原始通知。

```
private void SendRawClick(object sender, RoutedEventArgs e) {
    var msg = Encoding.UTF8.GetBytes("Hello World!");
    SendNotification(msg, Channel.ChannelUri, 3);
}
```

请注意，在原始通知的情况下，您不需要指定`targetHeader`属性，因为这仅与磁贴和 toast 通知相关。

### Toast 通知

因为您希望即使用户不在您的应用程序中，您的应用程序也会出现 toast 通知，所以您需要让操作系统知道它应该代表您的应用程序接受 toast 通知。为此，您需要在调用了`Open`之后，调用`HttpNotificationChannel`实例上的`BindToShellToast`方法。

```
Channel = new HttpNotificationChannel(ChannelName);
Channel.ChannelUriUpdated += HttpChannelChannelUriUpdated;
Channel.Open();
Channel.BindToShellToast();
```

当用户在您的应用程序中，并且一个 toast 消息被发送到您的应用程序，而不是通常的 toast 接口，该消息将被您的应用程序截获，在`HttpNotificationChannel`实例上引发`ShellToastNotificationReceived`事件。

```
Channel.HttpNotificationReceived += HttpChannelHttpNotificationReceived;     
Channel.ShellToastNotificationReceived += HttpChannelShellToastNotificationReceived;
```

在相应的事件处理程序中，您可以解析返回字符串的集合。Toast 通知由键值为“text1”和“text2”的两个字符串组成。

```
private void HttpChannelShellToastNotificationReceived(object sender, NotificationEventArgs e) {
    var sb = new StringBuilder();
    foreach (var kvp in e.Collection) {
        sb.AppendLine(string.Format("Key:{0} Value:{1}", kvp.Key, kvp.Value));
    }
    this.Dispatcher.BeginInvoke(() => {
                                        MessageBox.Show(sb.ToString());
                                    });
}
```

最后，要发送一个 toast 通知，只需生成适当格式的 XML，并将其转换为一个字节数组，该数组成为通知的有效负载。

```
private void SendToastClick(object sender, RoutedEventArgs e) {
    var messageTemplate = "<?XML version="1.0" encoding="utf-8"?>" +
                                                    "<wp:Notification XMLns:wp="WPNotification">" +
                                                        "<wp:Toast>" +
                                                            "<wp:Text1>{0}</wp:Text1>" +
                                                            "<wp:Text2>{1}</wp:Text2>" +
                                                        "</wp:Toast>" +
                                                    "</wp:Notification>";
    var message = string.Format(messageTemplate, "Hello", "World!");
    byte[] msg = Encoding.UTF8.GetBytes(message);
    SendNotification(msg, Channel.ChannelUri, 2,"toast");
}
```

在这种情况下，`SendNotification`方法中指定的 2 表示立即发送 toast 通知。注意，还需要指定“toast”的`targetHeader`参数。

### 磁贴通知

与 toast 通知一样，为了让设备在应用程序未运行时处理图块通知，您需要通知操作系统。这是通过`BindToShellTile`方法完成的。请注意，由于您可以使用来自远程服务器的背景图像来更新 Live Tile，因此`BindToShellTile`方法可以接受一组域作为参数。在图块通知中只能指定来自这些域的背景图像(或与应用程序打包在一起的本地图块)。

```
Channel = new HttpNotificationChannel(ChannelName);
Channel.ChannelUriUpdated += HttpChannelChannelUriUpdated;
Channel.Open();

var allowedDomains = new Collection<Uri>();
allowedDomains.Add(new Uri("Http://www.builttoroam.com"));
Channel.BindToShellTile(allowedDomains);
```

当收到图块通知时，即使应用程序正在运行，应用程序内也不会引发任何事件。与 toast 通知一样，发送 tile 通知只需要正确的 XML 格式的消息。

```
private void SendTileClick(object sender, RoutedEventArgs e) {
    var messageTemplate = "<?XML version="1.0" encoding="utf-8"?>" +
                                                    "<wp:Notification XMLns:wp="WPNotification">" +
                                                        "<wp:Tile>" +
                                                                "<wp:BackgroundImage>{0}</wp:BackgroundImage>" +
                                                                "<wp:Count>{1}</wp:Count>" +
                                                                "<wp:Title>{2}</wp:Title>" +
                                                            "</wp:Tile> " +
                                                        "</wp:Notification>";

    var message = string.Format(messageTemplate, "Background2.jpg", 5, "New Title");
    byte[] msg = Encoding.UTF8.GetBytes(message);
    SendNotification(msg, Channel.ChannelUri, 1, "token");
}
```

请注意，“1”表示应该立即发送 tile 通知，targetHeader 应该是“token”(而不是您所期望的“tile”)。这个特定的磁贴更新引用了一个不同的图像，Background2.jpg，它被打包成应用程序 XAP 文件中的内容，一个 5 的徽章和一个要显示的新标题，如图 2 所示。

[![Push Notifications Figure 2](img/ffdac3f92d92b4c79cb69305a77747ef.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/pushnotifications2.png)

图 2

在这篇文章中，你已经看到了一个设置你的应用程序来接收通知的快速概述，以及如何向设备发送 raw、toast 和 tile 通知。

## 分享这篇文章