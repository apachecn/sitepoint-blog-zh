# 在你的 WordPress 博客中显示 jQuery 源代码

> 原文：<https://www.sitepoint.com/show-jquery-source-code-wordpress-blog/>

想要在你的 WordPress 博客中包含 jQuery 源代码，而不需要通过一行行的格式编辑来显示源代码片段吗？

例如:
[![source-code-in-blog](img/f9ceb40acdc3787fa94e9c7e23dfb0b5.png "source-code-in-blog")](https://www.sitepoint.com/wp-content/uploads/jquery4u/2010/11/source-code-in-blog.png)

## 如何设置

1.安装名为[syntaxhighlight Evolved](http://wordpress.org/extend/plugins/syntaxhighlighter/ "syntaxhighlighter")的 WordPress 插件。

2.在 HTML 编辑器中输入文章的代码，如下:

```
[code lang="js"] jQuery code goes here [/code]
```

## 设置

更改设置以满足您的显示需求。我个人推荐以下设置，因为阅读你的代码的人可能也想把它复制到他们的剪贴板上，如果你包括行号，它们也会被复制，这是一个麻烦。

[![syntax-plugin-settings](img/3fb922405f46eef7a67ca4b675c1466c.png "syntax-plugin-settings")](https://www.sitepoint.com/wp-content/uploads/jquery4u/2010/11/syntax-plugin-settings.png)

插件设置页面也有一个预览窗口(见下文),这对于定制你的博客非常有用。

```
![syntax-plugin-preview](img/672aab6ae8203d299800155777afc165.png "syntax-plugin-preview")
```

## 其他设置/功能

### **高亮线**

您也可以突出显示特定的线条，使其更加突出。为此，您只需包括 highlight="1-3，6，9 "和要突出显示的行号。

[![syntax-plugin-highlighted](img/c048e5b1ea47d2a28850a2b005286778.png "syntax-plugin-highlighted")](https://www.sitepoint.com/wp-content/uploads/jquery4u/2010/11/syntax-plugin-highlighted.png)

### **缩进代码**

您可以使用文本编辑器，如 [Notepad++](http://notepad-plus-plus.org/download "notepadplusplus") 来缩进代码:

文本效果>文本效果编辑>重新导入 C++代码

[![indented-code](img/a55fdcf97554931e8b5661eb5ce11d5c.png "indented-code")](https://www.sitepoint.com/wp-content/uploads/jquery4u/2010/11/indented-code1.png)

## 支持的语言

action script
cs harp
CSS
Delphi
JavaScript
Java
perl
PHP
text
python
ruby
SQL
VB
XML
([查看支持语言的完整列表](http://en.support.wordpress.com/code/posting-source-code/ "all supported languages")

## 常见 web 语言的用法示例

```
[php] code goes here [/php]
```

```
[code lang="js"] code goes here [/code]
```

```
[css highlight="1-3,6,9"] code goes here [/css]
```

```
[sourcecode language="plain"] code goes here [/sourcecode]
```

```
[sourcecode language="html"] code goes here [/sourcecode]
```

实例:[http://www.jquery4u.com/basic-syntax/5-basic-jquery-syntax/](http://www.jquery4u.com/basic-syntax/5-basic-jquery-syntax/)
插件主页:[http://www . viper 007 bond . com/WordPress-plugins/syntaxhighlight/](http://www.viper007bond.com/wordpress-plugins/syntaxhighlighter/)Wordpress.org 下载:[http://wordpress.org/extend/plugins/syntaxhighlighter/](http://wordpress.org/extend/plugins/syntaxhighlighter/ "syntaxhighlighter")

另请参见:[向语法荧光笔添加帮助文本](http://www.jquery4u.com/snippets/jquery-add-syntaxhighlighter-text/)。

## 分享这篇文章