# 掌握 WordPress 类别 API

> 原文：<https://www.sitepoint.com/mastering-wordpress-categories-api/>

在之前的 SitePoint 文章中，由于函数`wp_list_categories()`，我提供了对 WordPress 类别 API 的[介绍。正如我们看到的，这个函数充满了不同的选项，这就是为什么如果我们想显示我们的类别列表，使用它是一个好主意。](https://www.sitepoint.com/wordpress-categories-api/)

![WordPress](img/46192d8a44f9e9ddf70100567a0d3945.png)

然而，它不能涵盖所有可能的情况，这就是我们在 Categories API 中找到其他函数的原因。这些函数允许我们检索原始数据:代替我们的类别的 HTML 列表，我们可以检索包含这些相同类别的数组。

后果就是更简单(而且妥妥的！)以一种特殊的方式显示这些数据，你可以把它想象成一棵 DOM 树。我们将从返回包含与我们的类别相关的所有数据的对象的函数开始。然后，我们会对只返回特定信息的函数感兴趣。

## 将一些类别作为对象检索

像链接管理器中的[链接一样，我们可以将类别作为包含所有属性的对象来检索。我们将通过一个只返回一个类别的函数来描述这些对象。然后，我们将使用我们新获得的知识来玩我们想要的所有类别！](https://www.sitepoint.com/further-master-the-wordpress-links-manager-api/ "How to Further Master the WordPress Links Manager API")

## 检索带有 ID 的给定类别

这里我们要介绍的第一个函数是`get_category()`。基本上，它允许您获取一个表示类别的对象，您将 ID 作为参数传递给该对象。

```
$cat = get_category(14); 
```

然后，`$cat`包含想要的对象。我们现在将描述一些我们感兴趣的性质。

## 类别 ID

首先，我们想要的类别的 ID。您可以在`cat_ID`属性中找到这个值。

```
$id = $cat->cat_ID; 
```

注意，这个值在这里不是很有用，因为我们已经知道了 ID(我们把它作为函数的参数传递了！).但是我们将在下面看到，还有其他方法来检索一个类别:在这种情况下，知道如何获得一个类别对象中的 ID 可能是有用的。

如果您的类别是另一个类别的子类别，您还可以通过属性`category_parent`检索其父类别的 ID。请注意，如果您的类别没有父类别，该值将被设置为`0`。

## 获取所有数据！

当我们显示一个类别的信息时，一条重要的信息就是它的名称。您可以通过属性`cat_name`检索它。同样，通过`category_description`，您可以检索类别的描述。

```
<h1><?php echo $cat->cat_name; ?></h1>

<p><?php echo $cat->category_description; ?></p> 
```

ID 不是一个类别的唯一标识符:slug 是另一个字符串形式的标识符(通常是类别本身的名称，小写，没有空格或特殊字符)。您可以使用属性`category_nicename`或`slug`来检索 slug(两者都有效)。

最后，由于属性`category_count`，您还可以看到有多少文章包含在这个类别中。请注意，该计数器没有考虑我们可以在儿童类别中找到的帖子数量。

## 我对你撒谎了

这部分的标题是骗人的。事实上，对象并不是我们类别的唯一可用表示。您还可以选择在数组中检索它们，这要感谢给`get_category()`的第二个参数。

```
// Get an object (default behavior)
$cat = get_category(15, OBJECT);

// Get an associative array
$cat = get_category(15, ARRAY_A);

// Get an numbered array
$cat = get_category(15, ARRAY_N); 
```

关联数组中的键是对象的属性。但是，在编号数组中事情就复杂多了:顺序是一样的，但是你不能有明确的索引。

## 检索没有 ID 的类别

我们上面说过，id 不是一个类别的唯一标识符，类别的 slug 也可以用来检索它的信息。这可以通过功能`get_category_by_slug()`来实现。正如您所猜测的，它接受 slug 作为参数。

```
$cat = get_category_by_slug('my-goldfish'); 
```

我们将不描述返回的对象，因为它与我们对`get_category()`的描述相同。然而，您在这里没有选择:object 是唯一可用的类型，您将无法使用此函数检索数组。最后，请注意，如果不存在具有指定 slug 的类别，`get_category_by_slug()`将返回`false`。

## 检索几个类别

我们将看到两个函数来获取不同的类别列表。第一个函数允许您检索任何类别列表，而第二个函数将帮助您检索与给定帖子相关的类别。

## 获取您想要的所有类别

如果你读了前一部分，你不会被`get_categories()`的返回值迷惑，因为它返回了一个数组，列出了所有需要的类别，每个类别都由一个对象表示，这个对象就是我们上面描述的那个。

如果没有任何参数，该函数将返回您创建的所有类别。如果你写下下面一行:

```
$cats = get_categories(); 
```

然后`$cats`是一个包含所有类别的数组，按字母顺序排列。例如，如果我想检索第二个类别的名称，我所要做的就是`$cats[1]->cat_name`(更多信息请参见前面关于类别对象的部分)。

如果你对`$cats`变量做了`print_r()`，也许你会注意到一些奇怪的事情:在某些情况下，这个数组有一些“漏洞”。例如，我们可以直接从索引`0`跳到`4`，而看不到`1`、`2`和`3`。发生了什么事？这种奇怪的计数方式是什么？

事实上，这并不奇怪:如果你有效地注意到了这个“bug”，也许你也注意到了缺失的类别正是那些空的类别:默认情况下，WordPress 隐藏了空的类别。万岁，不是 bug！

我们可以修改这种行为，以及其他一些我们可以定制的东西，这要归功于我们可以发送给`get_categories()`的参数。像往常一样，这个唯一的参数是一个包含我们想要的所有选项的数组。

我推荐你阅读我们之前关于类别 API 的[文章，在那里我们描述了`wp_list_categories()`。事实上，`get_categories()`的(几乎)所有可用选项都是我们看到的`wp_list_categories()`的选项，所以我们不会重复描述它们的功能。](https://www.sitepoint.com/wordpress-categories-api/)

至于`wp_list_categories()`你可以用`orderby`和`order`选择你想要的顺序，用`number`限制返回类别的数量或者再用`hide_empty`选择获取空类别。您还可以使用选项`include`和`exclude`来获得正确的类别。

也许`hierarchical`选项值得解释一下:默认设置为`1`，你可以通过设置为`0`来取消它的效果。设置为`1`，该选项可以返回一些空类别，即使`hide_empty`设置为`1`。然而，您将只看到空的类别以及不为空的子类别(这样您就可以构建整个树)。

`pad_counts`选项还在，`child_of`也是。但是这里有了一个新的选项:`parent`，类似于`child_of`，不同的是`parent`只返回所选类别的直接子类别:如果这些子类别本身有子类别，则不会返回。

## 获取与文章相关的类别

一篇文章可以关联多个类别。当您使用`get_the_category()`时，您可以检索您为给定帖子设置的所有类别的数组列表。

默认情况下，该函数返回与当前文章相关的所有类别(您必须在循环中启用该行为)。但是，您可以通过将 ID 作为参数传递来检索与任何帖子相关联的类别。

```
// Get the categories of the post with ID 139
$cats = get_the_category(139); 
```

## 检索一些特定的数据

有时候，我们只需要特定的信息，比如类别的名称，或者它的 ID。在这些情况下，检索整个对象以仅获得一批信息是没有用的。这就是为什么有一些其他的功能来满足这些特定的需求。

## 检索类别的 ID

记住类别的名称总是比记住它的 ID 更容易(在安装之后，ID 是不一样的)。好消息是，如果您知道类别的名称，就可以检索到它的 ID。为此，我们可以使用`get_cat_ID()`函数:您向它传递一个类别的名称，它返回它的 ID。如果没有找到类别，返回值是`0`。

```
// Yes, I love this category
$cat_id = get_cat_ID('My goldfish'); 
```

如果你已经有了一个类别的 ID，但是不知道它的父类别的 ID，你也可以使用`get_ancestors()`。它返回一个包含所有祖先 id 的数组，从最近到最远。

举个例子会更清楚。假设我们有“*我的金鱼*”(ID 为 15)作为“*我的生活*”(ID 为 14)的子类别。现在我们给“*我的金鱼*”::*我的金鱼*(ID 为 16)的饲料添加一个孩子。然后是下面一行:

```
$cats_id = get_ancestors(16, 'category'); 
```

返回以下内容:

```
Array
(
    [0] => 15
    [1] => 14
) 
```

注意，我们必须向`get_ancestors()`表明我们在谈论类别。事实上，这个函数更加通用，可以包含比类别更多的字段(但这不是我们感兴趣的)。

## 检索一些字符串

我们可以从一个类别的名称中获取它的 ID，但是我们也可以反过来做:这就是`get_cat_name()`所做的。

```
// Returns the name of the category with ID 15
$cat_name = get_cat_name(15); 
```

多亏了`category_description()`，对一个类别的描述也可以做同样的事情。

```
// Returns the description of the category with ID 15
$cat_desc = category_description(15); 
```

你有没有注意到，从这篇文章开始，我们就没有讨论过一个类别的 URL？这是一个有趣的话题，因为它的形状会随着安装而改变(URL 重写？没有 URL 重写？).老实说，我不明白为什么这些信息没有包含在我们上面描述的对象中。然而，多亏了`get_category_link()`，我们可以检索某个类别的 URL。

```
// I want to display a link to my favorite category
$cat_id = get_cat_ID('My goldfish');
$cat_link = get_category_link($cat_id); 
```

然后我所要做的就是使用包含正确 URL 的`$cat_link`。

```
<a href="<?php echo $cat_link; ?>">My goldfish</a> 
```

## 短名单

如果你一直在开发 WordPress 主题，很可能你已经知道了`the_category()`。这个函数允许你回显当前文章(或任何其他文章)使用的类别列表。这个列表是一个字符串，所有的链接一个接一个，由您想要的字符串分隔(在第一个参数中指明)。

```
// Get a comma-separated list of the used categories
the_category(', '); 
```

这个函数的第三个参数是我们想要的文章 ID:如果你不在循环中，你必须使用这个参数。第二个参数很特别。

它允许你控制父子关系的显示方式，并且只在相关文章属于有父类别的类别时有用。默认情况下，该参数设置为空字符串，并且只显示子类别。如果您将此更改为`single`，那么父类别的名称也将显示在其子类别的同一链接中。最后，您还可以选择`multiple`:使用这个值，父类别和子类别显示在两个独立的链接中。

如果您喜欢在回显之前将结果存储到一个变量中，那么您可以使用`get_the_category_list()`来代替(它可以以与`the_category()`相同的方式使用)。

## 结论

这个 API 是一个非常全面的类别，它包含了许多非常有用的功能。当你想使用类别时，无论你需要什么，很有可能一个函数已经存在了。

事实上，我选择了描述一些最常用的函数，但是这个 API 包含了大量的其他函数。例如，有一个功能可以检查一篇文章是否在一个类别中。还有一个功能，显示由每个类别的复选框组成的列表(对管理面板有用)。

描述所有这些将比其他任何东西都更难消化。这就是为什么，如果你找不到这篇文章中的功能，不要犹豫，请在下面寻求帮助。

## 分享这篇文章