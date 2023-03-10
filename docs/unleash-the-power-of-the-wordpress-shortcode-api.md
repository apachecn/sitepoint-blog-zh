# 释放 WordPress Shortcode API 的力量

> 原文：<https://www.sitepoint.com/unleash-the-power-of-the-wordpress-shortcode-api/>

WordPress 在其 2.5 版本中引入了 Shortcode API。这个 API 允许开发者在他们的插件或主题中添加一些或多或少复杂的特性，而用户不必插入任何 HTML 代码。

Shortcode API 的优点是开发者不需要使用正则表达式来检测用户是否将他们的 Shortcode 包含在他们的帖子中:WordPress 会自动检测注册的 short code。

在本教程中，我们将学习如何使用短代码 API。我们将创建自己的短代码，看看如何使用经典和未命名的属性，以及如何处理内容。

## 短代码 API 是如何工作的？

在使用 Shortcode API 之前，了解它如何为开发人员和最终用户工作是很重要的。

首先，开发者用 WordPress 函数`add_shortcode()`注册他们的短代码。然后，WordPress 会自动检测这个短代码何时被使用，并调用相应的函数，用特定的文本或 HTML 代码替换这个短代码。

对于用户来说，这真的很简单:他们将短代码插入到他们的帖子中，这个短代码将在每次显示帖子时被替换。

短码可以更复杂或更简单。例如，我们可以找到简单的短代码，如`[contact]`插入一个联系表单来代替标签。其他的短代码可以填充一些内容，比如`[hello]Me[/hello]`，其他的也可以使用属性，比如`[sayhello to="You"]`。这些属性甚至可以是未命名的:`[sphere 42]`是一个使用未命名属性的短代码，由 [WP Photo Sphere](http://wordpress.org/plugins/wp-photo-sphere/) 插件添加。最后，一些短代码可以结合所有这些，所以这个 API 真的可以做出一些强大的东西。

## 没有属性的短代码

在创建更复杂的短码之前，我们将从一些没有属性的例子开始。通常，这里给出的代码应该放在项目的一个文件中(例如，`functions.php`代表一个主题)。

### 创建短代码

WordPress 为我们提供了一个特定的功能来注册一个短码:`add_shortcode()`。这个函数需要两个参数，一个是短代码的名称，另一个是每次找到这个短代码时要调用的函数。

```
add_shortcode('myshortcode', 'replace_my_shortcode');
```

基本上回调函数只要求一件事:替换 shortcode 的内容必须在`return`指令中指明。

```
function replace_my_shortcode() {
    return 'Hello World!';
}
```

这个例子有效。如果你写一篇文章，并在其中插入简短代码`[myshortcode]`，它将被文本 *Hello World 取代！*。所以它是有效的。但它是有限的，不是很有用。

这就是为什么使用短代码的内容是一个好主意。问题是:例如，如果用户写了`[myshortcode]You[/myshortcode]`，如何检索这些内容？

为了获得这些内容，我们不需要在`add_shortcode()`调用中做任何改变，但是我们将为我们的`replace_my_shortcode()`函数添加两个参数。

```
function replace_my_shortcode($atts, $content = '') {
    return 'Hello ' . $content . '!';
}
```

在第一个参数中，我们可以检索文章作者使用的属性，但是我们将在下一部分中看到。这里，我们更感兴趣的是第二个参数，它包含我们的短代码的两个标签之间的文本。

如果用户在短代码中包含了一些内容，那么它将在`$content`变量中。否则，这个变量将是一个空字符串，因此我们可以使用一个条件来检测这种情况，例如使用`empty()`函数。

```
function replace_my_shortcode($atts, $content = '') {
    if (empty($content))
        return 'Hello World!';
    else
        return 'Hello ' . $content . '!';
}
```

### 其他短代码中的短代码

我们知道如何创建短代码以及如何管理其内容。但是如果这个内容包含其他短码呢？例如，假设我们有两个短代码:`[name]`显示作者的名字，和`[myshortcode]`上面的回调函数。然后，如果用户写`[myshortcode][name][/myshortcode]`，结果会是*“你好[姓名]！”*，这肯定不是我们想要的。

让 WordPress 解析出现在另一个短代码内容中的短代码是可能的，可以用`do_shortcode()`函数来完成，正如你在下面的代码中看到的。

```
function replace_my_shortcode($atts, $content = '') {
    return 'Hello ' . do_shortcode($content) . '!';
}
```

有了这个新的回调函数，我们之前的例子给出的结果是*“Hello Jeremy”*(是的，我们都有相同的名字！).

但是，这不是默认行为，这是有原因的。事实上，如果用户插入一个短代码，比如在我们的`myshortcode`短代码中显示一个图像，结果不会很好。但是还有另一个问题，描述如下。

用`do_shortcode()`函数，事情是这样的:

```
[myshortcode]
    [name]
[/myshortcode]
```

或者:

```
[myshortcode]
    [name][/name]
[/shortcode]
```

将被解析，没有任何问题。但是像这样的其他事情:

```
[myshortcode]
    [myshortcode]You[/myshortcode]
[/myshortcode]
```

是有问题的。使用上面显示的最后一个`replace_my_shortcode()`函数，结果将是*“Hello Hello！你！[/myshortcode]"* 。换句话说，找到的第一个`[/myshortcode]`标签关闭了第一个`[myshortcode]`标签。另一个`[myshortcode]`标签变成空短码，最后一个`[/myshortcode]`标签被忽略。

目前，使用 WordPress 函数没有办法阻止这种行为:`do_shortcode()`不要让我们过滤短码来搜索，所以如果你打算把短码解析成你的，要小心。

## 带属性的短码

属性对于向短码添加一些功能非常有用。使用属性，您将能够获得图像 ID 和其他附加数据，比如名称或完成短代码内容所需的任何内容(如果您处理内容的话)。

### 句柄属性

正如我们之前提到的，如果 WordPress 在用户插入的 shortcode 中找到属性，它会将它存储在回调函数的第一个参数中。这个参数是一个关联数组，包含所有使用的属性。

这个数组的结构和我们预期的完全一样:它的键是属性的名称，它的值是属性的名称。例如，如果用户插入了短代码`[myshortcode name="Here is my name" n="5"]`，那么我们将找到这个数组:

```
Array
(
    [name] => Here is my name
    [n] => 5
)
```

在函数的第一个参数中。

短代码中使用的所有属性都存储在这个数组中。但是，如果您使用属性，您可能希望将其中一些属性设置为可选的，并使用默认值。这就是 WordPress 功能`shortcode_atts()`存在的原因。

```
function replace_my_shortcode($atts, $content = '') {
    $atts = shortcode_atts(array(
            'name' => 'World',
            'n' => 1
        ), $atts);

	return 'Hello ' . $atts['name'] . '!';
}
```

该函数将合并用户提供的`$atts`数组和第一个参数中给定的数组，该数组包含您的默认值。在上面的例子中，如果用户没有填写`name`属性，我们显示*“Hello World！”*。否则，将使用指定的名称。

### 未命名的属性

有时，未命名的属性可能是比传统属性更好的解决方案。比如提供一张图片的 ID:`[myimage 7]`而不是`[myimage id="7"]`。当我们试图检索这些特殊属性时，问题就来了。没有名字可以搜索，我们应该选择哪个键？

至于其他的，未命名的属性可以在回调函数的第一个参数中找到，带有编号的键。一个例子会比一个长的解释更清楚，所以假设用户给了我们短代码`[myshortcode 7 name="My name"]`。然后，我们在回调函数的第一个参数中找到下面的数组。

```
Array
Array
(
    [0] => 7
    [name] => My name
)
```

如果有一个以上的未命名属性，区分它们的唯一方法是它们的顺序。例如，如果用户插入`[myshortcode 7 name="My name" display test="value" 22]`，我们有下面的数组。

```
Array
(
    [0] => 7
    [name] => My name
    [1] => display
    [test] => value
    [2] => 22
)
```

通常命名属性更好，因为它们更容易检索。然而，如果您将未命名的属性限制在很小的数量，或者如果它们仅作为布尔值使用，那么未命名的属性就不是大问题。事实上，像`[myshortcode name="Me" show]`这样的短代码肯定比`[myshortcode name="Me" show="true"]`更适合用户输入。

对于布尔值，未命名的属性是一个好主意:如果它们存在，我们将它们设置为`true`，否则设置为`false`。然而，这里还有一个问题:默认值呢？

如果应用`shortcode_atts()`函数，返回的数组将会丢失数字键。换句话说，在应用了`shortcode_atts()`函数之后，您将无法检索未命名的属性。

这就是为什么，如果你只对布尔值使用未命名的属性，你应该创建一个函数来规范化你的属性。您查看属性数组，如果键是一个数字，您将属性更改为一个已命名的属性，将其值设置为`true`以指示该参数被请求。

```
function normalize_attributes($atts) {
    foreach ($atts as $key => $value) {
    	if (is_int($key)) {
            $atts[$value] = true;
            unset($atts[$key]);
    	}
	}

	return $atts;
}
```

那么你现在需要做的就是在应用`shortcode_atts()`之前使用这个函数。这样，您的未命名属性将仍然存在，您甚至可以给它们默认值，如下例所示。

```
function replace_my_shortcode($atts, $content = '') {
    $atts = normalize_attributes($atts);
    $atts = shortcode_atts(array(
            'name' => 'World',
            'hide' => false
        ), $atts);

    if (!$atts['hide'])
        return 'Hello ' . $atts['name'] . '!';
    else
        return '';
}
```

在这个例子中，如果用户插入`[myshortcode name="You"]`，我们显示*“你好！”*。但是，如果检索到的短代码是`[myshortcode hide]`，那么我们什么也不显示。荒谬，但你明白这个想法！

## 最后

Shortcode API 是开发人员的强大工具。

它可以做很多不同的事情，从插入浮动块引用到显示带有许多选项的视频。

使用属性可以让用户设置各种细节，但是一定要记录这些选项，让用户发现短代码的所有功能。

为了让他们的生活更容易，你也可以在 WordPress 编辑器中添加一个按钮，自动将你的短代码插入文章中。例如，如果您的短代码使用媒体文件，您可能会对最近这篇关于[添加媒体按钮](https://www.sitepoint.com/adding-a-media-button-to-the-content-editor/)的文章感兴趣。

一些受本教程中描述的函数启发的短代码的例子被组合在一个测试插件中，你可以在这里下载。

## 分享这篇文章