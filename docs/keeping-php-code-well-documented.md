# 保持您的 PHP 代码有良好的文档记录

> 原文：<https://www.sitepoint.com/keeping-php-code-well-documented/>

## 介绍

几乎每个 PHP 开发人员都会写注释和实际代码。但是语言本身并没有规定如何做到这一点。你只需要用一些特定的标签把它们包起来，然后你就可以写任何你想要的内容了。那么究竟应该在注释块中放些什么才能让它们有用呢？代码的哪些部分应该被记录，哪些不应该？在这篇文章中，我将介绍一些重要的规则，这些规则可以帮助你保持你的 PHP 代码文档清晰易懂。

### 1.编写能自我解释的代码

首先，即使没有添加任何注释块，您编写的代码也可以作为一份很好的文档。在将逻辑转换成代码片段时，您可以做很多事情来使代码清晰。以下是几个例子:

**变量、函数和类的命名**
你几乎可以用任何你想要的方式来命名你的代码，你可以用它来保持代码的可理解性。只要记得选择明确的名字，不要编造任何奇怪的缩写或使用可能有歧义的名字。如果你的变量代表一个`VeryImportantCustomer`类的实例，就称它为`$veryImportantCustomer`，而不是`$customer`、`$vimpCust`或`$tempcustomer`。不要害怕在较长的名称中出现拼写错误，因为 IDE 可能会警告您代码中有未使用的变量或其他不一致的地方。我确信使用正确的命名会对弄清楚代码中发生了什么有很大帮助。这是一个简单的规则，但在你的日常工作中可能很容易忘记。

**类型提示**
PHP [允许你](http://www.php.net/manual/en/language.oop5.typehinting.php)将类/接口名或者`array` / `callable`关键字放在函数参数旁边。它可以防止你进行错误的函数调用，但是对于任何阅读你的代码的人来说，它也是一条重要的信息。你不必检查函数体来了解如何调用函数。您还可以快速检查不同的函数和类如何在彼此之间传递值。请记住，您的 IDE 可能会解释类型提示，并使用它们来描述在您工作时显示的弹出窗口或提示中的功能。

**方法可见性**
另一个值得一提的概念是方法[可见性](http://www.php.net/manual/en/language.oop5.visibility.php)。据说给类方法分配适当的可见性是编写高质量面向对象代码的重要部分。一方面，它显示了哪些代码代表了应该留在类中而不应该向应用程序中的其他类公开的逻辑部分。另一方面，它向公共访问公开了某些类方法，这样就可以从类外部调用它们，并与应用程序的其他部分进行通信。

如果您编写的代码包括设置适当的方法可见性，其他开发人员将很快知道如何使用您开发的类。他们会看到有一些公共方法可以在他们的代码中引用。他们还会注意到你写的逻辑的哪些部分是留给私有类方法处理的，可能不应该被触及。

同样，您的 IDE 也会解释这些提示。您使用的编辑器可能会向您显示一个类方法列表，以及它们的可见性。请看下图，注意方法名称旁边的锁图标:

![](img/9aca185b80090dbf422ed4aa1b829b07.png)

## 2.保持平衡

当然你可能会觉得代码本身并不总是足够清晰，需要额外的解释。当您正在实现业务逻辑的复杂部分、执行复杂的计算或者只是使用乍一看难以理解的命令(如正则表达式模式、数组转换等)时，尤其如此。).在这种情况下，写一个简短的评论肯定会有助于了解正在发生的事情。

另一方面，注释块不应该弥补写得不好的代码。如果您的代码包含太多的循环或控制结构，甚至您不花几分钟分析就不知道它是如何工作的，让它像那样带有几行注释并不是最好的解决方案。你应该努力重构代码，而不是试图用注释来解释。

除了复杂的代码块之外，还有一些代码部分是清晰的，不代表任何复杂的逻辑。一些开发者倾向于为他们的应用程序的这些部分放置评论块，在我看来这是不必要的。让我给你看一个例子:

```
<?php class  Deposit  {  /**
         * The deposit owner.
         *
         * @var string
         */  private $_owner;  /**
         * The deposit amount.
         *
         * @var float
         */  private $_amount;  /**
         * The date when the deposit was opened.
         *
         * @var DateTime
         */  private $_dateOpened;  /**
         * Class constructor.
         *
         */  public  function __construct()  {  //...  }  /**
         * Sets the deposit owner.
         *
         * @param string $owner The deposit owner name.
         */  public  function setOwner($owner)  { $this->_owner = $owner;  }  ?>
```

请记住，阅读您代码的人通常是开发人员(或者至少我是这样认为的)，因此他/她将能够发现`Deposit`类的`_owner`属性代表存款所有者。这就是为什么我认为在代码的这些部分添加额外的注释甚至会降低可读性，而不是以任何方式帮助读者。

不仅在类属性定义或典型方法(如构造函数、getters 或 setters)中，注释在代码的其他简单部分通常也是不必要的。只看下面的例子:

```
<?php public  function showUserDetails()  { $userId = $this->Session->read('userId'); $userData = $this->getUserData($userId);  if(!$user->isActive())  {  throw  new  Exception("The user account hasn't been activated.");  }  //...  }  ?>
```

我相信您可以很容易地理解上面给出的代码部分发生了什么。但是如果您想要注释代码，它可能看起来像这样:

```
<?php /**
 * Shows the details of the user that is currently
 * logged in.
 *
 */  public  function showUserDetails()  {  //get the current user from session $userId = $this->Session->read('userId');  //load the user data from database $userData = $this->getUserData($userId);  //check if the user has an active account  if(!$user->isActive())  {  throw  new  Exception("The user account hasn't been activated.");  }  //...  }  ?>
```

事实上，添加到方法中的注释包含了与代码中使用的几乎相同的单词。就像我们之前说过的，正确的命名使你的代码易于理解和阅读。在我看来，上面例子中显示的方法不需要任何额外的注释，因为一切都是由代码本身描述的。当然，我仍然鼓励你在你的应用程序中更复杂和需要额外解释的部分写评论。

## 3.还记得文档块吗

正如您在上面的代码示例中看到的，一些注释块包含以`@`字符开头的特定关键字。我用`@var`来描述类属性的类型，用`@param`来通知方法参数的类型。您还可以使用`@return`标签，它告知函数返回的值的类型。可以使用其他标签来描述关于应用程序或其部分的一些一般信息(如作者、包、许可证类型)。您可以在 Moshe Teutsch 撰写的[PhpDoc 简介](https://www.sitepoint.com/introduction-to-phpdoc/)文章中阅读更多关于 doc 块的内容。

Doc blocks 标签包含代码本身不能包含的信息。指定类属性或函数返回值的类型特别有用，因为它可以被大多数 ide 解析并显示在提示中。当然，你也可以在 doc 块中插入一个自定义文本，作为函数或类的文档。如果您的项目需要从代码之外获得它的文档，这可能特别重要。在这种情况下，您可以利用分析 doc 块的应用程序，并根据它们的内容生成整个应用程序的文档。阅读布鲁诺·什科沃茨的[用 ApiGen](https://www.sitepoint.com/generate-documentation-with-apigen/) 生成文档的文章，了解关于这种方法的更多信息。

如果代码易于理解，我不需要生成扩展文档，我只需要保留提供变量类型和返回值信息的标签。结果，代码不会变得过于复杂:

```
<?php class  Deposit  {  /**
         * @var string
         */  private $_owner;  /**
         * @var float
         */  private $_amount;  /**
         * @var DateTime
         */  private $_dateOpened;  //...  /**
         * @param string $owner
         */  public  function setOwner($owner)  { $this->_owner = $owner;  }  ?>
```

## 摘要

在本文中，我介绍了一些关于如何在 PHP 应用程序中维护代码文档的技巧。我认为，如果你编写的代码清晰明了，并且能自我解释，那么注释就没有必要了。放置注释的合适位置是当你实现一些复杂的逻辑或者使用人类不太可读的命令时。同样值得记住的是插入描述变量类型或函数返回值的 doc 块标记，因为这些信息不能包含在代码本身中。如果您需要维护更详细的项目文档，也要在文档块中放置适当的描述。

请随意对文章中提出的观点发表意见，或者通过 [Google+](https://plus.google.com/112138584619019192671?rel=author) 联系我。如果你有不同的看法或在工作中使用不同的规则，请告诉我们！

## 分享这篇文章