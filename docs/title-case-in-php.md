# PHP 中的标题大小写

> 原文：<https://www.sitepoint.com/title-case-in-php/>

PHP 函数 [strtoupper](http://www.php.net/strtoupper) 和 [ucwords](http://www.php.net/ucwords) 分别将字符串中的所有字符和每个单词的首字母大写。然而，在标准 PHP 库中没有实现[标题大小写](http://en.wikipedia.org/wiki/Title_case#Titles)的方法，这涉及到所有单词的大写，除了不是第一个单词的小单词(如连词)。

下面的小教程将提供一个类似于我想出的在 SitePoint 论坛的[突出论坛讨论](https://www.sitepoint.com/forums/)中显示主题标题的解决方案。

首先，我们需要一个列表，列出所有当不是第一个单词时不想大写的单词。我们要关注的词是连词(如 and，or)，介词(in，on)和内部冠词(The，a)。然而，我不是英语语法专家，所以我就称它们为“小单词”。这里是我使用的一些选择。

 `$smallwordsarray = array(
'of','a','the','and','an','or','nor','but','is','if','then','else','when',
'at','from','by','on','off','for','in','out','over','to','into','with'
);`

PHP 中的 [explode](http://www.php.net/explode) 函数可以用来将任意字符串拆分成字符串数组，使用一个字符作为拆分字符。因此，如果我们用一个空格字符(')来拆分，我们可以使用 [explode](http://www.php.net/explode) 来将我们的字符串拆分成单词。

 `$words = explode(' ', 'this is a title');`

$words 变成一个字符串数组，每个字符串代表原始$title 中的一个单词。这里它等于 array('This '，' is '，' a '，' title ')。

我们现在可以分别对每个单词进行操作。我们需要测试每个单词，以确定它是否是我们的“小单词”之一。如果是*不是*小词，或者是第一个词，就要大写。为了依次操作数组的每个成员，我们可以使用 PHP 语言构造 [foreach](http://www.php.net/foreach) 。要检查一个单词是否是我们的一个小单词，我们可以使用 [in_array](http://www.php.net/in_array)

 `foreach ($words as $key => $word)
{
if (!$key or !in_array($word, $smallwordsarray))
$words[$key] = ucwords($word);
}`

注意这里我把值赋给了$words[$key]而不是$word。原因是$word 是由 foreach 语句创建的。修改$word 不会对原始数组$words 造成任何改变。所以我需要修改原始数组$words 中对应于当前数组键的条目。

至此，我们已经拥有了标题中所有单词的数组。那些应该大写的单词已经大写了，剩下的就是把这些单词重新组合成一个字符串。这可以通过[内爆](http://www.php.net/implode)来完成，它的工作方向与[爆炸](http://www.php.net/explode)相反。

 `$newtitle = implode(' ', $words);`

下面是作为函数的整个脚本，欢迎您在应用程序中使用。

 `function strtotitle($title)
// Converts $title to Title Case, and returns the result.
{
// Our array of 'small words' which shouldn't be capitalised if
// they aren't the first word. Add your own words to taste.
$smallwordsarray = array(
'of','a','the','and','an','or','nor','but','is','if','then','else','when',
'at','from','by','on','off','for','in','out','over','to','into','with'
);`

 `//将字符串拆分成单独的单词
$words = explode('，$ title)；

foreach($ words as $ key = > $ word)
{
//如果这个词是第一个，或者不是我们的一个小词，那就大写
//用 ucwords()。
if ($key == 0 或者！in_array($word，$ smallwordsarray))
$ words[$ key]= UC words($ word)；
}

//将单词联接回一个字符串
$newtitle =内爆(' '，$ words)；` 

`return $newtitle;
}`

请注意，如果输入已经包含大写字母，这些字母将会保留。这确保了必须始终大写的字母，如首字母缩写词，将保持不变。

用一个标题试试看，比如

 `echo strtotitle("this is a title");`

结果呢？“这是一个标题”。

顺便说一句，在谷歌上快速搜索发现了 JavaScript 中同样问题的解决方案[。然而，这是代码的噩梦——不必要的复杂。它会分别检查每个字母！你最好把我的脚本移植到 JavaScript 上。](http://www.madirish.net/tech.php?section=1&article=6)

顺便说一下，这里有一个在 ColdFusion 中类似的解决方案。

## 分享这篇文章