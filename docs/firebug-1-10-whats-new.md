# Firebug 1.10 的新特性

> 原文：<https://www.sitepoint.com/firebug-1-10-whats-new/>

如果有一个原因让开发者继续支持 Firefox，那就是 Firebug。Firebug 不仅仅是一个浏览器插件。它是让客户端 web 开发变得可以忍受的工具。我确信，如果没有 Firebug，Ajax、Web2.0 和 HTML5 的采用不会以同样的速度增长。

虽然类似 Firebug 的控制台在所有主流浏览器中都有，但该工具仍在继续发展，1.10 版本将在未来一两天内自行安装到您的浏览器中。最新版本提供了一系列伟大的新功能和错误修复，所以让我们看看最好的…

## Cookie 标签

最明显的新功能是 **Cookies** 选项卡，允许您过滤、排序、检查、拒绝、创建、修改、复制、导出和删除现有 Cookies。

![Firebug cookies tab](img/1183172e17cda0034aee36803426ae38.png)

![Firebug cookie breakpoints](img/b4156f08050ed06b11a44e9ac9f357ea.png)您还可以设置 cookie 断点和条件。只需点击断点空白处，只要修改了 cookie，就会启动脚本调试器。

该功能已在 [Firebug 的 Firecookie 插件](https://getfirebug.com/wiki/index.php/Firebug_Extensions#Firecookie)中提供，但在主安装中看到它还是很好的。我通常使用 [Web 开发者工具栏](https://addons.mozilla.org/en-US/firefox/addon/web-developer/)来管理 cookie，但是 Firebug 的实现要巧妙得多。

## :焦点伪元素支持

在这个版本之前，Firebug 只支持样式菜单中的:悬停和:活动状态。现在，当元素获得焦点时，您可以显示应用于元素的样式:

![Firebug :focus style pseudo element](img/c46c3cac6722cf5fa3f52287fb8faec4.png)

## 计算样式文件和位置

“计算样式”面板现在显示每个属性的文件和行。可以点击链接将您带到该位置:

![Firebug computed styles](img/eb12b181440c7253d84f3967c0129967.png)

## 媒体查询规则编辑

使用媒体查询的 CSS @import 声明可以直接在 CSS 选项卡中编辑:

![Firebug CSS media query support](img/9e681016bb1012f1c9091b2251e80306.png)

## Web 字体声明

如果你使用的是网络字体，Firebug 现在可以将你直接链接到合适的@font-face 代码。右击任意字体名称，选择**检验申报**。

## 控制台改进

控制台对于评估 JavaScript 值和代码片段是必不可少的。编辑器现在在编辑 CSS 属性、监视面板中的变量、断点条件、数字、颜色等时具有颜色编码和自动完成功能。

此外，输入' **help** '查看 API 命令摘要。

## 速度提升

进行了多项改进，包括:

*   安装后无需重启浏览器
*   延迟加载:Firebug 组件仅在需要时启动，因此浏览器启动时间不受影响
*   降低内存需求(尽管这只会在几个月后发布的 Firefox 15 中表现出来)。

## 杂项改进

想要更多吗？你猜对了…

*   您可以通过右键单击选择器并选择“删除”*【规则名称】*来删除整个 CSS 规则。
*   HTML 实体可以显示为符号、名称或 unicode 字符—单击 HTML 选项卡上的下拉箭头。
*   颜色可以十六进制、RGB 或 HSL 格式显示—单击 CSS 选项卡上的下拉箭头。
*   所有选项卡菜单项都有工具提示帮助。

这个团队在 Firebug 1.10 上做得非常出色。下一版本的路线图有一个雄心勃勃的目标*“创新只有 Firebug 才能提供的特性”*！

更多信息和安装细节，请访问[getfirebug.com](http://getfirebug.com/)。

## 分享这篇文章