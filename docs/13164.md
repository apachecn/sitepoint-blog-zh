# 使用正则表达式子模式的标记化

> 原文：<https://www.sitepoint.com/tokenization-using-regular-expression-sub-patterns/>

不久前我在这个博客上写了一些关于常规表情的东西。虽然这还没有完成，一个迷你正则表达式的例子——没有什么惊天动地的，但如果你还没有看过，这是一个有用的技术。

在一个真实世界的例子的提示下，大多数正则表达式引擎的一个经常被忽略的特性是子模式如何相对容易地产生 T2 记号赋予器 T3。问题？我需要匹配字符串中的单词“Canton”、“Region”或“Group”，并根据匹配的单词执行后续操作。

在瑞士处理四种主要语言(德语、法语、意大利语和英语)，这变得有点有趣；“Canton”在德语中翻译为“Kanton”，在意大利语中翻译为“Cantone”，而“Region”在意大利语中是“Regione”。而 Group 在德语、法语和意大利语中分别是“Gruppe”、“Groupe”和“Gruppo”。把它们组合成三个简单的正则表达式。

*   广州:`/cantone?|kanton/i`
*   地区:`/regione?/i`
*   组:`/groupe?|grupp(?:o|e)/i`

现在，在检查一些输入字符串时，我可以试着针对该字符串单独测试这些正则表达式，但这 a)效率低下，b)可能导致代码变长。相反，我使用子模式创建一个正则表达式:`/(cantone?|kanton)|(regione?)|(groupe?|grupp(?:o|e))/i`…然后在匹配完成后找出匹配的子模式。

注意，从技术上讲，这个问题实际上不是一个标记化的问题，而只是用一个通用名称对输入进行分类，但是这种技术可以很容易地扩展。例如，在 PHP 中，解决方案是由 [preg_match()](http://www.php.net/preg_match) 的第三个参数提供的；

```
 $inputs = array( 'Kanton Zuerich', 'Frauenfeld Regione', 'Fricktal Gruppe');

foreach ( $inputs as $input ) {
    preg_match("/(cantone?|kanton)|(regione?)|(groupe?|grupp(?:o|e))/i", $input, $matches);
    print_r($matches);
} 

```

我得到的输出是。

```
 Array
(
    [0] => Kanton
    [1] => Kanton
)
Array
(
    [0] => Regione
    [1] => 
    [2] => Regione
)
Array
(
    [0] => Gruppe
    [1] => 
    [2] => 
    [3] => Gruppe
) 
```

注意这个数组的第一个元素总是我匹配的，而索引为 1+的元素对应于我匹配的子模式的位置，在模式中从左到右——这可以用来告诉我我实际匹配了什么，例如；

```
 $inputs = array( 'Kanton Zuerich', 'Frauenfeld Regione', 'Fricktal Gruppe');
$tokens = array('canton','region','group'); // the token names

foreach ( $inputs as $input ) {

    if ( preg_match("/(cantone?|kanton)|(regione?)|(groupe?|grupp(?:o|e))/i", $input, $matches) ) {

        foreach ( array_keys( $matches) as $key) {
            if ( $key == 0 ) { continue; } // skip the first element

            // Look for the subpattern we matched...
            if ( $matches[$key] != "" ) {
                printf("Input: '%s',  Token: '%s'n", $input, $tokens[$key-1]);
            }
        }
    }
} 

```

这给了我这样的输出:

```
 Input: 'Kanton Zuerich',  Token: 'canton'
Input: 'Frauenfeld Regione',  Token: 'region'
Input: 'Fricktal Gruppe',  Token: 'group'
```

…因此，我现在能够将输入分类到一组已知标记中的一个，并做出相应的反应。大多数正则表达式。API 提供了一些类似的东西，例如 Python 中的相同(并且更干净)，这是我在这个问题上实际使用的；

```
 import re

p = re.compile('(cantone?|kanton)|(regione?)|(groupe?|grupp(?:o|e))', re.I)
inputs = ('Kanton Zuerich', 'Frauenfeld Regione', 'Fricktal Gruppe')
tokens = ('canton','region','group')

for input in inputs:
    m  = p.search(input)
    if not m: continue
    for group, token in zip(m.groups(), tokens):
        if group is not None:
            print "Input: '%s', Token: '%s'" % ( input, token ) 

```

可以使用 [list comprehensions](http://en.wikipedia.org/wiki/List_comprehension#In_Python) 进一步减少，但我不认为这有助于可读性。

另一个问题是让你感受一下如何应用这种技术。假设您想要解析一个 HTML 文档，并列出它包含的块级和内联级标签的子集。你可以用两个子模式来实现这一点，例如`(</?(?:div|h[1-6]{1}|p|ol|ul|pre).*?>)|(</?(?:span|code|em|strong|a).*?>)`(注意这个正则表达式是为了迎合 python 贪婪的思想——你需要为 PHP 修改它),导致类似这样的东西，这就是 python；

```
 p = re.compile('(</?(?:div|h[1-6]{1}|p|ol|ul|pre).*?>)|(</?(?:span|code|em|strong|a).*?>)')

for match in p.finditer('foo <div> test <strong>bar</strong> test 1</div> bar'):
    print "[pos: %s] matched %s" % ( match.start(), str(match.groups()) ) 

```

对`match.groups()`的调用返回一个 tuple，它告诉你匹配了哪个子模式，而`match.start()`告诉你匹配的字符在文档中的位置，允许你从文档中提取子字符串。

## 分享这篇文章