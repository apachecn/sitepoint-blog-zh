# PHP 防御性编程的更多技巧

> 原文：<https://www.sitepoint.com/more-tips-for-defensive-programming-in-php/>

这里使用的防御性编程的一般定义(参见第 1 部分，PHP 中关于[防御性编程的概述)是:](https://www.sitepoint.com/defensive-programming-in-php)

> 简而言之，防御性编程是为了预测可能的故障点而进行的编程。目标是在这些可能的问题发生之前规避它们。

这个定义很宽泛。许多人反对防御性编程，但这通常是因为他们看到一些方法被认为是防御性编程。防御性编程不应该被视为避免[测试驱动开发](https://www.sitepoint.com/basic-tdd-new-php-package/)的一种方式，或者是简单地补偿失败并继续前进的一种方式。

[“快速失败”](https://en.wikipedia.org/wiki/Fail-fast)不应被视为防御型编程的反制。这一切都在同一把伞下。这些方法是什么，如果不是预测你的程序可能失败的方法，或者是防止失败的方法，或者是适当处理这些失败的方法？

![Shield target being hit by arrows](img/f40fd79833925586ce0cc4d05b4ee43a.png)

## 失败得又快又大声

简而言之，快速而响亮地失败意味着当错误发生时，它会尽可能早地这样做，并警告它应该警告的任何人，而不是静静地继续处于可能导致更多问题的错误状态。这里的是一篇关于快速风格开发的优秀文章。快速失效方法的前提是在获取可能危及程序安全的输入时失败(或者，更一般地说，在可能检测到任何问题时立即进入失败状态，而不是允许坏数据传播和程序未经检查就运行，或者更糟糕的是，坏数据存储在某个地方)。这在处理用户输入，或者处理来自脚本、模块之外，甚至通过 API 来自系统之外的输入时非常有用。可以使用这种方法的一个例子是检查传递给函数的无效值或数据类型。

```
function thisTestFunction($testInt)
{
	if(!is_int($testInt)){
		// Do something here
	}
} 
```

一些程序员在快速失败方法中犯的一个错误是简单地将异常和错误扔给用户，而没有为*处理*它们做好准备。您不希望普通用户对您的错误消息感到担心或困惑。更重要的是，你不希望怀有恶意的用户从显示给他们的信息中学到东西。向用户显示一条有用的消息，记录您的错误，并执行需要作为该异常结果的任何其他任务。您不希望仅仅失败*快速*，您还希望大声(*知道*有问题，马上)和安全(不要让您糟糕的异常处理或完全缺乏异常处理导致更多的安全问题)。

## 输入验证

有许多方法可以安全地验证用户输入。

类型转换是一种“验证”用户输入的有趣方式。有时候事情是这样的:

```
$member->property = (int) $_GET['property']; 
```

没有使用另一种方法来避免跨站点脚本攻击，而是简单地对值进行捕获、类型转换和赋值。这只在你有一个期望的类型，并且该类型的任何值都是安全的时候才起作用(否则，你还需要检查*合适的* int 值)。这种方法的问题是，我认为(在大多数情况下)你并不是真的*检查*输入，你只是*强迫*它成为它应该成为的样子。这可能会产生无法预料的后果。相反，更好的方法可能是使用`filter_input()`来检查合适的值。这里有一篇关于这个主题的很棒的文章。基本上，结果是这样的:

```
$member->property = filter_input(INPUT_GET, 'property', FILTER_VALIDATE_INT);

if (false === $member->property) {
  throw new Exception('Property was not an int');
} 
```

在现代 PHP 中使用原生`filter_input`函数有很多好处，你可以在前面提到的[文章](https://www.sitepoint.com/input-validation-using-filter-functions/)或[php.net](http://php.net/manual/en/function.filter-input.php)上读到更多关于它们的内容。

## 防止比较中的意外赋值

这是防御性编程的一个简单且经常被提及的原则。一个简单的改变你做比较的方式会有很大的影响。请考虑以下情况:

```
if($member->property == 12345){
	// Do crazy cool stuff
} else {
	// Don't do any fun stuff
} 
```

这是比较正常的比较吧？但是，如果你不小心用了“=”而不是“==”(或者，在大多数情况下，[更好的“= = =”](http://stackoverflow.com/questions/22140204/why-md5240610708-is-equal-to-md5qnkcdzo))会怎么样呢？手指在键盘上的简单滑动？可能是心不在焉？突然之间，你的比较在任何时候、任何情况下都是正确的。除非你有一个好的 IDE 警告你这一点，否则你要花多长时间才能发现？在某些情况下，这可能是一段时间内的无声错误。然而，有一个非常简单的方法可以防止这种情况:

```
if(12345 == $member->property){
	// Do crazy cool stuff
} else {
	// Don't do any fun stuff
} 
```

现在，如果你不小心用了一个等号，错误就不会无声无息了。显然，这种情况可能不会经常发生，可以通过测试来缓解，并且不是在所有情况下都有用，尤其是在进行变量间比较时。但是如果这种情况发生在你身上，这仍然不是一个坏主意。

## 处理尝试/捕捉和异常

语句是 PHP 开发人员的另一个热门话题。让我们先快速浏览一下我们正在谈论的内容。

```
try {
	if($member->property <= 0){
		throw new Exception("Value must be 1 or greater");
	}
	// If no exception was thrown, this will be output.
	echo "The value is acceptable";
} catch(Exception $e) {
	echo 'Message: '.$e->getMessage();
} 
```

一个众所周知的防御性编程工具是`try/catch`语句和`Exception`类。如果使用正确，这些工具对于捕捉和记录错误非常有用。一个好的程序员会使用`try/catch`语句来预测可能的错误或其他可能扰乱正常流程的情况。当这些异常发生时，必须以适当的方式处理它们。如果需要的话，应用程序的用户应该得到一个合理的错误消息，该消息在不泄露敏感信息的情况下尽可能有用。应用程序的管理员应该会收到详细的警报和/或日志。被错误处理或忽略的异常，忽略了“大声失败”的建议，并可能使程序在一段时间内继续处于一种无声的错误状态，这对任何人都没有好处。

关于 PHP 中异常的更多信息，请看[这里](https://www.sitepoint.com/exceptional-exceptions/)和[这里](https://www.sitepoint.com/error-handling-in-php/)。

## 处理

事务是数据库的一个特性，它允许将查询分组在一起，这样，如果一个查询失败，所有查询都会失败。这是 ACID 的一个实现，关于它你可以在这里阅读更多[。这个想法是，将多个查询组合成一个流程有时可能是一个更安全、更稳定的解决方案，尤其是当查询相互依赖时。PHP 开发人员经常完全忽略事务，或者假设它们是不必要的，但是当与数据库交互时，一点点防御性编程可以走很长的路。事务在](https://en.wikipedia.org/wiki/ACID)[这篇精彩的文章](https://www.sitepoint.com/mysql-transactions-php-emulation/)中有更深入的讨论，但简单来说，事务允许你运行 MySQL 更新，然后在*提交*结果之前检查结果。如果你正在使用 PDO [(你应该是)](https://www.sitepoint.com/re-introducing-pdo-the-right-way-to-access-databases-in-php/)，你可以使用 PDO 方法来开始一个事务，提交结果，以及回滚。除了前面提到的交易概述之外，还可以通过[这份深度指南](https://www.html5andbeyond.com/using-database-transactions/)进一步深入了解这些交易。

这里有一个在 PDO 使用交易的简单例子:

```
try{
	// Start the transaction
	$db->beginTransaction();

	// Perform one update
	$statement1 = $db->prepare("QUERY LANGAUGE HERE");
	$statement1->execute(array($a,$b));

	// Perform another update
	$statement2 = $db->prepare("MORE QUERY LANGUAGE HERE");
	$statement2->execute(array($a,$b,$c));

	// Commit the transaction
	$db->commit();
} catch(PDOException $ex) {
	// If an exception occurs, first, roll back updates
	$db->rollBack();
	// Then, in this block, further handle the exception
} 
```

## 结论

同样，这些只是一般的提示。显然，每一种都有它们的用途，而且每一种都有不使用*或*的显著情况。然而，如果你把这些概念运用到你的日常发展体系中，它会让你的工作更有效率。虽然这通常是一个更适用于目前正在学习 PHP 的开发人员的主题，但它对每个人来说都是一个很好的实践复习。

如果只有一件事是从这里学到的，特别是对于新开发人员，那就是你应该防御性地编程——为错误和失误的可能性做计划。适当地处理它们。不要让无声的错误发展。快速失败。测试您的代码。通过构建健壮的应用程序来测试和解决问题，并预测和处理未来的问题，您正在使您的应用程序更加可靠，并有望在幕后帮助创建更好的用户体验。

## 分享这篇文章