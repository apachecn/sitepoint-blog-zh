# 使用过滤函数的输入验证

> 原文：<https://www.sitepoint.com/input-validation-using-filter-functions/>

在这篇文章的开始，我想感谢你能坚持到现在。我完全意识到“使用过滤函数进行输入验证”并不是世界上最性感的文章标题！

PHP 中的过滤函数可能并不吸引人，但是如果你学会如何正确使用它们，它们可以提高代码的稳定性、安全性甚至可维护性。

在这篇文章中，我将解释为什么输入验证是重要的，为什么使用 PHP 内置函数来执行输入验证是重要的，然后抛出一些例子(即使用`filter_input()`和`filter_var()`)，讨论一些潜在的陷阱，并以一个漂亮、有趣的行动号召结束。听起来不错吧？我们走吧！

## 为什么输入验证很重要

输入验证是确保代码安全的最重要的事情之一，因为输入通常是应用程序中您无法直接控制的事情。因为你不能控制它，你不能信任它。

不幸的是，作为程序员，我们经常只考虑我们希望它们如何工作。我们不会考虑其他人如何让它们工作——无论是出于好奇、无知还是恶意。

如果不验证用户输入，我不打算详细描述你会遇到的麻烦；这个网站上有一篇非常好的文章，叫做 [PHP 安全:跨站点脚本攻击](https://www.sitepoint.com/php-security-cross-site-scripting-attacks-xss/ "PHP Security: Cross-Site Scripting Attacks (XSS)")，如果你想读的话。但是我要说的是，验证您的输入是确保您编写的代码按预期执行的第一步。

也许你是从另一种语言进入 PHP 的，你可能会想，“这以前从来不是问题，所以我为什么要关心？”验证成为问题的原因是因为 PHP 是松散类型的。这使得 PHP 对于某些事情来说很棒，但是它也使得像数据验证这样的事情变得有点棘手，因为你几乎可以将任何东西传递给任何东西。

## 为什么使用内置方法很重要

为了让验证变得简单一点，从 PHP 5.2.0 开始，我们现在可以使用`filter_input()`和`filter_var()`函数。我将很快更详细地讨论它们，但首先我想谈谈为什么我们应该使用 PHP 提供的功能，而不是依赖我们自己的方法或第三方工具。

当您使用自己的验证方法时，您通常会陷入在设计其他功能时可能会陷入的相同陷阱中:您会考虑您想要考虑的边缘情况，而不一定是所有可能用于掩饰特定输入的不同向量。另一个问题是，如果你和我一样，任何处理手工验证代码的代码评审的前 10 分钟都花在了 tutting 上，因为程序员没有做你会做的事情。这可能导致程序员花更多的时间学习代码库和阅读内部文档，而这些时间本可以用来编码。

有些人不自行开发，而是选择第三方解决方案。有一些不错的工具，过去我曾使用过 [OWASP ESAPI](http://code.google.com/p/owasp-esapi-php/ "owasp-esapi-php - OWASP ESAPI for PHP - Google Project Hosting") 进行一些额外的验证。这些可能比手工开发的解决方案要好，因为有更多的人关注它们，但是你会遇到在项目中引入第三方代码的问题。同样，这增加了学习代码库和阅读额外文档而不是编码的时间。

由于这些原因，使用本机函数更好；此外，因为这样的函数被嵌入到语言中，这意味着我们有一个地方可以存放所有的 PHP 文档。新开发人员将有更大的机会了解代码是什么以及如何最好地使用它。这样做的结果是更容易支持。

希望到现在为止，我已经让您相信了验证的重要性，并且使用 PHP 函数来帮助您实现验证需求是一个好主意。如果你不服气，可以留下评论，大家一起讨论。

## 一些例子

[filter_input()](http://php.net/filter_input "PHP: filter_input - Manual") 函数是在 PHP 5.2.0 中引入的，它允许你通过名字获取一个外部变量并过滤它。这在处理`$_GET`和`$_POST`数据时非常有用。

让我们以一个简单的页面为例，该页面读取从 URL 传入的值并对其进行处理。我们知道这个值应该是一个介于 15 和 20 之间的整数。一种做法是这样的:

```
<?php
if (isset($_GET["value"])) {
    $value = $_GET["value"];
}
else {
    $value = false;
}
if (is_numeric($value) && ($value >= 15 && $value <= 20)) {
    // run my code
}
else {
    // handle the issue
}
```

这是一个非常基本的例子，我们已经写了更多我想看的内容。

首先，因为我们不能确定是否设置了`$_GET`，所以代码会执行适当的检查，这样脚本就不会出错。

其次，`$value`现在是一个“脏”变量，因为它是从一个`$_GET`值直接赋值的。我们需要注意不要在代码中的任何地方使用`$value`,以防破坏任何东西。

还有一个问题是 16.0 是有效的，因为`is_numeric()`允许它。

最后，我们还有一个问题，那就是`if`语句有点难以理解，而且当你跟踪代码时，它是一个额外的逻辑。

将上面的例子与下面的进行比较:

```
<?php
$value = filter_input(INPUT_GET, "value", FILTER_VALIDATE_INT,
    array("options" => array("min_range" => 15, "max_range" => 20)));
if ($value) {
    // run my code
}
else {
    // handle the issue
}
```

这难道不会让你感到温暖和模糊吗？

`filter_input()`处理未设置的`$_GET`值，因此您不必担心脚本是否接收到正确的信息。

你也不必担心`$value`被弄脏，因为它在被赋值之前已经被验证过了。

现在请注意，16.0 不再有效。

最后，我们的逻辑不再复杂。这只是快速检查一个真值(`filter_input()`如果验证失败将返回 false，如果`$_GET["value"]`没有设置将返回 null)。

显然，在现实世界的设置中，您可以将数组提取到存储在某个配置文件中的变量中，这样事情就可以改变，甚至不需要进入业务逻辑。太棒了。

现在您可能会想，这对于获取几个`$_GET`或`$_POST`变量的简单脚本来说可能是有用的，但是对于在函数或类内部使用呢？幸运的是，我们有 [filter_var()](http://php.net/filter_var "PHP: filter_var - Manual") 来解决这个问题。

`filter_var()`函数是与`filter_input()`同时引入的，做的事情也差不多。

```
<?php
// This is a sample function, do not use this to actually email,
// that would be silly.
function emailUser($email) {
    mail($email, "Here is my email", "Some Content");
}
```

这里的危险在于，没有什么可以阻止`mail()`函数尝试向`$email`中可能存储的任何值发送电子邮件。这可能会导致电子邮件无法发送，或者在最坏的情况下，某些东西可能会恶意使用该功能。

我见过有人对`mail()`的结果进行检查，这可以很好地查看函数是否成功完成，但是当一个值返回时，损害已经造成了。

类似这样的事情要理智得多:

```
<?php
// This is a sample function, do not use this to actually email,
// that would be silly.
function emailUser($email) {
    $email = filter_var($email, FILTER_VALIDATE_EMAIL);
    if ($email !== false) {
        mail($email, "Here is my email", "Some Content");
    }
    else {
        // handle the issue invalid email address
    }
}
```

包括上述例子在内的许多例子的问题在于，它们是基本的。你可能会想`filter_var()`或`filter_input()`除了基本的检查之外不能用于任何事情。引入这些函数的优秀人员考虑到了这一点，并允许您向这些函数传递一个名为`FILTER_CALLBACK`的过滤器。

`FILTER_CALLBACK`允许您传入您创建的函数，该函数将接受被过滤的变量作为输入——这是您开始享受乐趣的地方，因为您可以开始将自己的业务逻辑应用到您的过滤中。

## 一些潜在的陷阱

这些函数非常棒，它们允许你做一些非常强大的过滤，正如我们已经讨论过的，可以帮助提高代码的安全性和可靠性。然而，有一些潜在的缺点，如果我没有指出来，我会觉得我是失职的。

主要的缺陷是，函数的好坏取决于您应用的过滤器。以使用电子邮件验证的最后一个例子为例——`FILTER_VALIDATE_EMAIL`处理电子邮件地址的方式在 5.2.14 和 5.3.3 之间发生了变化，即使假设您的所有应用程序都运行在同一版本的 PHP 上，也有您可能没有预料到的技术上有效的电子邮件地址。请确保您了解您正在使用的过滤器。

第二个陷阱是，人们认为如果他们放入一些过滤器，那么他们的代码就是安全的。过滤你的变量有一定的帮助，但是它不能保证你的代码 100%安全。我很想多谈谈这个，但是这超出了本文的范围，而且我的字数已经很高了！

## 结论

希望你已经发现这个 PHP 输入验证的介绍很有用。现在，是呼吁行动的时候了！

我想让你在代码中取一个函数，就一个，看看当你传入不同的数据类型和不同的值时会发生什么。然后，我希望您应用这里讨论的一些过滤方法，看看您的代码在执行方面是否有所不同。我很想知道你在评论中的表现。

图片 via[Chance Agrella](http://freerangestock.com/view_photog.php?photogid=2 "freerangestock.com | Free Stock Photos - Totally Free Commercial Photography and Textures")/[Freerangestock.com](http://freerangestock.com "freerangestock.com | Free Stock Photos - Totally Free Commercial Photography and Textures")

## 分享这篇文章