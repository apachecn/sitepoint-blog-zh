# setAttribute (W3C DOM 核心方法)

> 原文：<https://www.sitepoint.com/setattribute-w3c-dom-core-method/>

Returns

`void`

Throws

`INVALID_CHARACTER_ERR`, `NO_MODIFICATION_ALLOWED_ERR`

## 例子

```
element.setAttribute('rel', 'me');
```

上面的例子在元素上设置了一个值为`me`的`rel`属性。

因此，如果有问题的元素是这个 HTML:

```
<a href="http://www.brothercake.com/">brothercake</a>
```

那么上面的操作将导致如下结果:

```
<a href="http://www.brothercake.com/" rel="me">brothercake</a>
```

## 争论

`name` ()
required

对  的属性进行
的创建或修改。

`value` ()
required

用于
属性的字符串  。

## 描述

为该元素设置一个具有指定名称和值的新属性。

如果具有指定名称的属性已经存在，其  将被替换。

设置值时，不会解析该值，因此任何实体引用或其他标记都将被视为文字文本。为了创建包含实体的属性，规范建议创建一个  节点，将适当的  和  节点作为子节点，然后使用  将其添加到一个元素中，然而在实践中这很少起作用(详见  )。

此方法用于处理非命名空间属性；要添加命名空间属性，请使用 DOM 2  方法。

> ## 小心使用骆驼大小写的属性名
> 
> 在面向所有浏览器的代码中使用骆驼大小写名称时应该小心，因为在 XHTML 模式的 Opera 中，使用骆驼大小写名称设置属性可能会影响其对应的 DOM 属性。例如，如果将一个名为 tabindex 的属性设置为任何值，将会导致将 tabIndex 属性重置为 0(其默认值)，完全移除原始的 tabIndex 属性，并创建一个名为 tabIndex 且具有指定字符串值的新属性。
> 
> 这种行为可能是由于 XHTML 模式中处理大小写不一致造成的。在其他浏览器(Firefox 和 Safari)中，这样做将创建一个名为 tabindex 的新属性，同时保持原来的 tabIndex 属性及其对应的 tabIndex 属性不变。
> 
> 此说明仅适用于 XHTML 模式下的这些浏览器—在 HTML 模式 2 中，name 参数不区分大小写，因此 tabindex 被视为 tabIndex。

此外，HTML 中的 IE 不能设置 input 元素的 type 属性—试图这样做将会抛出一个错误(此命令不受支持)。它也不能设置样式属性(这样做没有任何效果)，也不能将事件处理属性设置为字符串(这样做没有任何效果，除非该元素已经有一个同名的事件处理属性，在这种情况下，设置新属性的操作将删除旧属性，什么也不留下—尽管 getAttribute 随后将返回设置的字符串值，但该元素上实际上没有活动的事件处理程序)。但是，如果函数作为值参数而不是字符串传递，IE 可以设置事件处理属性:

```
element.setAttribute('onclick', function() { alert("this works!"); });
```

最后，如果这个方法用于设置图像的 src 属性，那么随后从 getAttribute 返回的值将是一个完全合格的 URI；这与它在检索静态 HTML 中设置的 src 时的行为相同。然而，当设置链接的 href 时，随后返回的值将是设置的文字值；这不同于它在静态 HTML 中的行为，在静态 HTML 中，值作为限定的 URI 返回。

> ## 我怎么知道它是否有效？
> 
> 确定该方法是否按预期工作特别困难，因为可以设置任何名称的属性，尽管它可能不是您期望的属性。例如，在 Internet Explorer 中，您仍然可以设置一个名为 class 的属性，并随后使用 getAttribute('class ')检索它，但它不会与 className 属性对应，它将是一个完全独立的属性，对元素没有任何视觉差异。

在所有浏览器中，计算结果为布尔值(如 disabled)的属性只能设置为 true，设置为 false 没有任何效果。这是正确的行为，因为这样的属性应该只有一个可能的值(即 disabled =“disabled”)，或者未定义(因此应该使用 removeAttribute 对它们进行否定)。在 Opera 9.5、Firefox 和 Safari 中，属性值随后将返回 false，但该元素仍将被禁用，在 Opera 9.0 中，该值将继续返回 disabled，在 Internet Explorer 中，该值将继续返回 boolean true 这些都是元素状态的准确反映，即使它们有点混乱！但是，由于 IE 认为这些属性具有实际的布尔值，因此可以通过将其设置为布尔值而不是字符串来切换该值(并相应地禁用和启用元素):

```
element.setAttribute('disabled', false);
```

Internet Explorer 为 setAttribute 实现了第二个参数，这是一个区分大小写的标志，可以取值 0(不区分大小写)或 1(默认情况下，区分大小写)。该参数在 IE 中正常工作，不影响任何其他浏览器。

## 分享这篇文章