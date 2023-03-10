# 如何为 GitHub 的 Atom 代码编辑器开发一个包

> 原文：<https://www.sitepoint.com/how-to-write-a-syntax-highlighting-package-for-atom/>

![Atom logo](img/c69e9b66d224996424c1cafb1fafc9cf.png)

Atom 是一个很棒的编辑器，默认情况下，它提供了开发项目所需的所有东西……除了一件事——您想在 Atom 中看到的那个细节。有一样东西可以是任何东西:一个写得更快的键盘快捷键；一个重要的特征；或者甚至是你使用的语言的语法高亮显示，但是默认情况下不支持。

好消息是 Atom 已经准备好迎接大量的包了。您可以用社区编写的这些包来扩展它的默认特性。但是，如果您没有找到您正在搜索的包呢？

自己写包没那么复杂，何乐而不为呢？在本教程中，我们将以语法高亮显示包为例，看看如何为 Atom 创建自己的包。

## 我们将建造什么

最近我想用 Scilab 语言开发一些程序。因为它是数学中使用的语言，所以它不是我们在 Atom 中默认找到的语言类型，也没有针对它的语法的包。这就是为什么我决定编写自己的包: [language-scilab](https://atom.io/packages/language-scilab) 。

在这里，我们将为您想要的语言编写一个类似的包。我们将首先看到如何用一个有效的`package.json`文件初始化一个全新的包。然后我们会写一些规则来突出我们的语言。最后，我们将看看如何发布我们的包——以便任何 Atom 用户都能够使用它。

## 初始化新的 Atom 包

Atom 使用一个配置文件夹来存储您所有的个人选项，以及您安装的软件包。该文件夹名为`.atom`，位于您的个人文件夹中(例如`/home/user/.atom`)。

![Viewing the packages folder](img/29a947155b8d3684c53fc5acc2f25bba.png)

您安装的软件包都位于该文件夹的`packages`子文件夹中。每个包都有自己的文件夹。因此，创建您的包的第一步是创建您的文件夹，以您的包命名。在我们的例子中，我创建了文件夹`language-mylanguage`:当我们想要添加对一种语言的支持时，这是一种命名约定。

现在，你的包裹是无效的。要被 Atom 识别，它需要在您刚刚创建的文件夹的根目录下有一个`package.json`文件。

![The package.json file](img/093a196279a49dbbe7885c8f7fe06cf1.png)

这个文件包含一些信息，比如您的包的名称或者我们可以找到它的存储库。下面是我们的`language-mylanguage`包的`package.json`文件(解释如下):

```
{
  "name": "language-mylanguage",
  "version": "0.0.0",
  "description": "Mylanguage language support in Atom",
  "engines": {
    "atom": "*"
  },
  "dependencies": {},
  "repository": {
    "type": "git",
    "url": "https://github.com/JeremyHeleine/language-mylanguage.git"
  },
  "bugs": {
    "url": "https://github.com/JeremyHeleine/language-mylanguage/issues"
  },
  "license": "MIT"
}
```

我们在这个文件中找到几个条目。首先，第`name`个:正如你所猜测的，它包含了你的包的名字。您可以(也应该)在`description`条目中添加一个描述，基本上是为了让其他用户知道您的包是做什么的。

`version`条目用版本号填充，版本号必须遵循以下约定:`major.minor.bug`。这里我们标明`0.0.0`。即使你知道你正在开发版本 1.0.0 或 0.1.0 或你的包，也要指明`0.0.0`。我们将在发布我们的包时看到原因。

`engines`条目可用于指示您的包工作所需的最低 Atom 版本。同样，我们发现`dependencies`条目表示您的包需要的其他包。如果你为另一个包创建一个插件，它必须被使用。

然后我们找到了`repository`条目。它是一个 URL，指示您的包的公共存储库所在的位置。如果您想发布您的包，您需要这个条目。如果您现在不想创建您的公共存储库，您可以让它暂时为空，但是考虑在发布之前填充它。

`bugs`条目是我们可以报告影响您的包的问题的 URL。这里我们使用 GitHub 为每个存储库提供的默认页面。最后，许可证名称可以用`license`条目来表示。

如果需要，可以填写其他条目。它们不是强制性的。所有可用条目都可以在 [Atom 文档](https://atom.io/docs/latest/hacking-atom-package-word-count#package-json)中找到。

现在您的包已经有了一个有效的`package.json`文件，Atom 可以识别它并加载它。然而，它现在完全没有用，所以是时候通过赋予它一些功能来使它变得有用了。

请注意，Atom 现在不会加载您的包:它会在启动时加载所有已安装的包。但是，您可以使用`View/Reload`强制 Atom 重新加载包。这对于查看您刚刚对包所做的更改非常有用。

## 创建语法高亮显示包

要添加新的语法高亮规则，您需要创建一个名为`grammars`的子文件夹。在此文件夹中，创建一个新的 CSON 文件，该文件以您想要支持的语言命名(例如`mylanguage.cson`)。这个文件将包含您所有的语法高亮规则。

### 基本信息

在识别我们语言的语法之前，我们需要告诉 Atom 如何识别这种语言。这可以通过新创建的 CSON 文件中的三个条目来实现:

```
'scopeName': 'source.mylanguage'
'name': 'Mylanguage'
'fileTypes': ['ext1', 'ext2']
```

`scopeName`条目是为主题、其他包甚至配置文件识别语言的一种方式。通常我们遵循上面使用的惯例。

当您使用您的语言编辑文件时，`name`条目包含将显示在 Atom 右下角的语言名称。

最后，`fileTypes`条目包含一个数组，列出您的语言使用的所有文件扩展名。每次使用这些扩展名打开文件时，Atom 都会自动选择语法高亮显示。

### 语法规则

您的语言的所有规则必须在第四个条目中声明:`patterns`。我们将在这里看到我们可以添加的两种规则。但是，任何添加的规则都必须在`patterns`数组中的`{}`之间声明:

```
'scopeName': 'source.mylanguage'
'name': 'Mylanguage'
'fileTypes': ['ext1', 'ext2']

'patterns': [
    {
        # First rule
    },
    {
        # Second rule
    },
    # …
    {
        # Last rule
    }
]
```

### 匹配语法元素

让我们用`match`建立我们的第一个规则，这是我们将看到的第一个类型。该规则将检测任何数字，如`4`、`7.2`甚至`.568`，例如:

```
{
    'match': '\\b(([0-9]+\\.?[0-9]*)|(\\.[0-9]+))\\b'
    'name': 'constant.numeric.mylanguage'
}
```

必须用有效的正则表达式填充`match`条目。然后，每次 Atom 看到匹配这个正则表达式的文本时，它将把它封装在一个`span`元素中，并在`name`中指明类名。您可以添加几个类名，每个类名之间用点分隔。

您应该注意到正则表达式包含了比平常更多的反斜杠。事实上，我们可以使用每个像`\b`这样的特殊字符来检测断词。然而，由于你的正则表达式只是一个字符串，如果你只是键入`\b`，`b`将被转义，什么也不会发生。我们希望`\b`字符串出现在最终的正则表达式中，位于被解析的字符串之后。这就是为什么我们需要对反斜杠进行转义。在实践中，这意味着你需要将你输入的每一个反斜杠加倍，这样`\b`就变成了`\\b`。

您可以在`name`条目中添加任何您想要的类名。然而，有一些惯例要遵循。一般来说，指出你想要突出显示的元素的类型，并以语言的名称结束。有很多不同的类型都是由不同的编辑器共享的。TextMate 在其关于命名约定的[文档中列出了它们。](http://manual.macromates.com/en/language_grammars.html#naming_conventions)

如果您使用这些约定，其他开发人员可以更好地理解您在做什么。但是，尊重这些约定是个好主意还有另一个原因:主题。

在 Atom 中，主题决定了要突出显示的元素的样式。这些主题试图尽可能兼容更多的语言，所以它们使用这些约定。

这样，在上面的例子中，我们不必添加任何 CSS 来样式化我们的数字。当我们使用约定时，我们的主题会自动检测到它。更好:因为每种语言都使用相同的约定，所以我们语言中的数字显示方式与其他语言中的数字显示方式相同。

### 匹配同一规则中的多个元素

`name`条目应始终存在。这里突出显示了验证正则表达式的整个文本。但是如果你想在同一个规则中突出几个元素呢？

让我们以如下的函数声明为例:

```
function nameOfTheFunction() {
}
```

我们想要突出显示单词`function`作为关键字，以及函数的名称。如果我们可以匹配`function`关键字使其高亮显示，没有这个关键字就无法检索到函数的名称。这就是为什么我们需要在同一个规则中突出这两个元素。这可以通过以下规则来实现:

```
{
    'match': '\\b(function)\\s+(\\w+)\\s*\\(.*\\)'
    'captures':
        '1':
            'name': 'keyword.control.mylanguage'
        '2':
            'name': 'entity.name.function.mylanguage'
    'name': 'meta.function.mylanguage'
}
```

`name`条目仍然包含封装整个文本的类名。然而，我们现在发现了`captures`条目，这需要一些解释。

我们的正则表达式包含了对`function`和函数名的捕捉括号。与其他语言一样，由于有了`captures`条目，这些捕获可以被检索到。然后，`1`指的是第一个捕获(`function`关键字)，而`2`指的是第二个捕获(函数名)。我们用`name`将正确的类名应用于它们。

通过上面的函数声明，我们在 Atom 中获得了以下内容:

```
<span class="meta function mylanguage">
    <span class="keyword control mylanguage">function</span>
    <span class="entity name function mylanguage">nameOfTheFunction</span>
    …
</span>
```

### 匹配规则的开头和结尾

有时检测元素的开始和结束比检测元素本身更容易。例如，字符串也是如此:

```
{
    'begin': '\''
    'end': '\''
    'name': 'string.quoted.single.mylanguage'
}
```

根据这个规则，Atom 将搜索第一个引号来开始字符串。然后，它找到的下一个引号将是字符串的结尾，这是意料之中的(搜索是不精确的)。您可以将所需的正则表达式指定为`begin`和`end`分隔符。和往常一样，`name`条目必须用您想要的整个检索元素的类名来填充。

例如，用这个:

```
var best_site = 'SitePoint';
```

…你得到了这个:

```
var best_site = <span class="string quoted single mylanguage">'SitePoint'</span>;
```

捕捉元素也是可能的。例如，突出显示引号是一项常见的任务。要实现这一点，可以使用`beginCaptures`和`endCaptures`。他们的工作方式和`captures`为`match`规则工作的方式完全一样。唯一的区别是，使用`beginCaptures`可以捕获`begin`正则表达式中的括号，而使用`endCaptures`可以捕获`end`正则表达式中的元素:

```
{
    'begin': '\''
    'beginCaptures':
        '0':
            'name': 'punctuation.definition.string.begin.mylanguage'
    'end': '\''
    'endCaptures':
        '0':
            'name': 'punctuation.definition.string.end.mylanguage'
    'name': 'string.quoted.single.mylanguage'
}
```

使用与上面相同的 JavaScript 行，您现在将得到:

```
var best_site =
    <span class="string quoted single mylanguage">
        <span class="punctuation definition string begin mylanguage">'</span>
        SitePoint
        <span class="punctuation definition string end mylanguage">'</span>
    </span>;
```

最后，您还可以捕获放置在分隔符之间的元素。由于没有使用`match`，所以不能使用`captures`，但是可以用`patterns`代替。

这个新条目是一个数组，列出了您想要在当前突出显示中突出显示的元素。这是一个只在分隔符之间执行的正则表达式。在下面的例子中，我们对`\'`进行了转义，以强调它是字符串中的一个特殊字符:

```
{
    'begin': '\''
    'beginCaptures':
        '0':
            'name': 'punctuation.definition.string.begin.mylanguage'
    'end': '\''
    'endCaptures':
        '0':
            'name': 'punctuation.definition.string.end.mylanguage'
    'name': 'string.quoted.single.mylanguage'
    'patterns': [
        {
            'match': '\\''
            'name': 'constant.character.escape.mylanguage'
        }
    ]
}
```

您可以根据需要添加任意数量的图案。根据上面的规则，如果我们想要突出显示下面的 JavaScript 代码:

```
var best_site = 'It\'s SitePoint';
```

…我们会得到这个:

```
var best_site =
    <span class="string quoted single mylanguage">
        <span class="punctuation definition string begin mylanguage">'</span>
        It<span class="constant character escape mylanguage">\'</span>s SitePoint
        <span class="punctuation definition string end mylanguage">'</span>
    </span>;
```

## 发布 Atom 包

因此，现在我们的产品包非常好用……对我们来说。如果您需要您的包，其他开发人员也可能需要它。既然这样，为什么不把自己的作品分享出来，让大家都来享受呢？

发布您的包之前要做的第一件事是检查是否已经存在与您的包同名的包。为此，您可以在 Atom 本身中搜索这个名称(在选项中，您可以在这里安装软件包)。你也可以检查 URL `https://atom.io/packages/name-of-your-package`:如果你得到一个错误，这个包还不存在，你可以发布你的。

要发布您的包，您需要一个公共存储库，就像您可以在 GitHub 上创建的那样。参考上面关于如何在包的`package.json`文件中指明您的库的 URL 的提示。

现在您已经准备好发布您的包了。在终端中，进入你的包的文件夹并输入`apm publish minor`来发布你的第一个次要版本。你也可以通过用`major`或`patch`替换`minor`来发布一个主要或非常次要的版本。

这个命令将做几件事。如果这是你第一次使用它，它将从注册你的包的名字开始。然后，它将检索当前版本号并增加它。这就是为什么我们把`0.0.0`表示为我们包的版本号:通过使用`apm-publish`，它将自动变成`0.1.0`(带有`minor`)。然后，该命令创建相应的 Git 标记并推送这些更改。对于这一步，将需要您的凭据。

最后，您的包被发布，而您不必拥有任何其他东西。

![apm publish](img/818ec3b751ea2c6656d630a6195fcc08.png)

您可以使用`apm unpublish name-of-the-package`取消发布您的一个软件包。如果你也想删除你的 GitHub 库，要小心:你必须在删除库之前取消发布你的包。如果您首先删除您的存储库，您将无法取消发布您的包。

## 结束语

Atom 是一个非常强大的编辑器。使用软件包，您可以增强它并添加您需要的特性。现在你知道如何创建你自己的，以及如何发布它们。

由于 Atom 非常灵活，您可以用包做许多不同的事情。这也意味着我们不能在一篇文章中描述如何编写任何类型的包。这就是为什么我们在这里只看到如何创建一个语法高亮显示包。然而，初始化和发布你的包总是以同样的方式完成。

如果您有任何问题或意见，请在下面回复。你有没有创建一个自己的包？跟我们说说吧！

## 分享这篇文章