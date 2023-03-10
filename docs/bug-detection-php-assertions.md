# 毫不费力(或者更好！)用 PHP 断言检测 Bug

> 原文：<https://www.sitepoint.com/bug-detection-php-assertions/>

如果你和我一样，比起测试和调试代码，你更喜欢写代码。如果我们可以用更少的测试和调试工作产生更高质量的代码，我们会欣然接受这个机会，对吗？

断言可能会帮我们做到这一点。

不要误解我。测试和调试是开发的重要部分。忽视他们，后果自负。但是正确使用断言可以更早、更彻底地检测和诊断错误。这会导致更容易的修复，让你有时间去做更有趣的事情。怎么会？因为:

1.  **Bugs are harder to fix the later they are detected.**
    It’s much harder and trickier to find and fix a bug in a live Website than the first time you try out the code you’ve just written. Assertions find more bugs, sooner. This saves testing and debugging time.
2.  **Bugs become harder to diagnose the further the symptom is removed from the cause.**
    It is often laborious to work backwards from the symptom to find the cause of a bug. In the case of intermittent bugs, it is sometimes impossible. Assertions can spot the bugs early, before any symptoms show up. In a sense, they create symptoms right where things first went wrong. This saves testing and debugging time.
3.  断言很容易使用。
    很多时候这并不比写评论难多少。这只会增加很少的测试和调试时间。

您在代码中投入的少量时间将很快得到回报，在易于诊断和修复的时候发现错误。如果其他人会使用和修改您的代码(或者您自己在离开几个月后会回来)，这些好处对您和其他人都有好处:对代码的理解提高了，支持请求减少了。

##### 什么是断言？

激战中的开发人员知道——或者假设——某些事情是真的。例如，他们可能知道某个缓存中的时间戳不会超过一天，购买者在购买均码袜子时不会选择特大号，或者客户表中有一个条目与每个购物车中的客户 id 相匹配。

但是假设并不总是正确的。有时候，就像没有考虑到一个不寻常的情况一样简单。也许某个第三方模块确实让文件滞留了一天以上，或者当没有指定大小时，Extra Large 被作为默认大小传递。

更常见的情况是，随着新功能的增加，最初编写程序时为真的假设不再为真。在加强网上商店，让人们在注册前购物，也许不再保证在顾客把东西放入购物车的那一刻，顾客的表中就有条目。

如果存在假设不成立的情况，开发人员需要重新审视它们(并且可能修复一个 bug)。)在缺乏数学证明的情况下，找出答案的最佳方法是使用断言。断言是运行时检查 PHP 表达式是否为真。如果是，执行继续不变；如果不是，则采取一些其他动作。默认情况下会发出警告。

它们通常只在开发期间进行检查，而不是在站点部署之后。这让开发人员不必担心生产代码中的断言性能问题。

但是不需要从代码中删除断言来实现这一点。正如我们将在下面看到的，有一种简单的方法来控制断言是否执行。它们可以留在代码的其余部分，并根据需要启用或禁用，在需要做进一步工作时提供内置的健全性检查。

##### 使用断言

从表面上看，PHP 中的断言看起来就像一个函数调用:

```
 assert(false);
```

如果启用了断言(PHP 默认)，该语句将导致生成一个警告:

```
Warning: Assertion failed in test.php on line 43
```

鉴于:

```
assert(true);
```

将不会导致任何警告。

在 PHP 中，要断言的参数要么是一个布尔值(就像前面的例子一样)，要么是一个要作为 PHP 代码计算的字符串。使用字符串参数通常会提供更多信息:

```
assert('false');
```

结果是:

```
Warning: Assertion "false" failed in test.php on line 43
```

这里有两个更简单的例子:

```
$one = 1;  

$three = 3;  

assert("$one + $one == $three");  

assert('$one + $one == $three');
```

导致

```
Warning: Assertion "1 + 1 == 3" failed in test.php on line 45  

Warning: Assertion "$one + $one == $three"  

failed in test.php on line 46
```

断言字符串使用单引号或双引号，尽管警告消息不同。如果您对错误消息中显示的变量名感兴趣，请使用单引号；如果您希望看到值本身，请使用双引号(确保遵循 PHP 关于引用双引号内变量的规则)。

到目前为止，示例断言不是很有趣，因为它们重复了显而易见的内容。当他们检查程序的实际状态时，他们有更多的意义。

检查时间戳最长期限的断言可能是这样的:

```
define('MAX_CACHE_AGE', 60 * 60 * 24);  

//...  

assert('time() - $timestamp <= MAX_CACHE_AGE');
```

并且检查袜子的尺寸不是 XL 的断言可能是:
`define('XL', 'Extra Large');  
//...  
assert('$socks->size() != XL');`

您可以通过编写一个函数，比如说`customer_exists`，并在断言中调用它，来检查 customer 表中是否有购物车条目的行，如下所示:

```
assert('customer_exists($cart->get_customer_id())');
```

##### 在哪里使用断言

使用断言的方法和场合有很多，只受你的想象力的限制。一般来说，如果你想写一个关于程序状态的注释，你可以把它变成一个断言。
当然，生产代码不能依赖断言。即使删除了所有断言，它也必须正确执行。这意味着断言不应该进行需要清理或其他操作的生产级错误检查，比如在数据库调用后检查错误或验证用户输入。

为了说明一些使用断言的好地方，这里有一些具体的想法。但是，请不要认为这些是唯一的可能性！

1.  **Switch with no default.**
    When a switch statement has no default case, often there is an implicit assumption that all the possibilities have been covered. This can be made explicit with an assertion:

    ```
    switch ($some_variable) {  

      case 'a':  

        //...  

        break;  

      case 'b':  

        //...  

        break;  

      //...  

      default:  

        assert('$some_variable != ' . "$some_variable");  

        break;  

    }
    ```

    如果达到默认情况，断言将失败，错误消息将描述“不可能”的值。

    当一系列 if-else 语句旨在涵盖所有情况时，也有类似的机会。

2.  **Type checking.**
    PHP is a loosely and flexibly typed language, which serves it well most of the time. But sometimes it is important that a variable be a string, a number, an object, or of some other type.

    ```
    assert('is_resource($fp)');
    ```

3.  **Parameter checking.**
    Assertions can be used to check the validity of parameters passed to a routine.
    `function some_function($positive_int_parameter) {  
     assert('isset($positive_int_parameter)');  
     assert('is_int($positive_int_parameter)');  
     assert('$positive_int_parameter > 0');  
     //...  
    }`

    重要的是，如果您需要在生产系统中使用断言来检查参数，就不要使用断言。例如，如果`$positive_int_parameter`的值可以是用户输入的任何值，那么使用常规方法检查它是否在范围内，如果检查失败，就做一些类似抱怨或纠正值的事情。如果我们用断言来做，那么当断言检查消失时，抱怨/纠正就会消失，这将从生产系统中带走功能。

    检查函数结果。您可以进行健全性检查
    `$result = process_string($input_string);  
    assert('strlen($result) <= MAX_RESULT_LEN');`

    或者你可以使用另一种方法来计算函数结果

    ```
    $output = expensive_crunching_routine($input);  

    assert('$output == alt_expensive_crunching_routine($input)');
    ```

    即使替代例程在计算上有点昂贵，您也可能想要考虑这种断言，因为它不会成为生产代码中的性能问题。

4.  **Multistatement assertions.**
    Generally speaking, the expressions in assertions should not have side effects. One important exception is when multiple statements are required to make an assertion. For example:
    `assert(list($january, $ignore) = explode(',', $year_data));  
    assert('$january > MIN_FIRST_MONTH');`

    对 explode 的调用被包装在一个断言中，这样它就不会在生产系统中执行。它肯定会成功，因为只有当分隔符为空字符串时，explode 才会返回 false。因为它总是会成功，所以不需要将参数作为字符串传递(不会有错误消息)。重要的论断当然是第二个。

    PHP 确实对这种技术施加了限制。例如，不可能在断言中定义函数或类，以避免在生产代码中定义它们。尽管如此，在产品代码中保留“助手”函数和类所带来的性能和便利性损失通常很小，所以这不是一个大问题。

通过练习，你会发现很多断言的机会。

##### 控制断言

PHP 中用来控制断言的例程是`assert_options`。它有两个参数，一个指定要设置哪个选项，另一个指定要设置的选项的值。启用断言的常量是`ASSERT_ACTIVE`，可能值为 0 或 1。所以在开发过程中，你会希望在
`assert_options(ASSERT_ACTIVE, 1);`
的早期某个地方这样做，但在生产中它应该读作
`assert_options(ASSERT_ACTIVE, 0);`
你还可以控制当断言失败`(ASSERT_BAIL)`时是否终止执行，以及在断言评估期间是否发出警告`(ASSERT_QUIET_EVAL)`。小心`ASSERT_QUIET_EVAL`！如果它打开了，并且在执行您编写的断言时出现了问题，可能是因为语法错误或者因为您调用了一个不存在的函数，PHP 不会告诉您，并且您的应用程序可能会在到达该断言时表现异常或停止。最好将它设置为 0(默认值)，让评估产生一些“噪音”，除非您有特定的原因要打开它。

另外两个有趣的选项是`ASSERT_WARNING`和`ASSERT_CALLBACK`。这些允许您定制您的应用程序如何对断言做出反应。默认情况下，断言失败会生成一个警告，就像我们前面看到的那样。但是你可能想通过编写自己的回调例程并用`ASSERT_CALLBACK`安装它来对断言处理进行更多的控制。如果这样做，您可以选择用`ASSERT_WARNING`来抑制默认警告。让我们看一个例子。

##### 越来越花哨

断言的一个潜在问题是，它们直接向正在处理的页面输出警告。这意味着如果你在生成头的代码段中会有麻烦——你会得到可怕的“不能添加头信息——头已经发送”的警告。如果您正在呈现页面布局、计算要在表单中显示的值或执行类似的 UI 密集型任务，这也是一个问题。

有一个开源包形式的解决方案，叫做 [phpAssertUnit](http://sourceforge.com/projects/jsassertunit/) 。它通过提供一个单独的断言报告窗口来报告失败的断言，从而“置身事外”。它还为 JavaScript 和 PHP 提供了断言功能。

我鼓励您探索这个包，但是在本文中，我将只向您展示如何使用断言报告器来查看失败的 PHP 断言。基本思想是编写一个 PHP 断言处理程序，将结果发送给断言报告器，并将该处理程序注册为 PHP 断言回调。这里的评论解释了细节。

```
<?php   

  include_once('assert.mod');    // phpAssertUnit PHP code   

  define('FAILED_PHP_ASSERTION',0);  // used to create a    

                                        // failed phpAssertUnit   

                                        // assertion   

  assert_options(ASSERT_CALLBACK,   

    phpAssertUnit_callback);          // register the handler   

  assert_options(ASSERT_WARNING, 1);  // set to 0 for no    

                                        // on-page warning   

  assert_options(ASSERT_QUIET_EVAL, 0); // leave off if you can       

                //   

  function phpAssertUnit_callback($file, $line, $code) {     

                                        //    

    global $Assert;                  // from phpAssertUnit   

//   

// Windows: take care of ':' because it's a trigger    

// for assertUnit, and prepare '' for HTML display   

// this should have no effect on filepaths from    

// Unix/Linux unless they contain these characters).   

//   

    $f = str_replace(':', '&#'.ord(':').';',  $file);   

    $f = str_replace('\', '\\', $f);   

//   

// If PHP assertion was called with a Boolean expression,    

// $code will be unset or empty   

// so just label it 'Boolean expression'.   

//   

    if (!isset($code) || $code == '') {   

      $code = 'Boolean expression';   

    }     

//   

// Prepare the code for HTML display...   

//    

    $c = htmlentities($code);   

//   

// ...and invoke the phpAssertUnit assertion   

//   

    $Assert->isTrue(FAILED_PHP_ASSERTION,   

true,           

//always report PHP assertions   

"PHP Assertion failed on Line $line, File $f: $c");   

  }   

?>
```

将它包含在您的 PHP 文件中(或者更好的是，让它成为包含在每个页面上的 config.php 文件的一部分)，确保 phpAssertUnit 文件(assert.modreporter.html；reporter.jsreporter.css)都在正确的位置(根据 phpAssertUnit 文档和您所做的任何配置)，您将在一个单独的窗口中获得信息丰富、不引人注目的断言报告——如果您愿意，可以与 JavaScript 断言集成。

您可以在这里看到关于本文[中讨论的断言的断言浏览器报告。](http://webmasterbase.com/examples/php-assert/test.php)

##### 其他简单的技术

断言不是唯一有助于早期错误警告的工具。这里还有一些其他的。

1.  **Error Levels**
    Consider setting error levels to `E_ALL` during development. Sure, you’ll receive some notices that may not matter, say about a variable being referenced before set when all you want to know is whether it’s `NULL`. But when you reference an unset variable that should have a value, you’ll be happy PHP told you about it so quickly. It’s worth developing the habit of keeping your code in a state that doesn’t trigger any warnings or notices, so when they do occur you’ll know you’ve got something to look into.

    如果您愿意，可以通过修改上面显示的代码并为`set_error_handler`提供合适的错误处理程序，将通知、警告和错误与 phpAssertUnit 集成在一起。

2.  **HTML Validation**
    Most PHP code generates HTML for Websites. Another easy way to find issues early is to use an HTML validator to check what your code is generating. There are many validation services available, including a good free one at [http://validator.w3.org/](http://validator.w3.org/).
3.  **Unit Testing**
    Modern development methodologies such as Extreme Programming advocate that you write unit tests before you write code. Whether or not you want to go this far, you’ll find you spend less time writing unit tests if you have help automating their execution. Consider using [PhpUnit](http://phpunit.sourceforge.net/), a terrific open source unit test harness based on JUnit for Java.

    顺便说一下，断言和单元测试的效果是互补的。让单元测试启用断言，您将自动增加代码测试的数量和覆盖范围。

无论您的项目涉及一个人、一个在同一办公室工作的小团队、一个分布在世界各地的大团队，还是介于两者之间，断言都提供了重要的好处。它们不仅能帮助你传达你的假设，还能告诉你什么时候你的假设是错的。他们及时地回报小的投资，用更早和更容易地诊断和修复代码中的问题的大回报来创建它们。使用它们会节省你的时间。

## 分享这篇文章