# 以“正确的方式”本地化 PHP 应用程序，第 4 部分

> 原文：<https://www.sitepoint.com/localizing-php-applications-4/>

在[第 3 部分](https://www.sitepoint.com/localizing-php-applications-3)中，您了解了在现实世界中本地化应用程序的一些更重要的方面，比如使用默认的后备语言环境，以及根据消息的用途将它们分成多个域文件。在这一部分中，我将向您展示 gettext 最强大的功能——处理复数形式。gettext 的复数形式特性使您能够完美地、专业地本地化您的应用程序。

## 复数形式

最初编写消息显示给用户的早期程序员很快就遇到了问题。当使用可变数字作为信息的一部分时，应使用可数名词的正确形式。例如，说“1 个文件被删除”是正确的，但“30 个文件被删除”是不正确的。单词“file”应该以复数形式出现，因为 30 在英语中是复数。

对于语法情况简单的语言，例如英语，通常使用`if`语句:

```
<?php
if ($i == 1) {
    echo $i . " file deleted.";
}
else {
    echo $i . " files deleted.";
}
```

如果所有的语言都遵循和英语一样的语法数字，那么这样的代码可能就足够了。处理复数形式可能是一件麻烦的事，但人们不会太在意这个问题。但是许多语言有更丰富的语法数字。例如，在波兰语中，不同数字的复数形式不同:

```
1 plik (file)
2-4 pliki
5-21 plików
22-24 pliki 
25-31 plików
...
```

另一个例子是阿拉伯语，它至少有 6 个规则来定义名词的复数形式。显然，在面向多个地区的应用程序中，硬编码的`if`语句不是可持续的解决方案！

幸运的是，gettext 解决这个问题的方法更加简洁。首先，在域的头中提供一个指定复数形式的规则，然后为每种可能的复数形式提供翻译消息。然后，gettext 使用该规则来确定要显示的正确翻译。

## 行动中的复数形式

为了能够在您的领域内使用复数形式，您需要为目录的目标语言编写一个处理复数的规则；这条规则将包含在 PO 文件的头中，告诉 gettext 在查找翻译时如何处理复数形式。

为波兰语地区(`pl_PL`)创建一个新目录，创建`LC_MESSAGES`目录和`messages.po`，如本文前面部分所述，这样现在您的 Hello World 脚本就支持波兰语了。

使用文本编辑器打开`messages.po`,在标题后添加以下一行:

```
"Plural-Forms: nplurals=3;plural=n==1 ? 0 : n%10>=2 && n%10=20) ? 1 : 2;n"
```

并将以下内容附加到消息行:

```
msgid "%d file deleted."
msgid_plural "%d files deleted."
msgstr[0] "%d plik został usunięty."
msgstr[1] "%d pliki zostały usunięte."
msgstr[2] "%d plików zostało usuniętych."
```

保存文件，在 Poedit 中打开，编译并关闭它。然后，转到`test-locale.php`并添加以下几行:

```
<?php
echo "<br>";
echo ngettext("%d file deleted.", "%d files deleted.", 1);
echo "<br>";
echo ngettext("%d file deleted.", "%d files deleted.", 2);
echo "<br>";
echo ngettext("%d file deleted.", "%d files deleted.", 5);
```

现在，当您运行带有参数`lang=pl_PL`的脚本时，您应该会看到最后三行以波兰语显示:

```
1 plik został usuniętye.
2 pliki zostały usunięte.
5 plików zostało usuniętych.
```

## 复数形式规则

让我们仔细看看添加到 header 部分的复数形式规则:

```
"Plural-Forms: nplurals=3;plural=n==1 ? 0 : n%10>=2 && n%10=20) ? 1 : 2;n"
```

规则以标签“复数形式”开始，它告诉 gettext 应该使用下面的规则来决定给定数字`n`时使用哪种形式。“nplurals=3”告诉 gettext 在这个区域中每个名词有三种复数形式。然后用分号将其与规则的其余部分隔开。

规则的下一部分以“plural=”开始，是描述复数形式的选择标准的表达式。它使用典型的 C 语言语法，但有一些例外:不允许负数，数字必须是十进制的，唯一允许的变量是`n`。表达式中允许空格，但不允许反斜杠转义的换行符。

每当调用函数`ngettext()`、`dngettext()`或`dcngettext()`中的一个时，将对提供的表达式进行计算。传递给这些函数的数值随后被变量`n`的所有使用所替代。结果值必须大于或等于零，但小于作为`nplurals`的值给出的值。例如，如果`nplurals`表示有三种复数形式，那么复数表达式的值不应该是 5。

由复数表达式计算的最终整数指定为特定的`n`值选择哪种复数形式，因此，例如，如果在替换`n=1`时，表达式返回的整数为 0，则使用复数形式“0”，如果它等于 1，则使用复数形式“1”，依此类推。

用文字说复数形式规则:如果`n`等于 1，则使用形式“0”，即 *plik* 。否则，如果`n`除以 10 返回 2-4 之间的值，如果`n`除以 100 返回小于 10 或大于或等于 20 的值，则使用表格“1”作为 *pliki* ，否则使用表格“2”作为 *plików* 。

通过阅读 GNU gettext 文档，您可以找到各种语系的复数形式规则，以及关于如何在您的领域中翻译复数形式的更多信息。具体来说，复数形式和翻译复数形式部分将是最有帮助的。

## 摘要

在本地化系列的这一部分中，您看到了 gettext 最强大的特性之一。每当需要呈现多元化信息时，试图编写自己的内联`if`语句的程序员很快就会发现这种方法既麻烦又脆弱。Gettext 抽象了逻辑，因此您可以保持代码整洁。您需要做的就是确定正确的表达式来计算您翻译的每种语言的复数形式索引，提供 msgid 和 msgid_plural 对，然后提供一组索引的 msgstrs，gettext 可以从中选择，其余的由 gettext 处理。

本系列的下一部分(也是最后一部分)将向您展示如何将从 PHP 代码中提取 msgid 字符串的过程自动化到一个模板文件中，您可以从该模板文件中生成您自己的语言环境的目录。

<small>图片 via[sgame](http://www.shutterstock.com/gallery-62870p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")</small>

## 分享这篇文章