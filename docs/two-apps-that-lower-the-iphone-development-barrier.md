# 降低 iPhone 开发门槛的两款应用

> 原文：<https://www.sitepoint.com/two-apps-that-lower-the-iphone-development-barrier/>

当 iPhone 首次发布时，史蒂夫·乔布斯宣布网络是 iPhone 的开发平台。尽管最初有许多抱怨说不会有一个真正的设备应用平台，一个专门为 iPhone 创建的健康的网络应用生态系统开始出现。最终，乔布斯和他的公司让步了，给手机添加了一个完整的平台，允许任何人创建应用程序，利用手机独特的原生功能。但是这让 web 应用程序开发人员受到了冷落。

他们面临着两个选择:要么学习 Objective-C 和 Cocoa，这样他们就可以制作一个 iPhone web 应用程序的 iPhone 应用程序版本，要么坚持使用他们的 web 应用程序，因为他们无法访问 iPhone 的许多原生功能，所以处于劣势。

进入[大五](http://www.big5apps.com/)和 [PhoneGap](http://phonegap.com/) 。这两个项目本质上有着相同的目标——允许网络开发者通过网络访问 iPhone 的原生功能，如拍照、地理位置、振动和加速度计——但每个项目采取的方法略有不同。

## 五大浏览器方法

Big Five 是 iPhone 的“扩展浏览器”。本质上，这是一个与 iPhone 本地 API 挂钩的 Safari。通过 Big Five 访问的 Web 应用程序是使用当前的 iPhone SDK 和 UIWebKit 组件开发的，可以利用浏览器对 API 的访问来做一些事情，如通过调用特殊的 JavaScript API 文件来使用加速度计或 GPS。

应用程序可以创建一个特殊的 Big Five URL，可以在 iPhone 主屏幕上添加书签，并在 Big Five 中自动打开，而不是常规的 iPhone Safari。

然而，问题是，如果用户下载的话，Big Five 只对开发者有效。在用户 iPhones 上获得它的最大障碍是苹果公司迄今为止在应用商店拒绝了它。苹果拒绝该应用程序的原因是，它“对广大 iPhone 和 iPod touch 用户群体的效用有限。”实际上，它很可能因为安全问题而被拒绝。

## PhoneGap“AIR”方法

PhoneGap 是一个在 iPhone 上运行的 web 应用程序的容器，允许它们访问加速度计、GPS、sqLite，很快还会有摄像头和振动控制。基本上，PhoneGap 创建了一个指向特定 web 应用程序 URL 的地址栏免费 web 浏览器，并提供了一个用于访问原生 iPhone 功能的 JavaScript API。

据我所知，开发者可以使用 PhoneGap SDK 将所有东西打包成一个专门的 iPhone 应用版本的 web 应用。PhoneGap 甚至有自己的网络应用程序，允许开发人员“通过提供 URL、名称和图标图形，快速将其网络应用程序打包到原生 iPhone 应用程序中，然后自动创建原生 iPhone 应用程序。”然后，可以提交原生 iPhone 应用程序以包含在 App Store 中。

AJAX 开发人员 Dave Johnson [将 PhoneGap 与 Adobe AIR](http://blogs.nitobi.com/dave/2008/09/18/phonegap-air-for-the-iphone/) 进行了比较，尽管要将其打包还有很多工作要做。然而，这种比较是合理的。他写道:“PhoneGap 的目标是将手机开发带给网络开发者，就像 AIR 将桌面应用开发带给网络社区一样。”

## 结论

大五和 Phone Gap 的好处显而易见。它们允许开发者在 iPhone 上部署应用程序(理论上，他们的方法可以在其他手机平台上复制)，而不必学习新的编程语言。可以利用 iPhone APIs 但用 HTML、CSS 和 JavaScript 编写的 iPhone 原生应用程序对开发人员来说很有意义，可以理解的是，移动开发人员社区中对这些应用程序有很多关注。

下一步将是让苹果加入进来。

## 分享这篇文章