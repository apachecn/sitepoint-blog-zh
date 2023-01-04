# 在 Sublime 2 文本中映射新的 tab 命令

> 原文：<https://www.sitepoint.com/mapping-commands-sublime-2/>

![console.log](img/7ea5c2c8ebb5adc1ef0209acfa41924d.png "console.log")

我发现自己一天要在 **console.log 中输入数百次**(尽管你可以使用类似“热键”的程序来映射你的命令)。我决定创建一个 sublime 代码片段，只需输入“co”并按 tab 键就可以完成这项工作。它还将光标放在带引号的调试字段中，并将 console.log 命令打印到 IDE 中。

相关帖子:

*   [如何设置 Sublime 2 文本编辑器](http://www.jquery4u.com/editors/setup-sublime-2-text-editor)
*   [Sublime2 vs Notepad++](http://www.jquery4u.com/editors/sublime2-vs-notepad)
*   [使用 Sublime 2 文本编辑器简化 jQuery](http://www.jquery4u.com/editors/jquery-and-sublime-2-text-editor)
*   【Sublime 2 的 jQuery 代码片段完整列表

## 描述:

这个简单得令人惊叹的片段**让你在几秒钟内完成 console . log**(当然不到一秒钟！).光标操作直接加载到调试消息中(自动用引号括起来),如果您再次按 tab，光标操作将加载到 log 命令中，因此您可以根据需要将其更改为 console.debug 或 console.dir。


```
Filename:  console_log.sublime-snippet
```

```
Trigger: co (tab)
```

```
Output: console.log('debug message');
```

```
Usage: co [tab] - load the console.log() command
```

## 安装:

将代码片段复制到 JavaScript 文件夹中名为“console_log.sublime-snippet”的文件中:

```
*C:Documents and SettingsdeeringsApplication DataSublime Text 2Packages*
```

或者，您可以在 cmd 中键入“%appdata%来加载目录。

## 崇高 2 片段:

```
 <snippet><content><tabtrigger>co</tabtrigger>
    <scope>source.js,source.js.jquery</scope>
    <description>Console log</description></content></snippet> 
```

## 结论:

从这个简单的例子中，您应该能够开始映射任何命令来生成新的代码片段，以便与 Sublime 2 一起使用。如果任何人创造了任何其他崇高的片段，请分享！:)

## 分享这篇文章