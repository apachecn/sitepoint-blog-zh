# Drupal:如何创建自己的 Drush 命令

> 原文：<https://www.sitepoint.com/drupal-create-drush-command/>

Drush 是一个命令行工具，用来帮助你从终端使用 Drupal。它默认带有一堆有用的命令，比如下载、启用甚至更新模块。但是模块可以定义自己的命令，让 Drush 使用它们的代码执行操作。

在这篇文章中，我们将为一个虚构的模块创建一个 Drush 命令，这个模块实际上什么也不做。目的是说明您需要在 Drush 方面做什么，而不必担心定义命令的模块的实际功能。如果您想继续学习，我假设您已经建立了自己的定制模块。不管它有什么作用。

我们写的所有代码都可以在[这个库](https://bitbucket.org/Swader/drush-demo)中找到，所以如果你想继续或者甚至跳过，你可以去看看。每次提交代表我们在教程中采取的不同步骤。

## 我们的模块

我们希望向 Drush 公开的模块功能将会非常简单。这是一个设置`Hello world!`消息的函数:

```
function demo_drush_print_statement() {
  drupal_set_message(t('Hello world!'));
} 
```

虽然不多，但它将帮助我们理解如何使用 Drush 将该语句打印到终端屏幕上，以及在这样做的过程中，如何配置该命令来执行各种强大的操作。我们将在本教程中调整它，以展示各种可能性。

## Drush 命令文件

我们需要做的第一件事是向模块的文件夹中添加一个新文件，文件名以`.drush.inc`结尾。Drush 会扫描代码库，根据这个名字捡起来，加载我们在里面声明的函数。

最佳实践也是以您放置该文件的模块命名该文件。对于我们的例子，这将是`demo_drush.drush.inc`(我的模块的名称是`demo_drush`)。现在，只需打开顶部的 php 标签(`<?php`)并保存文件。

## 命令挂钩和回调

Drush 命令架构中有两个主要组件:钩子实现，我们在其中定义命令及其配置，以及由命令触发的回调函数。当然，在这个过程中还会调用其他函数(比如验证或前/后回调)，但我们不会在本教程中讨论它们。

首先，让我们实现 hook_drush_command()并定义一个名为`drush-demo-command`的简单命令，别名为`ddc`:

```
/**
 * Implements hook_drush_command().
 */
function drush_demo_drush_command() {

  $items['drush-demo-command'] = array(
    'description' => 'Demonstrate how Drush commands work.',
    'aliases' => array('ddc'),
  );

  return $items;
} 
```

通过这个钩子实现(以下称为`the hook`)，我们在数组中返回一个新的键，它将是完整的命令名。在这个数组中，我们配置它。现在，我们为该命令指定了一个简单的描述和一个别名。因此，如果我们运行`drush help ddc`，我们的终端会打印出命令的描述和别名。

Drush 命令架构的第二部分是回调函数。就像现在这样，Drush 希望有一个函数被声明为`drush_drush_demo_command()`。这个默认的命名结构以`drush`开头，后跟命令名，所有命令名都用下划线连接。所以让我们快速声明它，并用它来调用我们之前写的`demo_drush_print_statement()`函数:

```
/**
 * Callback for the drush-demo-command command
 */
function drush_drush_demo_command() {
  demo_drush_print_statement();
} 
```

现在，如果你清除 drush 缓存(`drush cc drush`)，你可以运行`drush ddc`命令，你应该看到`Hello world!`打印到屏幕上。恭喜你，你最基本的德鲁士命令起作用了。

尽管在本教程的剩余部分中，我们将继续使用这个默认的命名约定，但您不必受限于此。如果你想给回调函数另一个名字，你可以给`hook`添加一个新的键来指定名字:

```
...

'callback' => 'drush_demo_test'

... 
```

现在你可以声明一个名为`drush_demo_test()`的函数，这个函数将被这个命令自动调用。不过现在，我们将继续使用我们的`drush_drush_demo_command()`名称。

## 参数和选项

使用 Drush 命令可以做的一件基本事情是向它们传递参数和选项。两者的关键区别在于前者是强制性的，而后者不是。另一个区别是值传递给回调函数的方式。实参作为函数参数(按顺序)传递，而选项在回调中使用特殊的辅助函数(drush_get_option)检索。

因此，在我们命令的钩子内，让我们添加参数和选项信息:

```
...

'arguments' => array(
  'type' => 'The type of statement (error or success).',
),
'options' => array(
  'repeat' => 'The number of statement repeats.',
),

... 
```

我们声明了一个参数(称为`type`)和一个选项(称为`repeat`)。这些值应该解释它们的目的，但是如何使用它们可能不是那么明显。参数`type`将是在终端(`drush drush-demo-command`或`drush ddc`)中写在命令名之后的第一个字符串。该选项将是一个整数值，分配给命令中的`--repeat`标志。

示例:

```
drush ddc error --repeat=10 
```

这个命令应该使打印的语句出错，并在终端屏幕上打印 10 次。现在让我们调整代码来实现这一点。

首先，让我们修改一下`demo_drush_print_statement()`函数:

```
function demo_drush_print_statement($type = NULL) {
  drupal_set_message(t('Hello world!'), $type);
} 
```

现在打印的语句类型是动态的。其次，让我们修改命令回调函数，将其改为:

```
function drush_drush_demo_command($type = FALSE) {

  // Check for correct argument
  $correct_args = array('error', 'success');
  if (!in_array($type, $correct_args)) {
    return drush_set_error(dt('"@type" is not a valid statement type. Please choose between "success" and "error".', array('@type' => $type)));
  }

  // Option
  $repeat = drush_get_option('repeat', 1);
  if ($repeat > 1 && is_numeric($repeat)) {
    for ($i=0; $i < $repeat; $i++) { 
      demo_drush_print_statement($type);
    }
  }
  else {
    demo_drush_print_statement($type);
  }

} 
```

首先，我们检查传递的参数(`$type`)是否是两种可接受的类型之一。如果没有，我们返回一个 Drush 错误。如果它被接受，我们继续检查`repeat`选项是否已经被传递，以及它是否包含一个大于 1 的数值。如果没有，我们调用我们的`demo_drush_print_statement()`一次，但是如果有，我们调用`repeat`值的次数。无论如何，我们也将`$type`参数作为参数传递给函数。

现在，您可以继续从终端运行该命令，并测试参数和选项是如何工作的。

## 用户输入

运行这个命令时，您会注意到，如果我们传递的不是两个可接受的参数值，它会返回好的错误。但是如果我们什么都不通过呢？它返回一个一般性的 Drush 错误，抱怨缺少参数。

让我们这样做，如果用户没有传递参数，我们会问他们想要传递什么参数，并交互地使用他们提供的值。对此有一个方便的 Drush 函数，但整个实现可能如下所示:

```
// Check for existence of argument
if (!$type) {
  $options = array(
    'success' => dt('Success'),
    'error' => dt('Error'),
  );
  $type = drush_choice($options, dt('What kind of message you\'d like to print?'));
}

... 
```

在检查是否传递了正确的参数之前，这将转到命令回调函数的顶部。但是我们在这里做什么？

首先，只有当用户没有传递参数时，一切才会发生。第二，我们创建一个键-值对数组，表示我们给用户的选择。数组键代表选择的机器名，而值则是人类可读的名称。第三，我们将这个数组和一个问题字符串一起传递给`drush_choice()`函数，该函数将返回用户选择的机器名称。这就变成了我们新的`$type`变量(自变量)。

所以现在我们可以试试了。运行不带参数的命令，系统会要求您键入一个与两个可用选项之一相对应的数字。完成后，您应该会看到相应打印的语句。

## 例子

正如我之前提到的，为我们自己的命令(`drush help ddc`)运行 Drush help 命令会列出一些关于这个命令的有用信息(参数、选项、描述、别名等)。让我们回到我们的钩子，向我们的命令添加更多的信息:

```
...

'examples' => array(
  'drush ddc error' => 'Prints the statement once with the error flag.',
  'drush ddc success --repeat=10' => 'Prints the statement 10 times with the success flag.',
 ),

 ... 
```

我们有代表示例命令用法的键，以及描述命令运行时会发生什么的值。现在，如果我们运行`drush help ddc`，我们可以看到如何使用这个命令的这些有用的例子。

## 结论

这里是结束本教程的好地方，因为我们已经讨论了定义我们自己的 Drush 命令的许多基本但重要的方面。我们使用了一个简单的例子，但是这个例子让我们能够说明这个过程的许多特性:如何定义命令信息，以及如何在回调中使用这些信息。我们查看了参数、选项，甚至查看了如何从终端获取用户输入。

当然，还有很多方面我们无法在这里讨论。它们用于更高级的用途，但非常重要，必须记住它们的存在。例如，您可以指定 Drush 运行所需的 Drupal 引导级别。您还可以为命令指定一个模块依赖项，甚至可以指定它需要运行的核心版本。因此，请务必在 Drush API 站点和文档中查看这些内容，以获取更多信息。

## 分享这篇文章