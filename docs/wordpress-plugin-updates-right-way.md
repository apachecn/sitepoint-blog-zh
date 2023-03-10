# WordPress 插件以正确的方式更新

> 原文：<https://www.sitepoint.com/wordpress-plugin-updates-right-way/>

几周前，我收到一封关于我开发的 WordPress 插件 [WP Photo Sphere](https://wordpress.org/plugins/wp-photo-sphere/) 的邮件。有一个大问题:更新插件会破坏某些安装。经过一些调查，我发现问题来自插件使用的选项:这些安装没有我添加的新选项的任何默认值。

![WordPress Plugin Updates](img/3f3f1fc7a495207b2c0f66e0192cb1b5.png)

这些值很重要，所以我需要一种方法来创建默认值。但是，与我想的相反，WordPress 没有提供任何本地方式来处理更新过程。

这也是我想到写这篇教程的原因。首先，我们会明白为什么我们需要一个更新过程，为什么 WordPress 没有提供这样的过程。然后，我将向您展示如何正确地创建您自己的进程来更新您的选项。

## 为什么插件的更新过程很重要

通常，更改文件不足以正确更新某些内容。例如，当你手动[更新 WordPress 文件](https://www.sitepoint.com/a-guide-to-updating-wordpress/)到一个新版本时，平台会要求你点击一个按钮来更新数据库。

假设您在插件中使用了选项。随着你的插件的发展，在新版本中你将需要更多的选项。当用户第一次激活你的插件时，创建新的选项是很容易的，你只需要使用激活钩子。

例如，让我们看看下面的代码。

```
function my_awesome_plugin_activation() {
    update_option('my_awesome_plugin_option', 'default value');
}
register_activation_hook(__FILE__, 'my_awesome_plugin_activation');
```

如果你不熟悉使用`update_option()`而不是`add_option()`，不要担心，我们稍后会解释，那时我们将看看如何处理我们的更新过程。

如果你想要一个新的选项，或者如果你在一个新的版本中更新一个现有选项的值，你需要更新已经使用你的插件的用户的数据库，所以我们需要一个在更新之后调用的函数。

激活挂钩看起来有点混乱。毕竟，当你自动更新一个插件时，它会被停用和重新激活，所以我们可以期待这个钩子被调用。但事实并非如此。

更准确地说，是这样，但是 WordPress 在 3.1 版本中停止了这种行为。开发团队解释了这个选择，你可以在 [Make WordPress Core](https://make.wordpress.org/core/2010/10/27/plugin-activation-hooks-no-longer-fire-for-updates/) 博客上阅读整个解释。主要原因是它没有每次都被调用是因为如果用户手动更新插件，激活钩子可以被跳过。

所以 WordPress 并没有提供一个默认的方法来在插件更新后自动调用一个函数。这就是为什么您需要构建自己的流程。

## 如何处理更新过程

在这部分教程中，我将向你展示如何在插件更新后自动调用一个给定的函数。我们将在下一部分看到如何正确处理现有选项的更新，以及新选项的创建(在同一个函数中)。

### 这种方法的原理

我们方法的总体原则是，我们将把插件的版本号存储在两个地方:插件主文件中的一个常量，以及数据库中的一个选项。

数据库中的数字将存储用户当前安装的版本，而常量中的数字是当前版本。如果这两个数字不同，那么数据库选项自上次插件更新后就没有更新过，所以我们需要这样做。

在这种情况下，我们调用一个更新所有必要选项的函数。这个函数还更新存储在数据库中的版本号:这样，我们就不会不必要地调用这个函数。

### 常数

既然我们已经介绍了我们要做的事情，那么是时候编码了！首先，在插件的主文件中添加一个常量定义，用当前的版本号作为值。为了防止任何问题，我们测试它是否还不存在。

```
if (!defined('MY_AWESOME_PLUGIN_VERSION'))
    define('MY_AWESOME_PLUGIN_VERSION', '3.4.1');
```

通常，插件版本用数字标识，但是，如果你使用另一个系统，请随意使用。这里唯一的约束是每个版本都有一个唯一的标识符，或者至少是每个需要在数据库中进行更改(新选项、新默认值等)的版本。).

### 检查功能

我们现在需要编写一个函数来检查数据库是否需要更新。该函数会将先前定义的常数与当前存储在数据库中的值进行比较。为了做到这一点，我们将确保我们的函数在任何地方都被调用，动作`plugins_loaded`，一旦所有的插件都被加载就被触发。

```
function my_awesome_plugin_check_version() {
}

add_action('plugins_loaded', 'my_awesome_plugin_check_version');
```

这个函数很简单。我们检索存储在数据库中的版本号，就像任何其他选项一样，并将它与常数进行比较。如果这些值不同，我们调用`my_awesome_plugin_activation()`函数。

```
if (MY_AWESOME_PLUGIN_VERSION !== get_option('my_awesome_plugin_version'))
    my_awesome_plugin_activation();
```

现在，我们需要澄清一些问题。首先，如果选项在数据库中还不存在怎么办？如果选项不存在，`get_option()`返回`false`，与你的版本号不同，所以会调用函数。

那么我们为什么要调用激活函数呢？为了清楚起见，我们可以创建一个新的函数，专门用于更新过程。但是，如果你这样做，你会看到这个新功能将非常类似于激活，因为更新一个选项可以实现我们创建一个相同的方式。

### 更新数据库中的版本号

你可以在上面调用的激活函数里做任何你想做的事情。但是，需要做一件事，更新存储在数据库中的版本号。这样，我们就不会在每次加载页面时都调用我们的函数。

```
update_option('my_awesome_plugin_version', MY_AWESOME_PLUGIN_VERSION);
```

注意诀窍:我们不用`add_option()`，只用`update_option()`。事实上，如果选项还不存在，`update_option()`会创建它。如果存在，它会将其值更新为指定的值。这就是为什么我们可以毫无问题地将我们的激活函数用作更新函数。

## 更新选项

### 不要凌驾于用户的选择之上！

更新任何选项都可以像我们更新版本号一样:你调用`update_option()`，你就完成了，即使这是 WordPress 第一次看到这个选项。

然而，我们并不总是想要更新选项值。事实上，如果你使用选项，通常是为了让你的用户个性化设置。通过使用`update_option()`,你将在每次更新插件时覆盖用户的选择，这不是我们想要做的。

上面，我们看到如果选项不存在，`get_option()`返回`false`。我们将使用这个行为来测试我们想要更新的选项是否存在于数据库中。如果是这样，我们什么都不做。否则，我们创建选项。

```
if (get_option('my_awesome_option') === false)
    update_option('my_awesome_option', 'my_value');
```

注意，对于您不想覆盖的选项，这个测试是必要的。在某些情况下，我们可能想这样做，想想版本号，我们肯定不想保留旧的值！

### 一个特例——数组

你应该知道 WordPress 允许数组为我们的选项存储值，创建它们并不比创建其他选项更困难。例如:

```
update_option('my_awesome_plugin_settings', array(
    'awesome_titles' => true,
    'any_number' => 7
));
```

如果需要几个设置，使用数组是个好主意。这样，你就不会在数据库中使用很多条目，而且你也限制了另一个插件使用相同名称选项的机会。然而，当我们考虑更新过程时，这会引起问题。

为了理解为什么，假设您有一个数组作为选项，带有一些键。你的用户肯定会个性化这些价值观。通过上面的测试，我们可以只在选项不存在时创建它，并且这些选择不会被覆盖。这看起来很简单，但是如果你想在数组中创建一个新的键呢？

如果数据库中存在该选项，前面的代码不会创建它，所以您的新密钥不会存在。但是，如果我们删除条件，数组将在每次更新时检索其默认值。不理想。还好有解决的办法！

首先，我们定义一个数组，其中包含我们的选项的默认值(如果存在的话，使用新的键)。

```
$default = array(
    'awesome_title' => true,
    'any_number' => 7,
    'new_option' => 'default'
)
```

然后，我们检索当前存储在数据库中的数组。

```
$option = get_option('my_awesome_plugin_settings');
```

现在我们可以使用 PHP 函数`array_merge()`，将默认值数组作为第一个参数，将用户的值数组作为第二个参数。这样，我们将得到一个包含所有在`$default`数组中定义的键的数组，并且我们不会有任何不存在的选项。如果用户更改了旧选项之一，它们的值将被保留。与`array_merge()`一起，我们总是保持最新的定义。

```
$option_to_store = array_merge($default, $option);
```

最后，我们用`update_option()`将结果存储在数据库中。

```
update_option('my_awesome_plugin_settings', $option_to_store);
```

我们已经接近尾声了，但是我们现在需要修复一个 bug，如果这个函数是第一次执行的话，你可能会遇到这个 bug。

这个函数在插件被激活时被调用，这就是我们想要的。但是，在这种情况下，选项还不存在，所以`get_option()`返回`false`。问题是使用`false`作为`array_merge()`的参数会导致错误。

我们想要的很简单，如果选项不存在，我们希望`$option`是一个空数组。为此，我们可以使用第二个参数`get_option()`，它代表 get 的默认值(为了不返回`false`)。

```
$option = get_option('my_awesome_plugin_settings', array());
```

## 结论

一旦你完成了，处理 WordPress 插件的更新过程就不会太复杂了。但是，如果使用选项，这很重要，因为不初始化选项会导致一些问题。

目前，WordPress 没有提供处理插件更新的本地方式。事实是，考虑到我们上面列出的问题，如果我们有一天看到这种类型的功能被引入，它应该以类似于本教程的方式实现。

你可以在这里获得我的示例插件[的代码。把这段代码作为你实现自己的 WordPress 插件更新过程的框架。如果你有任何反馈，请在下面的评论中告诉我。](http://jeremyheleine.me/downloads/sitepoint/upgrade-process.zip)

## 分享这篇文章