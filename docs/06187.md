# 用 Pspell 查找并纠正拼写错误的单词

> 原文：<https://www.sitepoint.com/find-correct-misspelled-words-pspell/>

我们每个人在谷歌搜索中都犯过拼写错误，例如:“另类音乐”。这样做的时候，你可能已经注意到谷歌试图通过显示“你是指另类音乐吗？”来帮助你。如果你的网站有搜索功能，如果没有或很少找到结果，指出拼写错误是一个非常有用的功能，特别是如果访问者的糟糕英语会让你错过销售。幸运的是，PHP 的 Pspell 模块允许检查单词的拼写，并从默认词典中建议替换词(您也可以创建自定义词典)。

首先，我们需要检查是否安装了 Pspell:

```
<?php
$config_dic= pspell_config_create ('en');
```

如果你得到一个错误，它不是。在 Linux 系统上，按照这些指令来解决问题。

**使用默认词典**

这里有一个小函数可以帮助你理解 Pspell 是如何工作的:

```
<?php
function orthograph($string)
{
    // Suggests possible words in case of misspelling
    $config_dic = pspell_config_create('en');

    // Ignore words under 3 characters
    pspell_config_ignore($config_dic, 3);

    // Configure the dictionary
    pspell_config_mode($config_dic, PSPELL_FAST);
    $dictionary = pspell_new_config($config_dic);

    // To find out if a replacement has been suggested
    $replacement_suggest = false;

    $string = explode('', trim(str_replace(',', ' ', $string)));
    foreach ($string as $key => $value) {
        if(!pspell_check($dictionary, $value)) {
            $suggestion = pspell_suggest($dictionary, $value);

            // Suggestions are case sensitive. Grab the first one.
            if(strtolower($suggestion [0]) != strtolower($value)) {
                $string [$key] = $suggestion [0];
                $replacement_suggest = true;
            }
        }
    }

    if ($replacement_suggest) {
        // We have a suggestion, so we return to the data.
        return implode('', $string);
    } else {
        return null;
    }
}
```

要使用这个函数，只需向它传递一个字符串参数:

```
<?php
$search = $_POST['input'];
$suggestion_spell = orthograph($search);
if ($suggestion_spell) {
    echo "Try with this spelling : $suggestion_spell";
}
```

如果您提交给 Pspell 的字符串是“这是我拼错的单词”，前面的脚本将返回:“试试这个拼写:这是我拼错的单词。”然而，Pspell 并不是一个奇迹创造者，尤其是当你自动使用第一个建议的拼写选项时！为了获得最佳效果，您可以使用 Pspell 提供的所有建议。以下脚本返回围绕单词“lappin”的二十个建议:

```
<?php
$dict = pspell_new ("en");
if (!pspell_check ($dict, "lappin")) {
    $suggestions = pspell_suggest ($dict, "lappin");
     foreach ($suggestions as $suggestion) {
        echo "Did you mean: $suggestion?<br />";
     }
}
```

您必须配置一个字典来初始化 Pspell。为此，针对字典的配置文件创建一个描述符，更改该描述符的一些选项，然后使用配置字典为真实字典创建第二个描述符。如果这听起来有点复杂，不要担心:代码很少改变，您通常可以从另一个脚本中复制它。但是，在这里我们会一步一步来研究。下面是配置字典的代码
:

```
 // Suggests possible words in case of misspelling
    $config_dic = pspell_config_create('en');

    // Ignore words under 3 characters
    pspell_config_ignore($config_dic, 3);

    // Configure the dictionary
    pspell_config_mode($config_dic, PSPELL_FAST);
```

`$config_dic`是控制字典选项的初始模板。您必须加载`$config_dic`中的所有选项，然后使用它来创建字典。`pspell_config_create()`创建英语词典。要使用英语并指定您更喜欢美式拼写，请将“en”指定为第一个参数，将“American”指定为第二个参数。`pspell_config_ignore()`表示您的词典将忽略 3 个字母或更少的所有单词。最后，`pspell_config_mode()`向 Pspell 指示运行模式:

PSPELL_FAST 是一种快速方法，将返回最少的建议。
·PSPELL _ NORMAL 返回正常速度下的平均建议数。
·PSPELL _ SLOW 提供了所有可能的建议，虽然这个方法执行拼写检查需要一些时间。我们仍然可以使用其他配置选项(例如，添加一个自定义字典，我们将在后面看到)，但是由于这是一个快速检查，我们将简单地用下面的代码行创建字典:

```
 $dictionary = pspell_new_config($config_dic);
```

从这一点上你可以用两种方式使用字典:
1。如果“单词”在字典中，则`pspell_check($dictionary, "word")`返回`true`。
2。`pspell_suggest($dictionary, "word")`如果“单词”不在字典中，则返回建议单词的数组(该数组的第一个元素是最有可能的候选)。获得的单词数量各不相同，但使用 PSPELL_SLOW 获得的单词数量更多，而使用 PSPELL_FAST 获得的单词数量更少。

现在字典已经准备好了，我们剪切作为参数传递的字符串以获得单词数组:“here my sentence”变成了由三个元素组成的数组:“here”、“my”和“sentence”。然后，我们使用默认词典检查每个单词的拼写。因为它不喜欢逗号，所以我们在分解字符串之前也删除了它们。如果单词多于三个字符，将进行验证，如果拼写错误，我们将执行以下操作:

1.  我们要求 Pspell 提供一系列的改正建议。
2.  我们采用最可能的建议(数组$suggestion 的第一个元素),并用它替换拼写错误的单词。
3.  我们将`$replacement_suggest`标志设置为 true，这样在处理循环结束时，我们知道我们在`$string`的某个地方发现了拼写错误。在循环结束时，如果有拼写纠正，我们将从纠正后的数组元素中重组字符串，并返回这个链。否则，该函数返回 null，表明它没有检测到拼写错误。

**向 Pspell 添加自定义词典**

如果一个单词不在默认词典中，您可以轻松地添加它。但是，您也可以创建一个自定义词典，与默认词典一起使用。在你的站点上创建一个目录，PHP 有权在其中编写和初始化新的字典。要在服务器的目录路径中创建一个名为`perso.pws`的新字典文件，请使用以下脚本:

```
<?php
$config_dic = pspell_config_create ('en');
pspell_config_personal($config_dic, 'path / perso.pws');
pspell_config_ignore($config_dic , 2);
pspell_config_mode($config_dic, PSPELL_FAST);
$dic = pspell_new_config($config_dic);
```

这是与上一节相同的脚本，但是增加了一个基本内容:调用`pspell_config_personal()`初始化一个个人字典文件。如果这个文件不存在，Pspell 会为您创建一个新文件。您可以使用以下函数向词典中添加任意数量的单词:

```
`pspell_add_to_personal($dic, "word");`
```

只要您没有保存词典，单词就会临时添加到词典中。因此，在插入您想要的单词后，将这一行添加到脚本的末尾:

```
pspell_save_wordlist($dic);
```

然后像上面演示脚本中那样调用`pspell_config_personal()`，你的新字典就准备好了。

## 结论

Pspell 可以通过为您的访问者提供一种自动纠正和注意他们的错别字的方式来帮助您提高转化率。它可以增强搜索体验、论坛提交，以及具有用户提交内容的网站的一般语言准确性。如果你想更深入地了解 Pspell，或者已经以一种有趣的方式实现了它，请在下面的评论中告诉我们！

## 分享这篇文章