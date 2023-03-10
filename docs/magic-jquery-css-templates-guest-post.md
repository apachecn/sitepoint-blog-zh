# 神奇的 jQuery 和 CSS 模板(客座博文)

> 原文：<https://www.sitepoint.com/magic-jquery-css-templates-guest-post/>

![jquery-css-templates](img/81308ddad72b15cbbc43e82aa5a4880c.png "jquery-css-templates2")

今天，我们很高兴在我们的 jQuery 博客上发表了第一篇客座博文。来自 **Binary Mind** 的 Bastien Liutkus 是法国 CBS 电视台的前端开发/设计师。他开发了 Magic jQuery——一个易于使用、灵活的针对网页布局管理&用户交互管理的优化插件。该插件出现在 jQuery 官方网站上:【http://plugins.jquery.com/project/Magic】T4。

[参见现场试玩](http://www.jquery-css.com/)
[下载页面](http://plugins.jquery.com/project/Magic)

## 关于插件

Magic jQuery 最初是为了满足巨大流量和 SEO 相关网站的需求而创建的，因此有其特殊性:

*   最小的插件大小，为减少页面加载时间而优化(压缩 35kB)
*   非常灵活，以便能够在很短的时间内编码大量的模板和不同种类的交互(我独自在 5 个巨大的流量网站的前端)。
*   由于内容位置的独立性和它在网页上的可见位置(如子菜单，对用户来说在页面的上方，但对谷歌来说在页面的末端)，SEO 帮助
*   针对平板电脑/智能手机特定行为的旧浏览器兼容性和透明编码。

## 技术方面

从技术上讲，该插件有 5 个主要方面:

1.  Position:任何元素都可以有一个固定的|绝对位置，如果需要的话，还可以有一个平移偏移量
    ex1:你希望#myElement 有一个固定的位置，你可以写:$('#myElement ')。set position(" fixed ")；
    ex2:您希望#myElement 在偏移量(40px，100px)处有一个固定的位置，您将编写:$('#myElement ')。setPosition({position:'固定'，offset :{left:40，top:100 })；
2.  对齐:您可以将任何元素与任何其他元素对齐，或者在任何方向上与屏幕对齐。
    ex1:你想将一个子菜单与 menuItem 的底部/左侧对齐，你可以写:$('#subMenu ')。align({topIsBottomOf:"menuItem "，left:" menuItem " })；
3.  限制:你可以通过限制任何其他元素或屏幕来停止任何元素的自然移动。
    例 1:当你滚动:$('#myElement ')时，你希望#myElement 永远不要出现在屏幕上。限制({ top:" " })；
    注意:基本上任何对齐/限制和位置的奇怪组合都有效，因此你可以开始想一些有趣的东西。
4.  菜单:目标是统一转盘、菜单、大型下拉菜单、标签等的实现。都是菜单。很容易将子菜单与一个项目相关联，如果需要特殊的行为，可以向它添加一些参数。
    ex : $("#anElement ")。setSubMenu("mySubMenu")
    菜单动作:打开菜单、关闭菜单、打开下一个、打开上一个、播放、停止
5.  消息:您可以从任何元素创建消息。如果需要的话，你可以指定许多参数，比如它的 html 内容，它的相对位置，如果没有鼠标经过它的话它的寿命等等

作者: **Bastien Liutkus** 来自**二元思维**——法国 CBS 的前端开发/设计师。
[联系作者](http://www.binarymind.org/index.php?option=com_contact&task=view&contact_id=6&Itemid=53&lang=en)

## 分享这篇文章