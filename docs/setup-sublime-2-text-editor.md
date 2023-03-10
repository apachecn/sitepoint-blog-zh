# 如何设置 Sublime 2 文本编辑器

> 原文：<https://www.sitepoint.com/setup-sublime-2-text-editor/>

对于那些想要设置 Sublime 2 文本编辑器来开始用 jQuery(或任何其他语言)编码的人来说，这里是如何做的**在几分钟内你就可以用它来编码**。

## 1.下载并安装软件包

[下载 Sublime 2](http://www.sublimetext.com/)

还有一个 **[开发版本](http://www.sublimetext.com/dev)，它会在新功能可用时提示您更新**(通常每隔几周左右——我推荐这个选项，直到它退出测试版并正式发布)。

## 2.下载并安装有用的插件

将插件(包括 [**jQuery 包**](https://github.com/mrmartineau/jQuery) )复制到:

```
*C:Documents and SettingsdeeringsApplication DataSublime Text 2Packages*
```

或者，您可以在 cmd 中键入“%appdata%来加载目录。

## 3.定制外观和感觉

改为自定义主题(我选择了[淡汽水主题](https://github.com/buymeasoda/soda-theme)):

*崇高文字 2 - >偏好设置- >用户全局设置:*

```
{
    "theme": "Soda Light.sublime-theme"
}

```

我使用 Notepad++已经有一段时间了，我已经习惯了它的颜色和 JavaScript 的布局方式。所以，因为我找不到 Sublime 2 的 Notepad++主题，所以我决定创建自己的主题。**将主题改为 notepad ++(或者另一个适合你口味的主题):**

*崇高 2 >喜好>配色>【选择主题】*

[**下载记事本加加主题**我创作的](http://www.jquery4u.com/files/Sublime2-Notepad-Plus-Plus-Theme.zip)。

## 4.自定义用户设置

这里和那里有一些调整，使崇高的外观和行为像你期望的那样，我列出了以下主要的。

*崇高文字 2 - >偏好设置- >文件设置-用户*

```
{
    "autoIndent": true,
    "color_scheme": "Packages/Color Scheme - Default/Notepad-Plus-Plus.tmTheme",
    "copyOnMouseSelect": false,
    "drawWhiteSpace": "none",
    "font_face": "Courier New",
    "font_size": 10,
    "gutter": true,
    "highlightLine": true,
    "rulers": false,
    "tab_size": 4,
    "translate_tabs_to_spaces": true,
    "trim_trailing_white_space_on_save": true,
    "use_tab_stops": true,
    "wantVerticalScrollBar": false
}

```

定制更多选项:[http://www.sublimetext.com/docs/file-type-preferences](http://www.sublimetext.com/docs/file-type-preferences)

## 自定义热键

我是热键的忠实粉丝，所以我做了一些改变来适应我的需要和我习惯按的键。

Sublime2 >首选项>按键绑定–默认

*   CTRL+SHIFT+D–复制所选内容(块/线)。在记事本++中，这只是 CTRL+D 。您需要 CTRL+D 来展开选择，所以请确保您更改了这一点，这样您就不会在命令上有双重映射。
*   CTRL+/–选定的注释(行)。在记事本++中，这是 CTRL+Q 。同样，默认情况下这是用来录制宏的，所以请确保您也更改了这一点。

***CTRL+P***–快速搜索文件
***CTRL+SHIFT+P***–快速搜索命令
***SHIFT+F11***–全屏代码
***CTRL+D***–多选
***CTRL+SHIFT+D***–快速复制选择到下面一行

 *## 结论

**就是这样！**您还可以进行更多的定制，但这将为您提供开始工作的基本设置！

## 常见问题解答

我回答了一些关于 Sublime 2 的问题，如果你在安装时有困难，希望它们能帮到你。

**如何设置默认语言 jquery？**
安装 jquery 包然后按 CTRL+SHIFT+P，输入“jQuery”

**如何淡化代码中那些实心横线？**
重启崇高修正本

如何获得一个看起来像默认记事本++的主题？
我做了自己的版本来模仿它，叫做记事本加加号

**如何将控制台从底部切换到顶部(即使用顶部控制台)？**
安装快捷插件然后按 CTRL+P / CTRL+SHIFT+P

**如何折叠功能？**
尚未开发但功能正在开发。

**为什么我的快速文件切换不起作用？**
检查你没有重复的键绑定来阻止它——在默认的键绑定文件中搜索“CTRL+P”。

**如何快速比较文件是否相等？导航栏中的**
高亮显示>右键> dff 文件

**如何在 sublime2 中让代码变漂亮？**
是一个可以做到这一点的插件。参见帖子:Sublime 2 的 Javascript 美化插件

**错误？**
![sublime2-jquery-error](img/21380f8c576413092aa191f7a8619beb.png "sublime2-jquery-error")

*   尝试删除 jQuery 包文件夹 reload Sublime，然后关闭它。然后复制回 jQuery 包文件夹中。当它在语言文件中注册时，应该会成功。
*   否则，尝试删除缓存文件。
*   否则，尝试删除软件包控制插件文件夹和已安装的软件包文件夹(它可能被您的代理阻止)。

相关帖子:

*   [Sublime2 vs Notepad++](http://www.jquery4u.com/editors/sublime2-vs-notepad)
*   [使用 Sublime 2 文本编辑器简化 jQuery](http://www.jquery4u.com/editors/jquery-and-sublime-2-text-editor)
*   【Sublime 2 的 jQuery 代码片段完整列表
*   [在 Sublime 2 文本中映射新的 tab 命令](http://www.jquery4u.com/editors/mapping-commands-sublime-2)

## 分享这篇文章*