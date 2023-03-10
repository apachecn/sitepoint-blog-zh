# createElement (W3C DOM 核心方法)

> 原文：<https://www.sitepoint.com/createelement-w3c-dom-core-method/>

Returns

[`Element`](https://reference.sitepoint.com/javascript/Element)

Throws

[`INVALID_CHARACTER_ERR`](https://reference.sitepoint.com/javascript/DOMException#DOMException__INVALID_CHARACTER_ERR)

## 例子

```
var element = document.createElement('h1');

element.appendChild(document.createTextNode
    ('The man who mistook his wife for a hat'));
```

上面的例子
创建了一个`<h1>`元素，然后将文本添加到
中。

该操作的最终结果将是这样的 HTML:

```
<h1>The man who mistook his wife for a hat</h1>
```

## 争论

`tagname` ([`DOMString`](https://reference.sitepoint.com/javascript/domcore#domcore__DOMString))
required

元素的`tagName`。在 XML 中，这是区分大小写的；在 HTML 中，名称可以用任何
大小写来指定，但是会被转换成 HTML
标签名称的规范大写形式。

## 描述

创建一个指定类型的 [`Element`](https://reference.sitepoint.com/javascript/Element) 节点。

然后可以使用 [`Node`](https://reference.sitepoint.com/javascript/Node) 方法，如 [`appendChild`](https://reference.sitepoint.com/javascript/Node/appendChild) 或 [`insertBefore`](https://reference.sitepoint.com/javascript/Node/insertBefore) ，将创建的
元素添加到文档中。

创建的元素一创建就实现了
[`Element`](https://reference.sitepoint.com/javascript/Element)接口，因此
属性可以立即添加到其中，而不必先将它追加到
文档中。如果元素在这个文档
类型中有默认属性，那么这些属性会被自动创建并附加到
元素上。

此方法创建没有命名空间的元素；要创建一个
命名空间元素，请使用 DOM 2“createElementNS”`createElementNS`
方法。

## 返回值

所创建的元素节点，
将其 [`nodeName`](https://reference.sitepoint.com/javascript/Node/nodeName) 设置为指定的标签
名称，将其 [`localName`](https://reference.sitepoint.com/javascript/Node/localName) 、 [`prefix`](https://reference.sitepoint.com/javascript/Node/prefix) 、 [`namespaceURI`](https://reference.sitepoint.com/javascript/Node/namespaceURI)
设置为`null`

## 分享这篇文章