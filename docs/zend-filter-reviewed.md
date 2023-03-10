# Zend_Filter 已审核，黑名单/白名单过滤器

> 原文：<https://www.sitepoint.com/zend-filter-reviewed/>

我喜欢 [Zend 框架](http://framework.zend.com/)的 [Zend_Filter 类](http://framework.zend.com/svn/framework/trunk/library/Zend/Filter.php)。它基本上是一组验证不可信数据的方法。尽管两个可以说是最重要的特性 isEmail()和 isUri()(后者可以用 Zend_Uri 来解决)仍然缺失，但整个事情看起来已经很有希望了。这里有一些关于包装的想法:

*   移除 isGreaterThan()和 isLessThan()。这就是我们有“< " and ">”运算符的原因。我能理解设计者交付一套完整测试的意图，但是这些只是膨胀了 Zend_Filter 和用户的代码。也没有 isEqualTo()。
*   isDate()看起来像一个存根。这个应该换成更高级的。
*   清理 isHostname()的代码。
*   方法名`isRegex()`让我觉得它检查参数是否是有效的正则表达式。既然模式匹配是一种特殊的过滤方式，我就放弃“is”前缀，称它为 match()。
*   不知道 isName()对任何外来名字是否完全准确。此外，它可以很容易地离开，因为它是白名单过滤的工作。见下文。
*   isPhone()的国际支持。我可以为它提供一个瑞士实现，只是让我知道。顺便说一下，在输入上应用 self::getDigits()而不是 ctype_digit 检查。
*   让我们再给 Zend_Filter 添加三个类方法。第一种方法是对字符串进行转义，以便在正则表达式中安全使用:

```
 public static function getRegexEscaped($input) {
  $output = '';
  for($i = 0; $i < strlen($input); $i++) {
    $output .= 'x'.bin2hex($input{$i});
  }
  return $output;
} 

```

*   下一个通过字符白名单验证字符串:

```
 public static function getWhitelisted($input, $allowed_chars = '', $allow_alpha = true, $allow_numeric = true) {
  $regex = '%[^'.($allow_alpha ? '[:alpha:]' : '').($allow_numeric ? 'd' : '').self::getRegexEscaped($allowed_chars).']%';
  return preg_replace($regex, '', $input);
} 

```

*   当有白名单时，也应该有黑名单。仔细想想，这应该用 str_replace()来实现。

```
 public function getBlacklisted($input, $forbidden_chars) {
  $regex = '%['.self::getRegexEscaped($forbidden_chars).']%';
  return preg_replace($regex, '', $input);
} 

```

例如，我们可以使用更灵活的白名单方法，而不是 Zend_Filter::isName。

```
 /* We only allow letters, spaces and dashes in names */
$name = Zend_Filter::getWhitelisted($name, " -", true, false); 

```

## 分享这篇文章