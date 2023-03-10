# 在 WP7 应用中使用 Windows Live ID

> 原文：<https://www.sitepoint.com/using-windows-live-id-in-a-windows-phone-7-app/>

您可能会惊讶地发现，尽管 Windows Live ID 和 Windows Phone 紧密集成，但无法在第三方应用程序中使用现有的 Windows Live ID 凭据。这意味着，如果您想通过新的 Messenger Connect API 访问用户的 Windows Live 个人资料、联系人列表或任何其他可用数据，您必须让用户在您的应用程序中根据 Windows Live ID 进行身份验证。在这篇文章中，您将了解如何使用 Messenger Connect APIs 在 Windows Phone 应用程序中进行身份验证。

**更新了移动应用的使用条款**

[Windows Live 开发者服务–使用条款](http://msdn.microsoft.com/en-us/windowslive/ff765012)已于 2011 年 6 月更新，允许在移动应用程序中使用(参见第 2 部分)。但是，需要注意的是，该协议规定“为使用 Windows Live API 版本 3 或版本 4 的移动设备开发或分发应用程序……未获授权”。这意味着您不能使用旧的 Web 或委托认证，您现在需要使用 OAuth 2 的 Messenger Connect 实现。

### 步骤 1:创建 Windows Live 应用程序

打开网页浏览器并转到 [Windows Live 开发者门户](http://msdn.microsoft.com/en-us/windowslive/default.aspx)。您需要使用 Windows Live ID 登录。当您登录时，会有一个通知，声明如果将有多个开发人员在该应用程序上工作，您应该创建一个公司 Live ID，该应用程序可以注册到该 ID。**重要提示**:无法将应用从一个 Live ID 转移到另一个。这意味着，如果您使用个人 Live ID 帐户创建应用程序，当您离开公司或希望出售该应用程序的权利时，将无法将其转移到另一个 Live ID。我的建议是花点时间想想适合你的策略。您可以决定使用您的 Live ID、公司 Live ID，甚至创建一个特定于该应用程序的 Live ID。最后一个选项最适合您将来可能希望出售的应用程序。

登录后，单击我的应用程序选项卡，然后单击创建应用程序链接。这将提示您输入应用程序的名称和语言，如图 1 所示。

[![WP7 Auth Figure 1](img/f03c08835892d2ceea80868126360aad.png)](https://www.sitepoint.com/wp-content/uploads/2011/07/sp0008_01.png)

图 1

一旦你点击“我接受”按钮，你将被分配一个客户端 ID 和客户端密码，如图 2 所示。

[![WP7 Auth Figure 2](img/a24e834c1b0add0ad8c37dcb91660c04.png)](https://www.sitepoint.com/wp-content/uploads/2011/07/sp0008_02.png)

图 2

此时，您应该导航回应用程序设置页面，并添加尽可能多的应用程序信息(图标、描述等)。当用户在您的 Windows Phone 应用程序中使用 Windows Live ID 进行身份验证时，他们将被告知哪个 Windows Live 应用程序要求他们进行身份验证。这将包括您在应用程序设置页面中输入的信息-信息越准确，用户越有可能同意使用 Windows Live ID 进行身份验证。

### 步骤 2:创建 Shell WP7 应用程序

我们将从一个非常基本的应用程序开始，它由一个用于启动身份验证过程的`Button`、一个用户将在其中输入凭据以通过 Windows Live 身份验证的`WebBrowser`控件以及几个用于显示已通过身份验证的用户信息的`TextBlock`元素组成。

```
<phone:PhoneApplicationPage x:Class="WindowsLiveIdSampleApp.MainPage"

    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:phone="clr-namespace:Microsoft.Phone.Controls;assembly=Microsoft.Phone">
    <Grid>
        <StackPanel x:Name="LayoutRoot" Background="Transparent">
            <Button Content="Authenticate" Click="AuthenticateClick" />
            <TextBlock x:Name="UserNameText" />
            <TextBlock x:Name="UserIdText" />
        </StackPanel>
        <phone:WebBrowser x:Name="AuthenticationBrowser" Visibility="Collapsed" Navigated="BrowserNavigated" IsScriptEnabled="True" />
    </Grid>
</phone:PhoneApplicationPage>
```

### 步骤 3:创建登录 URL

实质上，OAuth 的目的是将用户身份验证的责任委托给 Windows Live 服务。这包括捕获用户名和密码信息—主机应用程序不应直接接触或试图缓存这些信息。为了在 Windows Phone 应用程序中实现这一点，您需要将`WebBrowser`控件导航到适当的页面，以便用户登录并授权主机应用程序。

这一步包括构建正确的 URL 来导航`WebBrowser`控件。URL 由许多元素组成:

*   基本 URL:https://oauth.live.com/authorize
*   查询字符串参数:
    *   客户端 ID:您之前创建的 Windows Live 应用程序的客户端 ID
    *   响应类型:要使用的认证类型(我们将使用“令牌”)
    *   作用域:宿主应用程序请求访问的用户帐户部分
    *   重定向 URL:身份验证完成后，浏览器将重定向到的 URL。对于 web 应用程序，这将是您的网站中的一个页面，它将获取访问令牌。但是，因为我们正在构建一个客户端应用程序，所以您需要指定`https://oauth.live.com/desktop`，这是一个专用页面，用于提取访问令牌。
    *   Display:确定登录提示的样式。使用“触摸”以获得触摸驱动设备的最有用界面。

这一步唯一的技巧是确保所有的参数都被正确地连接起来，并进行适当的 URL 编码。

```
var uriParams = new Dictionary<string, string>() {
    {"client_id", "<your_client_id>"},
    {"response_type", "token"},
    {"scope", "wl.signin,wl.basic,wl.offline_access"},
    {"redirect_uri", "https://oauth.live.com/desktop"},
    {"display", "touch"}
};

StringBuilder urlBuilder = new StringBuilder();
foreach (var current in uriParams) {
  if (urlBuilder.Length>0) {
      urlBuilder.Append("&");
  }
  var encoded = HttpUtility.UrlEncode(current.Value);
  urlBuilder.AppendFormat("{0}={1}", current.Key, encoded);
}
var loginUrl= "https://oauth.live.com/authorize?" + urlBuilder.ToString();
```

在这种情况下，我们指定的范围请求允许应用程序进行身份验证(`wl.signin`)、访问配置文件信息(`wl.basic`)和保存访问令牌(`wl.offline_access`)。不要忘记将`(包括尖括号)替换为您在步骤 1 中创建 Windows Live 应用程序时获得的客户端 ID。`

 `### 步骤 4:提示 Windows Live ID 登录

为了让用户登录，您所要做的就是将`WebBrowser`控件导航到您刚刚创建的`loginUrl`控件。在这个应用程序中，你还需要显示`WebBrowser`控件。

```
AuthenticationBrowser.Navigate(new Uri(loginUrl));
AuthenticationBrowser.Visibility = Visibility.Visible;
```

### 步骤 5:用户登录并批准应用程序

用户将看到许多屏幕，提示他们输入凭证，然后将请求的权限授予应用程序，如图 3 所示。

[![WP7 Auth Figure 3](img/d07a8acda8249ab428483bca2c36bb43.png)](https://www.sitepoint.com/wp-content/uploads/2011/07/sp0008_03b.png)

图 3

### 步骤 6:提取访问令牌

在图 3 中，最后的图像是一个空白页。这实际上是重定向 URI，即`https://oauth.live.com/desktop`，它不应该被显示。相反，在隐藏`WebBrowser`控件之前，您的应用程序应该解析该页面的 URI 查询字符串并提取访问令牌。

```
public string AccessToken { get; set; }
private void BrowserNavigated(object sender, NavigationEventArgs e) {
  if (e.Uri.AbsoluteUri.ToLower().Contains("https://oauth.live.com/desktop")) {
    string text = HttpUtility.HtmlDecode(e.Uri.Fragment).TrimStart('#');
    var pairs = text.Split('&');
    foreach (var pair in pairs) {
      var kvp = pair.Split('=');
      if (kvp.Length == 2) {
        if (kvp[0] == "access_token") {
          AccessToken = kvp[1];
          MessageBox.Show("Access granted");
        }
      }
    }

    if (string.IsNullOrEmpty(AccessToken)) {
        MessageBox.Show("Unable to authenticate");
    }

    AuthenticationBrowser.Visibility = System.Windows.Visibility.Collapsed;
  }
}
```

### 步骤 7:从 Windows Live 访问内容

虽然使用访问令牌可以验证用户是合法的 Windows Live ID 用户，但它不应用于唯一标识用户。要获得关于登录用户的信息，您需要再发出一个请求，这次是对 Messenger Connect API 本身。所有对 Messenger Connect API(V5)的请求都以基本 URL`https://apis.live.net/v5.0`开始，在请求用户资料的情况下，您只需添加`/me`。向这个 URL 发出请求，在查询字符串中指定访问令牌，您将得到一个表示当前登录用户的 JSON 字符串。下面的代码使用`HttpWebRequest`类说明了这一点。

```
private void RequestUserProfile() {
    var profileUrl = string.Format("https://apis.live.net/v5.0/me?access_token={0}", HttpUtility.UrlEncode(AccessToken));

    var request = HttpWebRequest.Create(new Uri(profileUrl));
    request.Method = "GET";
    request.BeginGetResponse(result => {
        try {
            var resp = (result.AsyncState as HttpWebRequest).EndGetResponse(result);
            using (var strm = resp.GetResponseStream()) {
                var serializer = new DataContractJsonSerializer(typeof (WindowsLiveProfile));
                var profile = serializer.ReadObject(strm) as WindowsLiveProfile;
                this.Dispatcher.BeginInvoke((Action<WindowsLiveProfile>) ((user) => {
                    this.UserIdText.Text = user.Id;
                    this.UserNameText.Text = user.Name;
                }), profile);
            }
        }
        catch (Exception ex) {
            this.Dispatcher.BeginInvoke( () => 
            MessageBox.Show("Unable to attain profile information"));
        }
    }, request);
}

[DataContract]
public class WindowsLiveProfile {
    [DataMember(Name="id")]
    public string Id { get; set; }
    [DataMember(Name="name")]
    public string Name { get; set; }
}
```

最终输出是出现在相关的`TextBlock`控件中的用户名和 ID，如图 4 所示。

[![WP7 Auth Figure 4](img/6fb0254dad2903622f826ae8c8978115.png)](https://www.sitepoint.com/wp-content/uploads/2011/07/sp0008_04.png)

图 4

在这篇文章中，您看到了如何通过 Windows Live 进行身份验证，以及如何使用 Messenger Connect APIs 检索当前用户的信息。您还可以请求访问用户资料的其他方面，如联系人、事件、电子邮件，甚至可以添加到用户的活动源和更新他们的状态。这些都可以通过指定适当的范围，然后访问相应的 Messenger Connect API 路径来访问。更多信息可通过 [Windows Live 开发者门户](http://msdn.microsoft.com/en-us/windowslive/ff519582)和 [Messenger Connect](http://msdn.microsoft.com/en-us/library/hh243641.aspx) 文档获得。` 

## `分享这篇文章`