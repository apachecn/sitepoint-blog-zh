# 诺基亚 Windows Phone:问题 1

> 原文：<https://www.sitepoint.com/nokia-windows-phone-question-1/>

我们正在进行一项挑战，您可以[赢得一部诺基亚 Windows Phone](https://www.sitepoint.com/win-a-nokia-windows-phone/) 。上述比赛的规则在前面提到的帖子中有详细说明。未来几周将会有更多的问题，这里是第一个问题。

### 问题 1

Silverlight for Windows Phone 有一个类似于 Silverlight for desktop 的导航模型，其中有一个框架来承载一系列页面。在桌面上，框架外可能有内容(例如页眉、页脚或导航菜单)，而在大多数手机应用程序中，框架会占据整个屏幕，随后框架内呈现的页面也会占据整个屏幕。要在页面之间导航，您可以在框架上调用 navigate 方法，或者通过在 Windows Phone 应用程序的每个页面上作为属性公开的`NavigationService`。例如，以下导航到`SecondPage.xaml`

```
this.NavigationService.Navigate(new Uri("/SecondPage.xaml", UriKind.Relative)); 
```

除了能够向前导航到新页面之外，Windows Phone 应用程序还可以使用更多的导航 API。列出一个导航 API，并提供一个简短的例子来说明如何在你的应用程序中使用它？

## 分享这篇文章