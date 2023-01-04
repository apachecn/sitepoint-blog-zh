# 学习减价:结构、语法和惯例

> 原文：<https://www.sitepoint.com/learn-markdown/>

Web 上的内容需要以 HTML 格式呈现。许多网络发布工具(如博客软件和 CMSs)会将你的内容(文本、图片等)转换成 HTML 格式。但是在很多情况下，您希望自己编写 HTML 内容……并且手动用 HTML 标记来标记内容既费力又不可行。输入降价。

Markdown 是为 Web 编写内容的一种简单、无摩擦的方式，也是开发人员创建文档的最佳方式。它让您可以使用简单的、基于文本的标记轻松地构建和格式化文档，然后这些标记会被转换为 HTML 所有这些都可以在您最喜欢的文本编辑器中完成。

如果你还没有使用 Markdown，现在可能是时候开始了。你可以在几分钟内学会基础知识，随着不断的使用，语法将成为你的第二天性。在本文中，我们将鼓励您简单地开始，并向您展示如何在创建内容时使用 Markdown 来完成一系列常见任务。

让我们开始吧！

## 什么是降价？

Markdown 是由 John Gruber 在 2004 年创建的一种轻量级标记语言。它易于编写，易于阅读，并且可以很容易地转换成 HTML。它主要是为网络写作而设计的。

它迅速流行起来，现在被用在它的创造者从未设想过的环境中。但它并不完美。它有局限性，尤其是它省略了许多您可能需要使用的 HTML 元素(比如表格)的格式。也可以有点暧昧。

因此，产生了一系列变体来处理这些问题:

*   CommonMark 试图将 Markdown 标准化，使其不那么模糊，与该过程中的一些原始语法相矛盾。
*   GitHub 风格的 Markdown (GFM)扩展了 CommonMark，在 GitHub 上创建文档时使用。
*   增加了表格、脚注、引用等的新语法。
*   Pandoc 扩展了多种输出格式(不仅仅是 HTML)的 Markdown，并支持文档元数据、脚注、表格、上标、下标等等。

一些 web 服务和 Markdown 编辑器支持其中一些变体的语法，甚至使用它们自己的 Markdown 版本。幸运的是，它们都支持原始的 Markdown 语法，这也是我们将在本文中关注的。

## 学习降价

学习降价的最好方法就是开始。选择一个用例并开始，无论是创建一篇博客文章，处理文档，或者只是给你的笔记添加一些基本的格式。当你需要的时候，一点一点地学习语法。

你可以使用你最喜欢的文本编辑器，或者从众多应用程序中选择一个来使用 Markdown。Markdown 编辑器可以简化学习过程，因为它们允许您在线或在单独的窗格中预览格式。这意味着您一眼就能看出是否使用了正确的语法。

就我个人而言，我使用标记为 2 的[来预览 Mac 上的降价文件。这是一个商业产品，但是当然你可以为你选择的编辑器找到很多免费插件。您还可以使用](https://marked2app.com) [Markdown Live Preview](https://markdownlivepreview.com/) 和 [StackEdit](https://stackedit.io/) 在线编辑和预览 Markdown 文件。

要帮助选择正确的 Markdown 编辑器，请参考这些综述文章:

*   [Mac 的最佳降价编辑器](https://www.sitepoint.com/the-best-markdown-editors-for-mac/)
*   [最好的 Windows Markdown 编辑器](https://www.sitepoint.com/best-markdown-editors-windows/)
*   【Linux 的最佳降价编辑器

## 结构化文档

Markdown 允许您向文档中添加结构元素，如**标题** ( `h1`、`h2`、`h3`等。).在 Markdown 中添加标题有几种方法。我最喜欢的是在标题前加上哈希`#`，每一级标题一个哈希:

```
# Heading 1

## Heading 2

### Heading 3

etc.

And this is body text. 
```

散列将较低级别的标题进一步向右移动，因此它们看起来是缩进的。您可以选择在行尾使用相同数量的散列来结束标题:

```
### Heading 3 ### 
```

还有第二种方法，虽然我没看到它被经常使用。您可以通过在 H1 标题下加等号`=`和连字符`-`来创建两级标题:

```
Heading 1 or Title
==================

Heading 2
--------- 
```

可以使用**横线** ( `<hr />`)或线来分隔文档的各个部分。你可以在 Markdown 中使用三个(或更多)连字符`-`、星号`*`、下划线`_`或等号`=`来创建它们。将它们单独放在一行上，两边都是空行:

```
Brief introduction.

===

# Chapter 1

Lots of text.

---

# Chapter 2

Some more text

--- 
```

**列表**是另一个重要的结构元素。无序列表(`<ul>`)是这样创建的:以星号`*`、加`+`符号或连字符`-`开始一行，后面跟一个空格或制表符，然后是文本:

```
* this
* is
* an
* unordered
* list

+ this
+ is
+ too

- and
- so
- is
- this 
```

选择任何适合你的符号。您可以在这些符号之间切换，最终结果将是相同的。我倾向于使用星号或连字符。

有序列表(`<ol>`)是后跟句点的数字。数字不一定要按顺序排列。这两种方法都可以:

```
1\. this
2\. is
3\. an
4\. ordered
5\. list

1\. and
1\. so
1\. is
1\. this 
```

我使用的 Markdown 编辑器在按下`return`时会自动继续一个列表。

如果你想用一个数字和一个句点开始一行，而不是开始一个列表，你需要用一个反斜杠`\`来转义这个句点:

```
2020\. A year we'll never forget. 
```

最后，普通文本的段落由一个或多个空行分隔:

```
This will be formatted as an HTML paragraph.

And so will this. 
```

## 基本文本格式

基本文本格式包括粗体和斜体。下划线一般不会在网页上使用，因为它是超链接的格式，所以 Markdown 不支持它。如果真的要用，就用`<u>` HTML 标签。(这个更一般的值得注意。如果 Markdown 不支持特定类型的 HTML 元素，您可以使用 HTML 标记来代替。只有一个警告:任何在 HTML 标签中的降价语法*都不会被解析。)*

斜体字由单个星号(`*`)或下划线(`_`)分隔:

```
this is *italics*
and so is _this_ 
```

粗体字由双星号(`**`)或下划线(`__`)分隔:

```
this is **bold**
and so is __this__ 
```

有些人喜欢选择下划线或斜体。例如，我通常对`**bold**`和`*italics*`都使用星号。

其他人喜欢用不同的符号来区分粗体和斜体，比如:`**bold**`和`_italics_`:

```
_You **can** also combine them_ 
```

## 块引号和代码块

**块引号**可以通过以大于号(`>`)开始一行来创建，就像旧的电子邮件客户端引用以前的消息一样:

```
> This is a blockquote. Single paragraphs
> can be continued like this on a second line.
> 
> Multiple paragraphs can be quoted by using a
> line with a single greater than symbol. 
```

我喜欢的方法稍微简单一点，只在每个引用段落的开头使用大于号。无论您使用的是硬包装还是软包装段落的编辑器，都是如此:

```
> You can also blockquote a paragraph
by placing a single greater than symbol at
the beginning of each paragraph.

> Nested blockquotes are also possible
> > Like this. 
```

**代码块**通过将每行缩进至少四个空格或一个制表符来创建:

```
This is a normal paragraph:

    This is a code block. 
```

但是其他风格的降价更喜欢使用反斜线。例如，Ulysses 在代码块的行首使用两个反勾号:

```
``This is a code block. 
```

GitHub 风格的 Markdown 使用三个反勾来开始和结束代码块。Obsidian、Bear 和其他一些 Markdown 编辑器遵循相同的惯例:

```
```
This is a code block.
``` 
```

为了将该代码块嵌入到一个代码块中，我将它包装在四个反斜线中。在 GitHub 上，如果您想让[语法突出显示](https://docs.github.com/en/github/writing-on-github/working-with-advanced-formatting/creating-and-highlighting-code-blocks)，您可以选择包含该语言:

```
```ruby
This is Ruby code with syntax highlighting.
``` 
```

通过使用单个反勾号分隔符，可以在段落中显示代码:

```
This code `<bold>` will be displayed, not interpreted. 
```

## 链接和图像

链接和图像使用方括号`[]`和圆括号`()`的组合。对于**链接**，您用方括号将锚文本括起来，紧接着是圆括号中的 URL:

```
This is a [link to a web page](https://url.com). 
```

如果你愿意，你可以给链接添加一个标题。当您将鼠标悬停在链接上时，它将显示为工具提示。在 URL 后面的括号内用引号将标题括起来:

```
This is a [link to a web page](https://url.com "This title will appear as a tooltip"). 
```

另一种标记链接的方法被称为*引用链接*。这些看起来像 Markdown 文档中的脚注，但在导出到 HTML 时将被转换为标准链接。这里的目标是使 Markdown 文档更具可读性。

您可以使用方括号中的标签，而不是直接链接到 URL。然后在文档的其他地方(通常在底部)，将该标签与一个 URL 相关联:

```
This is a [link to a web page][mylabel].

Then at the end of the document …

[mylabel]: https://url.com "Optional title"
or
[mylabel]: <https://url.com> (Optional title) 
```

标签不区分大小写，可以由字母、数字、空格和标点符号组成。

**图片**使用类似的语法，但以感叹号(`!`)开头:

```
![Alt text](https://imageurl.com) 
```

如果您愿意，可以在括号内添加一个用引号括起来的标题。

```
![Alt text](https://imageurl.com "This is a title") 
```

您也可以使用图像的参考链接:

```
![Alt text][mylabel]

[mylabel]: https://imageurl.com "This is a title" 
```

## GitHub 口味的减价附加产品

许多开发人员使用 GitHub 风格的 Markdown，它提供了额外的语法，使它更有能力。这里有一些例子。

**删除线**是一个附加的文本格式选项，通过双波浪符号(`~~`)实现:

```
This is how you do ~~strikethrough~~. 
```

**未勾选的项目用`- [ ]`创建任务列表**，勾选的项目用`- [x]`创建:

```
- [ ] This item is unchecked
- [x] This item is checked 
```

您可以通过使用管道和连字符画线来创建一个**表**。三个或更多的连字符`---`创建标题，管道`|`创建列:

```
| Heading 1 | Heading 2 | Heading 3 |
| --------- | --------- | --------- |
| some text | more here | and this  |
| some more | this too  | and this  | 
```

当列对齐时，这看起来更好，但这不是必需的。无论哪种方式，导出到 HTML 时都会正确创建表格:

```
| Heading 1 | Heading 2 | Heading 3 |
| --------- | --------- | --------- |
| some text | more here | and this |
| some more | this too | and this | 
```

创建这样的表格非常繁琐，尤其是当您需要编辑单元格的内容时。幸运的是，有在线的[表格生成器](https://www.tablesgenerator.com/markdown_tables)可以简化这个过程。

## 最后的话

Markdown 并不适合所有人，但也有很多人喜欢。就我个人而言，我很欣赏它的开放性、易学性，并且不会把你局限于使用某个特定的程序。

如果您已经阅读完本文，它可能也是适合您的工具。投入并开始使用它。需要的时候一点一点的学习语法，不知不觉就成了第二天性。

**请务必下载我们的[免费可打印减价备忘单](https://uploads.sitepoint.com/wp-content/uploads/2021/07/1626758102Markdown-guide.pdf)。它涵盖了核心降价语法、一些扩展语法、用于处理降价的工具和其他资源。**

## 分享这篇文章