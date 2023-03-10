# 掌握链接管理 API

> 原文：<https://www.sitepoint.com/mastering-wordpress-links-manager-api/>

在之前的一篇文章中，我们看到了什么是 [WordPress 链接管理器](https://www.sitepoint.com/wordpress-links-manager/)，如何访问它，以及如何使用它来管理链接的排序列表。我们还学习了如何显示我们的链接，这要感谢默认的小部件。

然而，这个小部件并不完美:虽然它足以显示一个基本的 blogroll，但我们不能用它做所有我们想做的事情。在接下来的两篇文章中，我们将看到如何使用链接管理器 API 来显示我们的链接。在本文中，我们将重点描述 API 的主要功能，然后在下一篇文章中学习如何使用其他功能。

## 一个列出我们的链接的功能

我们在链接管理器 API 中找到了几个函数。然而，我们不会全部使用它们。事实上，如果你已经看过一些使用这个 API 的代码，你可能也看过下面三个函数之一的使用:`get_links()`、`get_links_list()`和`wp_get_links()`。你不应该使用这些函数:它们都被否决了。

本文我们要研究的函数是`wp_list_bookmarks()`。它所做的只是以列表的形式显示你的链接，但是一些选项允许我们自定义结果，这就是为什么这个函数如此有趣。

基本上，你可以使用`wp_list_bookmarks()`函数而不用给它任何参数。然后它会一个接一个地显示所有的类别。对于这些类别中的每一个，它都显示了一个无序的列表，其中包含与它们的图像的不同链接。在没有对`wp_list_bookmarks()`函数甚至 CSS 进行任何定制的情况下，我们会看到下面的结果。

![default output](img/3bea9abd6631a37d06a96afce0023715.png)

左侧的默认小部件有更好的结果。让我们看看不同的可用选项来个性化我们的展示。

## 个性化输出

`wp_list_bookmarks()`函数只接受一个参数:一个列出我们想要定制的所有选项的数组。在这里，我们将这些选项分为两类:与数据本身相关的选项(比如如何检索给定的类别，或者如何对链接进行排序)和与显示相关的选项(比如显示哪个标题)。

### 定制数据

#### 选择类别

如果你想在几个地方显示链接，很有可能你会选择一个特定的类别在每个地方显示。这要归功于`category`选项。因为这是我们看到的第一个选项，所以一个例子会更清楚，所以我在下面包括了一个。

```
$args = array(
'category' => '4,5'
);

wp_list_bookmarks($args);
```

感谢发送给`wp_list_bookmarks()`函数的参数，我们只显示了两个类别，由它们的 ID 标识。在这里，您可以指定您想要的 id，用逗号分隔。默认情况下，该选项设置为`' '`以显示所有类别。

如果您愿意，可以使用选项`exclude_category`列出您想要隐藏的类别，而不是列出您想要显示的类别。和前面的选项一样，它的值是要隐藏的类别 id 列表。默认情况下，由于值`' '`，没有类别被隐藏。

类别的 ID 并不总是实用的。这就是为什么你可以使用选项`category_name`。默认情况下，它的值是`' '`并且显示所有类别，但是您可以指定一个类别的名称，以便只显示这个类别。

```
$args = array(
'category_name' => 'tools'
);
```

请注意，此选项接受类别的名称，而不是其 slug。这个选项的值是不区分大小写的:上面的例子在我的类别名为“ *Tools* ”时有效。最后，你应该知道你不能用这个选项定义一个类别列表:只接受一个名字。所以，如果你想显示几个类别，你必须使用`category`选项。

如果您的脚本不关心类别，并且您想要显示所有的链接，而不管它们的类别，您可以将`categorize`选项设置为`false`。一旦设置了这个，它将不会根据链接的类别对链接进行排序。相反，通过这个选项，你会看到所有的链接，在一个名为“*书签*”的假类别中。

![categorize false](img/ade4bb525e3050de18c220ab7b22032f.png)

您可以将选项`categorize`与其他选项结合使用。例如，如果您将选项`categorize`设置为`false`，您可以选择仅显示带有`category`的类别`4`和`5`，但不显示它们在不同的类别中。

```
$args = array(
'category' => '4,5',
'categorize' => false
);
```

#### 选择订单

如果你用不止一个 ID 尝试了`category`选项，你应该注意到 WordPress 自动对输出进行了排序。您可以指定`'4,5'`或`'5,4'`，顺序将始终相同，除非您使用另一个选项。

这个选项是`category_orderby`。默认情况下，由于默认值`name`，类别按字母顺序显示。你可以选择用值`id`按类别 ID 排序，用值`slug`按类别 slug 排序，或者用值`count`按类别包含的链接数量排序。

第二个选项名为`category_order`，允许您通过选择值`DESC`来颠倒顺序。默认情况下，该选项设置为`ASC`，顺序不颠倒。

下面的例子显示了两个类别:第一个类别包含最多的链接。

```
$args = array(
'category' => '5,6',
'category_orderby' => 'count',
'category_order' => 'DESC'
);
```

可以找到模拟选项来对链接进行排序。感谢`orderby`选项，事实上，你可以选择链接的排序方式。接受许多值，您可以使用几个值(用逗号分隔)，如果第一个选择的顺序认为两个链接“相等”，这将非常有用。

你可以用 ID 和`link_id`或者 URL 和`url`对链接进行排序。他们的名字也可以用；使用`name`，链接按字母顺序排序，而使用`length`，它们按照名称的长度排序。由于有了`owner`值，你可以根据添加链接的用户对链接进行排序。

有些值没有其他值有用:你可以按照链接的目标(`target`)、描述(`description`)、附加注释(`notes`)、RSS 地址(`rss`)甚至是属性`rel`(`rel`)对链接进行排序。

您也可以选择使用您分配给链接的等级(`rating`)对链接进行排序。一个更有趣的值是`updated`:它根据链接指向的页面最后一次更新的时间对链接进行排序。最后，如果您不想使用特定的顺序，可以使用`rand`值。

默认情况下，链接按照名称排序。您可以通过在`order`选项中指示`DESC`来颠倒这个顺序或您选择的任何其他顺序。

如果您有许多链接，但只想显示给定数量的链接，您可以在`limit`选项中指明这个数量。默认情况下，它被设置为`-1`并显示所有链接。

下面是一个结合了上述几个选项的例子。它按照我们在前一个例子中定义的顺序显示了两个类别。在这些类别中，链接是按照它们的等级排序的:顺序是相反的，等级最高的放在最前面。此外，我们不会按类别显示超过五个链接。

```
$args = array(
// Sort the categories
'category' => '5,6',
'category_orderby' => 'count',
'category_order' => 'DESC',

// Sort the links
'orderby' => 'rating',
'order' => 'DESC',
'limit' => 5
);
```

#### 显示隐藏的链接

当您添加新链接时，您可以决定将其设为私有。WordPress 的默认窗口不显示私人链接，但是你的脚本可以，这要感谢`wp_list_bookmarks()` : `hide_invisible`中的一个选项。该选项是一个布尔值。默认情况下，它被设置为`true`并隐藏私人链接，但你可以设置为`false`，它会显示你所有的链接，即使其中一些是私人链接。

```
// Show me all the links, even the private ones!
$args(
'hide_invisible' => false
);
```

请注意，如果你的脚本可以被除你之外的其他 WordPress 用户使用(例如，如果你的脚本包含在其他人可以使用的主题或插件中)，你应该警告这些用户你的脚本显示了私人链接，因为这不是我们所期望的行为。

### 定制显示

一个叫做`echo`的选项允许你决定`wp_list_bookmarks()`是否必须返回(`false`或者显示(`true`，默认值)链接。但是，它返回的是它回显的字符串，所以自定义它并不是很实用。这就是为什么其他选项允许我们个性化显示。

首先，`title_li`选项只在一种情况下有用；当你设置`categorize`为`false`时。事实上，如果链接没有分类，WordPress 会使用这个选项在链接上方显示标题。您可以将其设置为`null`，使其不显示任何标题。

```
$args = array(
'categorize' => false,
'title_li' => 'Here are ALL my links!'
);
```

默认情况下，类别的名称显示在一个`h2`标签中。您可以通过使用选项`title_before`和`title_after`来改变这一点，这两个选项分别在类别名称前后显示一个文本(或者如果您将`categorize`设置为`false`，则显示一个假类别)。不要忘记在`title_after`关闭在`title_before`打开的标签！

```
// Display the titles in a smaller heading
$args = array(
'title_before' => '<h4>',
'title_after' => '</h4>'
);
```

每个类别(即使是假的)都在一个列表项中。默认情况下，这个项目使用`linkcat` CSS 类，但是您可以使用`class`选项对其进行个性化设置。

```
$args = array(
'class' => 'my-class'
);
```

您可以不仅仅使用`class`选项。使用`category_before`和`category_after`，你可以个性化显示在每个类别之前和之后的文本。默认情况下，它定义了一个`li`标签，其中包含当前类别中的所有链接。

```
$args = array(
'category_before' => '<div id=%id class=%class>',
'category_after' => '</div>'
);
```

正如你所看到的，你可以插入 WordPress 使用的`%id`和`%class`标签来显示相应的值。

最后，我们找到`before`(默认值:`'<li>'`)和`after`(默认值:`'</li>'`)，它们与`category_before`和`category_after`类似，但用于每个书签。同样的，我们找到`link_before`和`link_after`，它们是`title_before`和`title_after`的等价物。

```
$args = array(
'before' => '<li class="a-link">',
'after' => '</li>',
'link_before' => '<em>',
'link_after' => '</em>'
);
```

## 生成表格

为了了解如何在一个具体的例子中使用和组合几个选项，我们现在将学习如何用一个函数生成一个列出所有链接的表格:`wp_list_bookmarks()`。

对于这个例子，我创建了九个链接，分为三类。每个类别中有三个链接。第一个环节评分 10 分，第二个 8 分，最后一个 7 分。我们将使用`wp_list_bookmarks()`生成一个包含所有这些链接的表格。该表将包含一个标题行和另外三行(每个类别一行)。在每一行中，我们将找到类别的名称和三个链接，按照它们的等级排序。看看下面的图片就知道结果了。

![Table](img/831044d411f98353cfc4cd305cddecc8.png)

你可以复制这个例子，而不需要下面提供的那么多链接。事实上，你只需要在每个类别中有相同数量的链接(以及每个类别中相同的评级系统)。

首先，让我们创建我们的表。

```
<table>
<tr>
<th>Category</th>
<th>10</th>
<th>8</th>
<th>7</th>
</tr>

< ?php
$args = array(
);

wp_list_bookmarks($args);
?>
</table>
```

目前，这段代码不起作用:默认情况下，` wp_list_bookmarks()`会生成一个列表，您应该知道像这样将列表插入到表中是不正确的。因此，让我们在我们的“$args”数组中指出正确的选项。

首先，我们选择要显示的类别。如果您想显示所有类别，可以跳过此选项。在这里，我选择显示三个类别，按名称排序。

```
$args = array(
// Categories to display
'category' => '4,5,6',
'category_orderby' => 'name'
);
```

然后，我们对链接进行排序。我们在标题行中选择首先显示 10 级链接，所以我们要求`wp_list_bookmarks()`按照它们的等级对链接进行排序，然后我们颠倒默认的升序。

```
$args = array(
// Sort the links
'orderby' => 'rating',
'order' => 'DESC'
);
```

现在，我们需要表明我们不想创建一个列表。我们将在这里使用`category_before`和`category_after`选项来定制每个类别的代码。在分类之前，我们打开一个`tr`标签，然后马上关闭。

```
$args = array(
// One category = one line
'category_before' => '<tr>',
'category_after' => '</tr>'
);
```

这比默认输出要好，默认输出为每个类别显示一个`li`标签。但是这还不够，因为我们希望在标题单元格中显示类别的名称。为此，我们使用`title_before`和`title_after`选项来定制每个名字前后的代码。

```
$args = array(
// Category's name in a heading cell
'title_before' => '<th>',
'title_after' => '</th>'
);
```

最后，默认情况下，所有的链接都显示在一个`li`标签中:由于`before`和`after`选项，我们改为使用表格单元格。

```
$args = array(
// One link = one table cell
'before' => '<td>',
'after' => '</td>'
);
```

我们到了。你现在要做的就是把我们上面看到的选项结合起来，得到正确的结果。

```
$args = array(
// Categories to display
'category' => '4,5,6',
'category_orderby' => 'name',

// Sort the links
'orderby' => 'rating',
'order' => 'DESC',

// One category = one line
'category_before' => '<tr>',
'category_after' => '</tr>',

// Category's name in a heading cell
'title_before' => '<th>',
'title_after' => '</th>',

// One link = one table cell
'before' => '<td>',
'after' => '</td>'
);
```

现在，该问一个问题了:这个例子有用吗？理论上，是的。看看如何使用`wp_list_bookmarks()`函数生成列表之外的东西的具体例子是很有用的。可用的选项允许我们创建几乎任何我们想要的东西。

但在实践中，它并没有真正提供过多的帮助。`wp_list_bookmarks()`不是实现我们目标的完美功能。事实上，如果每个类别中的评级体系不完全相同怎么办？如果我们想显示评级，而不仅仅是链接的名称，该怎么办？

有很多事情是用`wp_list_bookmarks()`根本做不到的，这也是其他函数存在的原因。

## 最后

WordPress 链接管理器 API 是一个非常有用的工具，现在，由于这篇文章中提到的功能，你可以用你想用的方式使用它。

理论上，你可以通过使用可用的选项，只用`wp_list_bookmarks()`函数做很多事情。然而，这并不是创建一个可靠的可读代码的最佳解决方案。这就是为什么我们将在本系列的下一篇文章中看到的其他函数非常重要。



## 分享这篇文章