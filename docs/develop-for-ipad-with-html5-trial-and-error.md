# 使用 HTML5 为 iPad 开发:试错法

> 原文：<https://www.sitepoint.com/develop-for-ipad-with-html5-trial-and-error/>

上周，在苹果全球开发者大会(WWDC) 上，苹果首席执行官史蒂夫·乔布斯告诉开发者，苹果为其各种设备支持两个开发平台:应用商店，以及用 HTML5、CSS 和 JavaScript 编写的网络应用。

尽管针对这些设备的网络应用程序可能实现的目标肯定有实际限制，但雄心勃勃的开发人员，如 [script.aculo.us](http://script.aculo.us/) fame 的 [Thomas Fuchs](http://mir.aculo.us/) ，正在挑战这些限制，看看有什么可能。

[![screenshot of the Every Time Zone app running on iPad](img/9ffaeb65bf1e1c7ee79badc475d67677.png)](http://everytimezone.com/)

在一篇[富有启发性的博文](http://mir.aculo.us/2010/06/04/making-an-ipad-html5-app-making-it-really-fast/)中，Fuchs 解释了他在开发针对 iPad 优化的华丽的[时区](http://everytimezone.com/)应用程序时，为了在苹果的 iPad 上实现可接受的性能而被迫做出的各种技术选择:

*   **避免图像和 CSS 渐变**，但是在 HTML5 `<canvas>`标签上画渐变是可以的。
*   **避免 CSS 阴影**，这再次意味着依赖`<canvas>`的发光和阴影。
*   使用 CSS 制作动画，并保持简单，因为当事情变得复杂时，硬件加速就会消失。
*   **使用 JavaScript 触摸事件(例如`ontouchmove` )** ，而不是可用的`onclick`。
*   **避免 CSS 不透明**，它往往会在你最意想不到的时候禁用硬件加速。
*   **滚动自己的 JavaScript 代码，**因为目前像 jQuery 这样的框架只针对桌面浏览器进行了优化。
*   **使用 3D CSS 动画，即使你只需要 2D，**因为移动 Safari 只为 3D 翻译提供硬件加速。

从字里行间来看，使用网络技术为苹果设备开发仍然是一个试错的过程。随着苹果和其他公司的移动设备继续快速发展，未来几年规则将会频繁改变。

如果你想在这个舞台上比赛，你真的需要知道你的代码，这将使你有信心尝试两个或三个不同的方法来解决同一个问题。例如，如果你习惯于使用 jQuery，现在可能是一个很好的机会去参加像 [JavaScript Live](https://www.sitepoint.com/courses/javascript-live/) 这样的课程，以获得从头编写 JavaScript 的经验。

## 分享这篇文章