# 如何在 Microsoft Outlook 中取消阻止不安全的附件

> 原文：<https://www.sitepoint.com/outlook-unblock-unsafe-attachments/>

这篇文章写于 2010 年，至今仍是我们最受欢迎的帖子之一。如果你热衷于学习更多关于电子邮件系统的知识，你可能会对最近这篇关于控制你的收件箱的文章很感兴趣。

有大量的桌面和在线电子邮件客户端，但很难击败微软的 Outlook。这个应用程序从 1997 年就出现了，尽管有一些关于 HTML 渲染的错误决定，它仍然是我和许多其他人选择的电子邮件客户端。

不幸的是，有一条 Outlook 信息是 web 开发人员讨厌的:

*Outlook 阻止了对以下潜在不安全附件的访问…*

感谢微软。我知道你为什么屏蔽那个 JavaScript 文件，但我知道它是什么，我真的真的很想要它。

Outlook 不允许您取消阻止不安全的附件。幸运的是，有一种方法可以获得该文件并避免请求重命名版本的尴尬。在我们开始之前…

**tip:** BIG REGISTRY WARNING!

我们将要深入研究 Windows 注册表。如果你不知道注册表是什么或者以前从未接触过它，请继续。这是一个危险的地方——一旦走错一步，你的电脑就会爆炸。

该修复程序还将降低 Outlook 安全设置。对于具有合理 IT 知识的人来说，这不是问题，但不是对所有人都是问题。

还在吗？好吧，你这样做:

**1。关闭 Outlook**

**2。运行 regedit.exe**
导航至:

```
HKEY_CURRENT_USERSoftwareMicrosoftOfficeXX.XOutlookSecurity

```

其中 XX。x 表示您的 Outlook 版本:

*   用于 Microsoft Office 2010 的 14.0
*   用于 Microsoft Office 2007 的 12.0
*   用于 Microsoft Office 2003 的 11.0
*   用于 Microsoft Office 2002 的 10.0
*   用于 Microsoft Office 2000 的 9.0 版

**3。创建一个新值**
在那个名为:

```
Level1Remove
```

**4。选择您的“安全”文件类型**
编辑密钥并输入您要解除阻止的附件扩展名列表。每个都应该以句点开头，并用分号分隔。比如说…

要仅取消阻止 JavaScript 文件，请输入:

```
.js

```

要取消阻止 JavaScript、VBScript 和 exe 文件，请输入:

```
.js;.vbs;.exe

```

单击“确定”保存值，然后退出 regedit。

**5。重启 Outlook**
附件应该会神奇地解封。如果不是这样，请尝试重新启动。

微软为使用 Exchange Server 的用户提供了一个[有用的支持页面](http://support.microsoft.com/kb/829982),提供了替代选项和配置建议。

## 分享这篇文章