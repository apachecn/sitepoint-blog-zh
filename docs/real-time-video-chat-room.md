# 使用 WebRTC 和 Twilio 创建实时视频聊天室

> 原文：<https://www.sitepoint.com/real-time-video-chat-room/>

*这篇文章由[沃恩·安切塔](https://www.sitepoint.com/author/wancheta)和[蒂姆·塞韦里恩](https://www.sitepoint.com/author/tseverien/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

在我的上一篇文章“WebRTC 的黎明”中，我展示了如何使用 [Web 实时通信(WebRTC)](https://webrtc.org/) API 创建一个简单的 photo booth 应用程序。在这篇文章中，我将向你展示如何建立一个更实用的视频聊天室。

概括地说，WebRTC 的开发是为了让 web 和移动开发者能够使用简单的 API 创建高清视频和音频呼叫。许多大型和小型公司跨越所有行业，包括但不限于医疗保健、教育、客户服务、专业服务和社交媒体，正在利用 WebRTC 构建下一代应用程序。

很有可能你已经使用了这种技术，却没有意识到这一点。如果您使用过 Facebook Messenger、WhatsApp 和/或 SnapChat 视频或语音功能，那么您已经在不知不觉中使用了 WebRTC 与家人和朋友交流。

## 跨越式发展

WebRTC 和其他类似的 web 技术正在以各种方式改变人们交流和参与的方式。开发人员正在提供增强的通信，可以轻松集成到任何应用程序中。正如脸书、SnapChat、Tango 和 WhatsApp 等公司正在将实时音频和视频功能集成到他们的应用程序中，你也可以这样做。

你可能会惊讶，这样做的过程是简单，快速和所有成本效益最好的。由于这项伟大的技术是由谷歌开源的，你现在可以免费构建你的应用程序，没有许可费用。然而，如果您不熟悉 WebRTC 所需的一些常见组件，如 [TURN](https://webrtcglossary.com/turn/) / [STUN](https://webrtcglossary.com/turn/) 、[信令](https://webrtcglossary.com/signaling/)、[多点会议单元(MCU)](https://webrtcglossary.com/mcu/) 等等，那么构建自己的解决方案的道路可能会相当艰难。

在我们的行业中，有超过 20 家平台即服务(PaaS)提供商提供 WebRTC 解决方案。在获得了多年使用这项技术的经验后，我们(在 Blacc Spot Media)有了一些最受欢迎的 PaaS 提供商，它们已经证明对我们的客户非常有效。我们与许多供应商合作，缩短将客户产品快速推向市场所需的开发时间。我们有机会与之合作的供应商之一是 Twilio。在本文中，我们将重点介绍他们的平台。

## Twilio 视频

如果你不熟悉 [Twilio](https://www.twilio.com/) ，他们通过一组简单的 API 和 SDK 提供了一套通信工具。他们的解决方案使开发人员能够将实时通信功能添加到他们的应用程序中。Twilio 武器库中的新品是[可编程视频](https://www.twilio.com/video)，它允许您在移动和网络应用程序中创建高清多方视频和音频体验。Twilio 是 WebRTC 行业的资深人士，正在扩展其当前的 Twilio 客户端产品，该产品已经在其核心运行了一些 WebRTC 组件。Twilio Video 有着光明的未来，有着完整的增强路线图。很快，您将能够体验移动屏幕共享以及增强的多方功能。

## 建立一个聊天室

您将需要一个 Twilio 帐户来使用此演示。如果你还没有，你可以[现在就注册](https://www.twilio.com/try-twilio)一个免费账户。确保您选择“**可编程视频**作为您计划使用的第一项服务。创建帐户后，您将需要以下信息来创建应用程序:

| 资格证书 | 描述 |
| --- | --- |
| Twilio 帐户 SID | 您的主要 Twilio 帐户标识符–[在您的仪表板上找到它。](https://www.twilio.com/user/account/video) |
| Twilio 视频配置 SID | 将视频功能添加到访问令牌–[在此生成一个](https://www.twilio.com/user/account/video/profiles) |
| API 密钥 | 用于认证-[在此生成一个](https://www.twilio.com/user/account/messaging/dev-tools/api-keys)。 |
| API 秘密 | 用于认证-[就像上面一样，你会在这里得到一个](https://www.twilio.com/user/account/messaging/dev-tools/api-keys)。 |

我们还将使用 [Firebase](http://www.firebase.com/) 作为一种简单的方式来跟踪哪些用户可以聊天。Firebase 是一个移动后端服务，为您的应用后端提供支持，包括数据存储、用户身份验证、静态托管等。如果你没有账户，[注册](https://www.firebase.com/account/)就可以免费使用了。完成这些说明后，您就可以将此演示上传到服务器上，运行实时视频聊天室。

### 演示

本文中开发的代码可以在 [GitHub](https://github.com/sitepoint-editors/quick-start-your-webrtc-project-with-twilio) 上获得，你也可以在 Blacc Spot Media 查看一个[的现场演示](http://bit.ly/1L16dGD)。目前，WebRTC 仅在桌面上的 Google Chrome、Mozilla Firefox 和 Opera 中受支持:

我可以使用 rtcpeerconnection 吗？caniuse.com 各大浏览器对 rtcpeerconnection 特性的支持数据。