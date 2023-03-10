# 关于 PHP 和 Web 服务的有趣评论

> 原文：<https://www.sitepoint.com/tantalizing-remark-on-php-and-web-services/>

戴维·沙菲克发表了一篇[关于从 PHP 代码生成 WSDL 的诱人评论](http://pixelated-dreams.com/blog/archive/000040.html)，与他正在做的一些工作有关。除了这个例子之外，还没有什么值得看的。

这为什么有意思？答案需要一点解释…

如果你读过 [web 服务揭秘](https://www.sitepoint.com/article/web-services-demystified)，你会知道 WSDL (web 服务描述语言)是一种 XML 标记，它允许你以计算机可以使用的方式描述 Web 服务。在 Zend 关于新的 [PHP SOAP 扩展](http://www.zend.com/php5/articles/php5-SOAP.php)的文章中，通过比较 client1.php 和 client2.php 的例子，你可以看到，当构建一个*客户端*到一个 web 服务时，它减少了许多手工工作。

所以对于构建 web 服务客户端，当使用 WSDL 时，用 PHP(和其他动态类型语言)非常简单。然而，就 WSDL 一代而言，用 PHP 构建服务器就不一样了。

在 WSDL，当描述一个 SOAP 方法接受的参数和它返回的值时，你应该使用 XML Schema。XML Schema 是“强类型的”——它拥有所有的基本类型(如 string、int 等)。)正如你在这里看到的，从中你可以构建复杂的类型来表示像对象和散列(关联数组)这样的东西。XML schema 唯一不“做”的是索引数组，它是在 WSDL 定义的。

在这一点上，我可以痛骂 SOAP / WSDL 是一场灾难，但是我不会，除了一个评论:如果你曾经在评估是否使用 SOAP 的情况下，从研究 SOAP 代表什么开始(在“简单对象访问协议”和“面向服务的架构”之间的某个地方，你可能开始紧张)。

总之——动态类型语言(在源代码中显式声明类型的语言)的问题是如何从代码中自动生成 WSDL？作为一个 web 服务的开发者，理想情况下，你希望能够用 PHP 编写你的代码，然后让一些程序为你生成 WSDL，以节省时间，消除错误等。

例如，在像 Java 或 C#这样的语言中，你被迫声明类型，这没有问题；

 `public class Calculator {
public int add(int i1, int i2) {
return i1 + i2;
}
public int subtract(int i1, int i2) {
return i1 - i2;
}
}`

add 和 subtract 方法声明它们接受什么类型的参数以及它们的返回值，所以这只是使用一个工具来解析源代码并生成 WSDL 的问题。但是在 PHP 中考虑同样的情况；

 `class Calculator {
function add($i1, $i2) {
return $i1 + $i2;
}
function subtract($i1, $i2) {
return $i1 - $i2;
}
}`

虽然看着这段代码，你我可能都很清楚，所涉及的值应该都是整数，但是一个分析这段代码的程序是如何计算出来的呢？

更有趣的是，PHP 允许您根据运行时环境接受和返回完全不同的类型。例如，我可以修改上面的 add 方法，允许我添加元素或两个数组；

 `class Calculator {
function add($i1, $i2) {
if ( is_array($i1) && is_array($i2) ) {
$i1count = count($i1);
if ( $i1count == count($i2) ) {
$sums = array();
for ($i=0; $i < $i1count; $i++) { $sums[$i] = $i1[$i] + $i2[$i]; } return $sums; } else { return FALSE; } } else { return $i1 + $i2; } } // ... }`

add 方法返回的值现在取决于我给它的值。在这种情况下，试图自动生成 WSDL 显然会有问题——解析器需要对 PHP 的语法有非常深刻的理解。

顺便说一句，当涉及到编写“编译器”将 PHP 转换成另一种更快的形式时，也存在同样的问题(见 George Schlossnagle 的 [Roadsend PCC，micro-review](http://www.schlossnagle.org/~george/blog/archives/249_Roadsend_PCC_a_microreview.html) )。

无论如何，观看[这个空间](http://pixelated-dreams.com/blog/)获得更多关于从 PHP 生成 WSDL 的信息。如果戴维能成功就太好了。

## 分享这篇文章