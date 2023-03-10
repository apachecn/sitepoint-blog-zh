# 如何在 Firefox 中为被阻止的显卡启用 WebGL

> 原文：<https://www.sitepoint.com/firefox-enable-webgl-blacklisted-graphics-card/>

WebGL 太神奇了。看看火狐里的 [X 翼](http://oos.moxiecode.com/js_webgl/xwing/)或者[第一人称俄罗斯方块](http://www.firstpersontetris.com/)。你说没用是什么意思？

我的笔记本电脑已经用了两年，但是，尽管有一个合理的专用显卡，WebGL 在 Firefox 中是禁用的。Mozilla 根据驱动程序版本号将特定的 GPU 列入黑名单，即

*   NVIDIA 卡需要编号为 8.17.12.5721 或更高版本的驱动程序
*   AMD/ATI 卡是 8.741.0.0 或更高版本
*   英特尔卡在 XP 上通常需要 6.14.11，在 Vista 上需要 7.15.10 或在 7 上需要 8.15.10(尽管有些产品被完全阻止)
*   MAC 需要 10.6 或更高版本的操作系统

总的来说，黑名单是一件好事。您不希望 WebGL 使浏览器崩溃，因为您的卡不支持正确数量的 Z/Stencil ROP 单元、128 位浮点 HDR、各向异性纹理过滤或其他一些没人理解的奇怪功能。

不幸的是，硬件供应商停止为传统产品*(上周二之前发布的任何产品)*生产驱动程序。在某些情况下，您的 PC 供应商将坚持发布他们自己修改过的驱动程序更新，但不能保证他们会这样做。你的显卡可能与 WebGL 兼容，但如果驱动程序版本比批准的版本低 0.0.0.1，它会在 Firefox 中被阻止。在地址栏中键入“关于:支持”，向下滚动到底部的“图形”部分，查看您的卡是否合适。

你所有的 Chrome 用户可以停止傻笑了——谷歌很快就会在他们的浏览器中采用 GPU 黑名单。

## 红色大警告

我们即将绕过火狐的 GPU 黑名单。这是一件愚蠢的事情，涉及到危险的配置设置。这样做风险自担:我不能对这些变化导致的任何灾难性的 PC 故障、偏头痛、瘟疫、爆炸或人体自燃承担责任……*但值得冒险玩 [X 翼](http://oos.moxiecode.com/js_webgl/xwing/)* ！

## 这里有龙

在 Firefox 的地址栏中键入 **about:config** ,并进行以下更改:

*   要启用 WebGL，请将 **webgl.force-enabled** 设置为 **true** 。
*   要启用层加速，将**Layers . Acceleration . force-enabled**设置为 **true**
*   要在 Windows Vista/7 中启用 Direct2D，请将**gfx . direct 2d . force-enabled**设置为 **true**

现在，交叉手指，重启 Firefox 并尝试运行 WebGL 应用程序。如果你的电脑开始冒烟，最好撤销这些更改。

## 分享这篇文章