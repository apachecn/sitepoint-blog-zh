# RubyMotion:梦想成真

> 原文：<https://www.sitepoint.com/rubymotion-dreams-do-come-true/>

RubyMotion 的网站称其为“iOS 的革命性工具链”。它让您可以快速开发和测试原生 iOS 应用程序…使用您熟悉和喜爱的出色的 Ruby 语言”。作为一个长期的 Ruby(和 Rails)开发者，当我第一次听到这个消息时，我以为我是在做梦！

我浏览了那些关于 iOS 开发的“…在 24 小时内”的书籍。我一直在和 Objective-C 做斗争(以至于我称之为‘令人讨厌的-C’)。我还与 XCode 特有的“拖动以将元素与其事件处理程序连接起来”的方法进行了斗争。一想到要编写可以编译成原生 iOS 的纯 Ruby，我就激动不已。我的想象力让我只需点击一下鼠标，就能把我的一些 Rails 应用程序直接转换成 iPhone 应用程序！

事实上，RubyMotion 可以编译成本地 iOS 机器码。你用它编写的应用在提交到苹果的应用商店时，和 Objective-C 版本没有什么区别。代码编译成 LLVM 机器码。
但是，这并不像编写 MRI Ruby 并将其编译为在 iPad/iPhone/iPod 上运行那么简单。

不完全是。你仍然需要遵守 Objective-C 的一些结构和语法。我们可以在 iOS 上应用我们最喜欢的 Ruby 习惯用法:

*   鸭子打字
*   熟悉的点符号
*   隐含回报
*   宝石
*   静态库的链接(iOS 或其他 RubyMotion)
*   测试(基于[麦克培根](https://github.com/alloy/MacBacon)
*   内存管理
*   交互式调试器(刚刚出现在 1.24 版本中)
*   读取-评估-打印循环(这个单一特性可以在 RubyMotion 上‘勾住’你；同样，DHH 的“15 分钟创建一个博客”吸引了大量的人使用 Rails)

另一个关键特性是能够在代码中自由混合使用 Ruby 和 Obj-C。你甚至可以用 RubyMotion 编写代码，这些代码可以静态链接到 XCode 项目中！

在最近一次针对我们本地 Ruby 用户组的演讲中，我用了这个简单的，尽管非常做作的例子来说明 Objective-C 和 RubyMotion 之间的区别

### ==目标-C ==

```
// Header (.h)
@interface Navigate: NSObjectc

@property (strong) NSString *name;
@property (strong) NSString *address;
@property (strong) CCLocation *location;

- (CLLocation)distanceFromLocation(CLLocation *)otherLocation;
@end

// Messages (.m)
@implementation Navigate

@synthesize name, address, location;

- (CLLocationDistance)distanceFromLocation(CLLocation *)otherLocation
{
return [self.location distanceFromLocation:otherLocation];
}
```

### == RUBYMOTION ==

```
// navigate.rb
class Navigate
attr_accessor :name, :address, :location

def distanceFromLocation(location)
self.location.distanceFromLocation(location)
end

end
```

RubyMotion 中的工作流是任何 Rails 开发人员都**非常* *熟悉的:

使用一组专门的`Rake`任务，您可以编译您的代码——它直接与 iOS SDK 链接——并在 iOS 模拟器上或直接在连接的设备上运行它。

用你最喜欢的编辑器写。Sublime Text 2、VIM、TextMate、EMacs 都有[插件](http://www.rubymotion.com/developer-center/articles/editors/)并且 RubyMotion 从版本 5 开始在 [RubyMine](http://www.jetbrains.com/ruby/whatsnew/) 中得到全面支持。

最受欢迎的工具如[捆绑器](http://gembundler.com/)和 [RVM](https://rvm.io/) 也可以工作。

### Ruby 社区前来救援

如前所述，这并不是编写 Ruby 代码(例如，在 Rails 应用程序中运行)并神奇地编译成在 iPhone 上运行的天堂。尽管 iOS API 基于 MVC，RubyMotion 支持控制器和视图的概念，但在编写 RubyMotion 代码时，仍然需要 iOS 结构的工作知识。

活跃社区的成员继续创新并开发有用的工具来帮助 RubyMotion 的发展。

从一开始，开发人员社区就开始利用 Ruby 在领域特定语言(DSL)方面的天赋进行破解和构建，并开发了一套实用程序，用更像 Ruby 的语法“包装”了繁琐的 [NeXTStep](http://en.wikipedia.org/wiki/NeXTSTEP) 表单。第一个是[气泡包装](https://github.com/rubymotion/BubbleWrap)(恰当的命名)。今天，您可以找到许多工具来:

*   [以编程方式构造 UI 元素](http://colinta.com/projects/sugarcube.html)
*   使用熟悉的 Ruby (hash)语法构建表单
*   利用 XCode 界面构建器和故事板 UI 格式

甚至还有[模型](https://github.com/sxross/MotionModel)和[活动记录](https://github.com/adelevie/ParseModel)仿真来帮助 Rails 老手们感觉更自在。

而且几乎每天都有新的 iOS 应用出现在苹果应用商店上，用 RubyMotion 写的[。最近的一些例子包括:](http://www.rubymotion.com/apps/)

*   [每日焦点](https://itunes.apple.com/us/app/daily-focus/id493378151?ls=1&mt=8)
*   [GG 按钮](http://itunes.apple.com/gb/app/the-gg-button/id443187938?mt=8)
*   [信标注释](https://itunes.apple.com/ca/app/beacon-notes/id540620563?mt=8)
*   [iBuy 美国](http://itunes.apple.com/app/id542986755)
*   [好习惯](https://itunes.apple.com/us/app/good-habits/id573844300)
*   [风扇船](https://itunes.apple.com/us/app/fanboat/id592162965)
*   伦敦街头美食
*   [全球聊天](https://itunes.apple.com/us/app/globalchat-2-pro/id571879934?ls=1&mt=8)

[构建系统](https://github.com/HipByte/RubyMotion)在 [Github](https://github.com/HipByte/RubyMotion)
上以[开源](https://github.com/HipByte/RubyMotion#license)的形式提供。虽然 RubyMotion 不是免费的，但是所有这些库和支持工具都是由社区创建的，并且在 [Github](http://www.github.com) 上提供。RubyMotion 的发展速度令人印象深刻。RubyMotion 的创始人 Laurent Sansonetti 成立的 HipByte 公司在修复和更新方面对社区做出了极大的响应。

我试图用'*tendent-C*'编写 iPhone/iPad/iPod/iAnything 应用程序，这简直是一场噩梦。
使用灵活、宽容的 Ruby 及其富于表现力的英式风格的能力，让 iOS 开发成为现在的梦想。

### 了解更多信息的资源

RubyMotion 网站有一个[介绍视频](http://www.youtube.com/embed/t_M2wQYRzwQ)。网站上还有[开发者中心](http://www.rubymotion.com/developer-center)和 [FAQ](http://www.rubymotion.com/support/#faq) 。务实工作室有一个很棒的[免费截屏](http://pragmaticstudio.com/screencasts/rubymotion)，展示了 RubyMotion 的许多亮点。
关于更深入的技术细节，可以参考 RubyMotion 网站上的[运行时指南](http://www.rubymotion.com/developer-center/guides/runtime/)。
和[运动投射。TV](http://motioncasts.tv/) 是一组专门关于 RubyMotion 的社区生成的截屏。

## 分享这篇文章