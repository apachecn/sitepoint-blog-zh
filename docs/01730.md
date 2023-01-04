# HTML5 表单:新控件

> 原文：<https://www.sitepoint.com/html5-forms-new-controls/>

![htmlcss2thumb](img/d1b531ecd770df998ea32dbc2cf0f23c.png)

以下是我们的书的摘录，由 Alexis Goldstein、Louis Lazaris 和 Estelle Weyl 撰写的《真实世界的 CSS3，第二版。世界各地的商店都有出售，或者你可以在这里买到电子书。

我们已经讨论了 input 元素的`type`属性的新值，以及对大多数表单元素有效的一些属性。但是 HTML5 web 表单还能为我们提供更多！HTML5 表单规范中有五个新的表单元素:`datalist`、`output`、`keygen`、`progress`和`meter`。我们在上面提到了`datalist`。我们在上一章向您介绍了`progress`和`meter`，因为它们在表单之外通常很有用。让我们回顾一下，看看另外两个要素。

#### `progress`和`meter`元素

两个最著名的 HTML5 元素是`progress`和`meter`元素。

`meter`元素提供一个标尺，显示一个范围内的一般值。您提供最小值(`min`)和最大值(`max`)以及介于这些最小值和最大值之间的必需值`value`。虽然许多人认为它是一个表单控件，其属性类似于一些数字输入类型，但它没有`name`属性，也不会在表单提交时被提交。

`meter`将最小值默认为`0`或血糖仪的`value`，以较低者为准。最大值默认为`1`或血糖仪的`value`，以较高者为准。当存在最小值、最大值和最佳值时，使用`meter`，该值可以像测试等级、油箱液位或血压一样上下波动。有了这三个属性，支持 meter 包括 Android 4.4+(但不支持 iOS7 或 IE11)的浏览器都会显示绿色的计量器。

`meter`使我们能够用`low`、`high`和`optimum`值显示值何时在正确的范围内。如果`value`在`min`和`low`之间，则仪表为黄色。如果值在`low`和`high`值之间，则仪表为绿色。如果`value`在`high`和`max`之间，则为红色。目前`optimum`值没有明显的影响。

`meter`元素不应用于指示进度；相反，使用一个`progress`条来表示任务完成的百分比。

进度属性包括`max`和`value`，进度总是在 0 到 100%完成之间。浏览器会计算该值占最大值的百分比，并相应地调整进度条的长度。它显示一个部分填充的从灰色到蓝色的进度条，其中全灰色为 0%，全蓝色为 100%。

如果没有包含`value`，进度条是不确定的。Chrome、Opera、Safari 和 Firefox 以动画条的形式显示不确定的进度，IE 则以动画点的形式显示。

与`meter`不同的是，`progress`只朝向`max`值的 100%的方向。演示默认为内嵌块，因此您可以在`progress`元素上设置`width`和`height`。Height 不会改变风格化栏的实际高度(与`meter`不同),但会增加其下方的空间。

#### `output`元素

元素的目的是接受和显示计算的结果。当用户可以看到值，但不能直接操作它时，以及当值可以从表单中输入的其他值派生时，应该使用`output`元素。一个例子可能是购物车中扣除运费和税费后的总成本。

`output`元素的值包含在开始和结束标记之间。通常，在浏览器中使用 JavaScript 来更新这个值是有意义的。`output`元素有一个`for`属性，该属性用于引用表单字段的 id，表单字段的值被用于计算`output`元素的值。

与表单一起提交的还有`output`元素的`name`和`value`。

`keygen`元素是一个控件，用于生成一个[公钥-私钥对](http://en.wikipedia.org/wiki/Public-key_cryptography)，并从该密钥对中提交公钥。Opera、Chrome、Safari、Android、Firefox 都支持这个元素，把它渲染成下拉菜单，有生成键长度的选项；不过，它们都提供了不同的选择。iOS7 和 IE11 中仍然不支持。

`keygen`元素引入了两个新属性:`challenge`属性指定一个与公钥一起提交的字符串，而`keytype`属性指定生成的密钥的类型。在撰写本文时，唯一支持的`keytype`值是`rsa`，这是公钥加密中使用的一种常见算法。

#### `contenteditable`属性

虽然使用最合适的表单元素总是最好的，但有时现有的表单元素不能满足我们的需求；例如，没有一个表单控件能成为好的内嵌 WYSIWYG 文本编辑器。

不过，有一个迂回的解决方案。HTML5 文档中的任何元素都可以通过`contenteditable`属性进行编辑。`contenteditable`属性，简单地写成`contenteditable`或`contenteditable="true"`，使包含它的元素可编辑。你通常会在 divs 上找到这个属性，但是你甚至可以让一个设置为 `"display:block"`的`style`元素可编辑，并且动态地改变 CSS。虽然默认情况下，任何本身不是表单控件的元素都不会在表单提交时与表单数据一起发送到服务器，但是您可以使用 JavaScript 将用户编辑的内容异步发送到服务器，或者在表单提交时发送。

如果您曾经见过一个可编辑的配置文件，其中要单击的元素看起来一点也不像表单控件，那么您实际上可能正在编辑一个`contenteditable`元素。对`contenteditable`组件的任何编辑实际上都更新了 DOM。

只需将`contenteditable`添加到元素中，就可以在所有浏览器中编辑该元素。此外，它的后代也将是可编辑的，除非`contenteditable="false"`被明确地应用于它们。虽然这确实更新了 DOM 客户端，但是您必须添加 JavaScript 来显式保存它。

### 对现有表单控件的更改

HTML5 中的表单控件还有一些其他的变化。

#### `form`元素

在本章中，我们一直在讨论适用于各种表单域元素的属性；然而，还有一些新的属性是特定于`form`元素本身的。

首先，正如我们所见，HTML5 提供了多种方式来本地验证表单字段；某些输入类型，例如`email`和`url`，以及`required`和`pattern`属性。但是，出于样式或语义的原因，您可能希望使用这些输入类型和属性，而不阻止表单被提交。新的布尔`novalidate`属性允许表单在没有对其字段进行本地验证的情况下提交。

接下来，表单不再需要定义`action`属性。您不再需要显式地声明 URL 来使用它进行表单提交。如果省略，表单的行为就好像`action`被设置为当前页面。您可以用激活表单提交的按钮输入类型的`formaction`属性编写或覆盖表单的`action`属性中定义的 URL。

最后，我们前面介绍的`autocomplete`属性也可以直接添加到`form`元素中；在这种情况下，它将应用于表单中的所有字段，除非这些字段用自己的`autocomplete`属性覆盖它。

#### `optgroup`元素

在 HTML5 中，你可以有一个`optgroup`作为另一个`optgroup`的子节点，这对多级`select`菜单很有用。

#### `textarea`元素

在 HTML 4 中，我们需要通过指定`rows`和`cols`属性的值来指定`textarea`元素的大小。在 HTML5 中，不再需要这些属性；你应该使用 CSS 来定义一个`textarea`的宽度和高度。

HTML5 中新增的是`wrap`属性。该属性适用于`textarea`元素，可以有值`soft`(默认值)或`hard`。使用`soft`，提交的文本除了用户实际输入的之外没有换行符，而`hard`将提交浏览器由于字段大小而引入的任何换行符。如果将`wrap`设置为 hard，则需要指定一个`cols`属性。

### 最后

不幸的是，我们无法涵盖所有内容——这本身就应该是一本书。然而，这是一个相当深入的介绍。随着对 HTML5 输入元素和属性支持的增长，网站需要越来越少的 JavaScript 来进行客户端验证和用户界面增强，而浏览器处理大部分繁重的工作。在可预见的未来，传统的用户代理很可能会继续存在，但是没有理由不向前发展和使用 HTML5 web 表单，在需要的地方用适当的 polyfills 和 fallbacks 来填补空白。

在下一章，我们将继续充实 HTML5 先驱报，加入许多人认为是 HTML5 的杀手级特性:原生视频和音频。

## 分享这篇文章