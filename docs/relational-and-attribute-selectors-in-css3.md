# CSS3 中的关系和属性选择器

> 原文：<https://www.sitepoint.com/relational-and-attribute-selectors-in-css3/>

以下是我们的书的摘录，由 Alexis Goldstein、Louis Lazaris 和 Estelle Weyl 撰写的《真实世界的 CSS3，第二版。世界各地的商店都有出售，或者你可以在这里买到电子书。

### CSS3 选择器

CSS 选择器是 CSS 的核心。如果没有选择器来定位页面上的元素，修改元素的 CSS 属性的唯一方法就是使用元素的`style`属性，并内联声明样式，这是很笨拙且不可维护的。所以我们使用选择器。最初，CSS 允许通过类型、类和/或 ID 来匹配元素。这需要在我们的标记中添加 class 和 ID 属性，以创建钩子并区分相同类型的元素。CSS2.1 增加了伪元素、伪类和组合子。有了 CSS3，我们几乎可以用各种各样的选择器来定位页面上的任何元素。

在下面的描述中，我们将包括 CSS 早期版本中提供给我们的选择器。之所以包括它们，是因为虽然我们可以使用 CSS3 选择器，但 CSS3 之前的选择器也是 CSS 选择器第 3 级规范的一部分，并且仍然受到支持，因为 CSS 选择器第 3 级是在它们的基础上扩展的。即使对于那些已经存在了相当长时间的选择器，也值得在这里浏览一遍，因为旧规范中有一些隐藏的宝石，很少有开发人员知道。注意，所有现代浏览器，包括 IE9 及以上版本，都支持所有 CSS3 选择器。

### 关系选择器

关系选择器基于元素与标记中另一个元素的关系来定位元素。从 IE7+开始，所有这些都受支持，并且在所有其他主流浏览器中也受支持:

#### 后代组合子(`E F`)

你应该对这个很熟悉。后代选择器的目标是作为元素`E`的后代(子元素、孙元素、曾孙元素等等)的任何元素`F`。例如，`ol li`的目标是有序列表中的`li`元素。这将包括嵌套在`ol`中的`ul`中的`li`元素，这可能不是您想要的。

#### 子组合子(`E > F`)

该选择器匹配作为元素`E`的 *直接子元素* 的任何元素`F`——任何进一步嵌套的元素都将被忽略。
继续这个例子，`ol > li`只会将
直接定位到
`ol`内的`li`元素，而忽略那些嵌套在
`ul`内的元素。

#### 相邻子字符串或下一个同级选择器(`E + F`)

这将匹配与`E`共享
的任何元素`F`，并且在标记中 `E`之后直接出现
*。例如，`li + li`将指向给定容器中除第一个`li`之外的所有`li`元素。*

#### 常规子行，或跟随同级选择器(`E ~ F`)

这个有点棘手。它将匹配与任何`E`共享相同父元素的任何元素`F`，并且在标记中位于它之后。因此，`h1 ~ h2`将匹配跟在`h1`后面的任何`h2`，只要它们共享同一个直接父元素——也就是说，只要`h2`没有嵌套在任何其他元素中。

让我们看一个简单的例子:

```
<article>
  <header>
    <h1>Main title</h1>
    <h2>This subtitle is matched </h2>
  </header>
  <p> blah, blah, blah …</p>
  <h2>This is not matched by h1 ~ h2, but is by header ~ h2</h2>
  <p> blah, blah, blah …</p>
</article>
```

选择器字符串`h1 ~ h2`将匹配第一个`h2`，因为`h1`和`h2`都是`header`的子代或直接后代。您将在代码片段中看到的下一个`h2`不匹配，因为它的父级是`article`，而不是 header。然而，它会匹配`header ~ h2`。类似地，`h2 ~ p`只匹配最后一段，因为第一段在与它共享母文章的`h2`之前。

### 注意:为什么没有“父”选择器？

您会注意到，到目前为止，还没有“父”或“祖先”选择器，也没有“前面的兄弟”选择器。浏览器在决定是否应用某个样式之前，必须沿 DOM 树向上返回，或者递归进入嵌套元素集，这种性能妨碍了具有本地“沿 DOM 树向上”选择器的能力。

jQuery 包含了`:has()`作为祖先选择器。这个选择器正在被考虑用于 CSS 选择器级别 4，但是还没有在任何浏览器中实现。如果它被实现，我们将能够使用`E:has(F)`来查找将`F`作为后代的`E`、`E:has(> F)`，查找将`F`作为直接子代的`E`、`E:has(+ F)`，查找直接在兄弟`F`之前的`E`，等等。

浏览*html 5 Herald*的样式表，您会看到我们在很多地方使用了这些选择器。例如，当确定站点的整体布局时，我们希望三列`div`向左浮动。为了避免这种样式被应用到嵌套在其中的任何其他`div`中，我们使用子选择器:

```
main > div {
  float: left;
  overflow: hidden;
}
```

随着我们在接下来的几章中向站点添加新的样式，您将会看到很多这样的选择器类型。

### 属性选择器

CSS2 引入了几个属性选择器。这些允许基于属性匹配元素。CSS3 扩展了那些属性选择器，允许一些基于模式匹配的定位。CSS 选择器级别 4 增加了一些功能:

`E[attr]`

匹配任何具有属性`attr`的元素`E`，而不考虑属性的值。我们在第 4 章中利用了这一点来设计所需输入的样式；`input:required`可以在最新的浏览器中工作，但是`input[required]`有同样的效果，在 IE7 和 IE8 中也可以工作。

`E[attr=val]`

将属性为`attr`的任何元素`E`与精确值`val`匹配。虽然不是新的，但它有助于定位表单输入类型；例如，用`input[type=checkbox]`定位复选框。

`E[attr|=val]`

匹配任何元素`E`，其属性`attr`要么具有值`val`，要么以`val-`开头。这是最常用的`lang`属性。例如，`p[lang|="en"]`将匹配任何被定义为英语的段落，无论它是英国英语还是美国英语，带有`&lt;p lang="en-uk">`或`&lt;p lang="en-us">`。

属性=值

匹配任何元素`E`，其属性`attr`的值中包含完整的单词`val`，由空格包围。例如，`.info[title~=more]`将匹配具有包含单词“more”的`title`属性的类信息的任何元素，例如“单击此处获取更多
信息”

`E[attr^=val]`

匹配任何属性`attr`以值`val`开始的元素`E`。换句话说，`val`匹配属性值的开头。

`E[attr$=val]`

匹配属性`attr` *结束于`val`中* 的任意元素`E`。换句话说，`val`匹配属性值的结尾。

`E[attr*=val]`

匹配属性`attr`与属性内任意位置的`val`匹配的任意元素`E`。它类似于`E[attr~=val]`，除了`val`可以是单词的一部分。使用与前面相同的示例，`.fakelink[title*=info] {}`将匹配任何具有类`fakelink`的元素，该类具有包含字符串`info`的`title`属性，例如“单击此处获取更多信息”

在这些属性选择器中，`val`的值对于 HTML 中区分大小写的值是区分大小写的。例如，`input[class^="btn"]`区分大小写，因为类名区分大小写，但是`input[type="checkbox"]`不区分大小写，因为在 HTML 中`type`值不区分大小写。

如果该值是字母数字，则不一定要加引号，但有一些例外。空字符串、以数字开头的字符串、两个连字符和其他特殊字符需要加引号。因为有例外，所以当你需要的时候，养成引用的习惯是个好主意。

在 CSS 选择器级别 4 中，我们可以通过在右括号`E[attr*=val i]`前包含一个`i`来区分大小写。

## 分享这篇文章