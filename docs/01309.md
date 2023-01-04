# 使用 JavaScript 的即时表单验证

> 原文：<https://www.sitepoint.com/instant-validation/>

HTML5 引入了几个新属性来实现基于浏览器的表单验证。`pattern`属性是一个正则表达式，它定义了`textarea`元素和大多数类型的`input`的有效输入范围。`required`属性指定一个字段是否是必需的。对于没有实现这些属性的遗留浏览器，我们可以使用它们的值作为 polyfill 的基础。我们还可以使用它们来提供更有趣的增强功能——即时表单验证。

我们必须非常小心，不要忘乎所以，创建过于激进的验证，破坏自然的浏览行为，妨碍人们的浏览。例如，我见过无法将`Tab`从无效字段移开的表单——JavaScript 被用来(或者说*滥用了*)强迫焦点停留在字段内，直到它有效。这是非常差的可用性，并且直接违反了[易访问性指南](https://www.w3.org/TR/UNDERSTANDING-WCAG20/navigation-mechanisms-focus-order.html "Understanding Success Criterion 2.4.3: Focus Order (WCAG 2.0)")。

我们在这篇文章中要做的事情远没有那么麻烦。它甚至不是完整的客户端验证——它只是一个微妙的可用性增强，以一种可访问的方式实现，这(正如我在测试脚本时发现的)几乎与 Firefox 现在原生做的事情相同！

## 基本概念

在 Firefox 的最新版本中，如果一个`required`字段为空或者其值与`pattern`不匹配，那么该字段将显示红色轮廓，如下图所示。

![Invalid field indication in Firefox 16](img/f01ca14ed0a66ea9e3a7a871b2719e71.png)

当然，这不会马上发生。如果是这样的话，那么默认情况下每个必填字段都会有这个大纲。相反，这些轮廓只在你与该场进行了互动后的**中显示，这基本上(尽管不精确)类似于`onchange`事件。**

这就是我们将要做的，使用`onchange`作为触发事件。作为替代，我们可以使用`oninput`事件，只要任何值被键入或粘贴到字段中，该事件就会触发。但这真的是*太*即时，因为它可以很容易地在打字时快速连续地触发许多类型，创建一个闪烁的效果，这对一些用户来说会很烦人或不可能分散注意力。而且，在任何情况下，`oninput`都不会像`onchange`那样从编程输入中触发，我们可能需要它来处理像来自第三方附加组件的自动完成这样的事情。

## 定义 HTML 和 CSS

让我们来看看我们的实现，从它所基于的 HTML 开始:

```
<form action="#" method="post">
  <fieldset>

    <legend><strong>Add your comment</strong></legend>

    <p>
      <label for="author">Name <abbr title="Required">*</abbr></label>
      <input 
        aria-required="true"
        id="author"
        name="author"
        pattern="^([- \w\d\u00c0-\u024f]+)$"
        required="required"
        size="20"
        spellcheck="false"
        title="Your name (no special characters, diacritics are okay)"
        type="text"
        value="">
    </p>

    <p>
      <label for="email">Email <abbr title="Required">*</abbr></label>
      <input 
        aria-required="true"
        id="email"
        name="email"
        pattern="^(([-\w\d]+)(\.[-\w\d]+)*@([-\w\d]+)(\.[-\w\d]+)*(\.([a-zA-Z]{2,5}|[\d]{1,3})){1,2})$" 
        required="required"
        size="30"
        spellcheck="false"
        title="Your email address"
        type="email"
        value="">
    </p>

    <p>
      <label for="website">Website</label>
      <input
        id="website"
        name="website"
        pattern="^(http[s]?:\/\/)?([-\w\d]+)(\.[-\w\d]+)*(\.([a-zA-Z]{2,5}|[\d]{1,3})){1,2}(\/([-~%\.\(\)\w\d]*\/*)*(#[-\w\d]+)?)?$"
        size="30"
        spellcheck="false"
        title="Your website address"
        type="url"
        value="">
    </p>

    <p>
      <label for="text">Comment <abbr title="Required">*</abbr></label> 
      <textarea
        aria-required="true"
        cols="40"
        id="text"
        name="text"
        required="required"
        rows="10"
        spellcheck="true"
        title="Your comment"></textarea>
    </p>

  </fieldset>
  <fieldset>

    <button name="preview" type="submit">Preview</button>
    <button name="save" type="submit">Submit Comment</button>

  </fieldset>
</form> 
```

这个例子是一个简单的注释表单，其中有些字段是必填的，有些是已验证的，有些是两者都是。有`required`的字段也有`aria-required`，为不理解新的`input`类型的辅助技术提供回退语义。

[ARIA 规范](https://www.w3.org/TR/wai-aria/)还定义了一个`aria-invalid`属性，这就是我们要用来指示字段何时无效(HTML5 中没有对应的属性)。`aria-invalid`属性显然提供了可访问的信息，但它也可以用作 CSS 挂钩来应用红色轮廓:

```
input[aria-invalid="true"], textarea[aria-invalid="true"] {
  border: 1px solid #f00;
  box-shadow: 0 0 4px 0 #f00;
} 
```

我们可以只使用`box-shadow`而不用`border`，坦率地说，那样看起来会更好，但是那样我们就没有不支持框阴影的浏览器的指示了，比如 IE8。

## 添加 JavaScript

现在我们有了静态代码，我们可以添加脚本了。我们首先需要一个基本的`addEvent()`函数:

```
function addEvent(node, type, callback) {
  if (node.addEventListener) {
    node.addEventListener(type, function(e) {
      callback(e, e.target);
    }, false);
  } else if (node.attachEvent) {
    node.attachEvent('on' + type, function(e) {
      callback(e, e.srcElement);
    });
  }
} 
```

接下来，我们需要一个函数来确定一个给定的字段是否应该被验证，这个函数简单地测试它既不是禁用的也不是只读的，并且它有一个`pattern`或`required`属性:

```
function shouldBeValidated(field) {
  return (
    !(field.getAttribute("readonly") || field.readonly) &&
    !(field.getAttribute("disabled") || field.disabled) &&
    (field.getAttribute("pattern") || field.getAttribute("required"))
  );
} 
```

前两个条件看似冗长，但却是必要的，因为元素的`disabled`和`readonly`属性不一定反映其属性状态。例如，在 Opera 中，具有硬编码属性`readonly="readonly"`的字段仍然会为其`readonly`属性返回`undefined`(点属性只匹配通过脚本设置的状态)。

获得这些实用程序后，我们可以定义主验证函数，该函数测试字段，然后执行实际验证(如果适用):

```
function instantValidation(field) {
  if (shouldBeValidated(field)) {
    var invalid =
      (field.getAttribute("required") && !field.value) ||
      (field.getAttribute("pattern") &&
        field.value &&
        !new RegExp(field.getAttribute("pattern")).test(field.value));
    if (!invalid && field.getAttribute("aria-invalid")) {
      field.removeAttribute("aria-invalid");
    } else if (invalid && !field.getAttribute("aria-invalid")) {
      field.setAttribute("aria-invalid", "true");
    }
  }
} 
```

因此，如果某个字段是必需的，但没有值，或者它有一个模式和一个值，但值与模式不匹配，则该字段是无效的。

由于`pattern`已经定义了正则表达式的字符串形式，我们所要做的就是将该字符串传递给`RegExp`构造函数，这将创建一个 regex 对象，我们可以根据该值进行测试。但是，我们必须*预先测试*值以确保它不为空，这样正则表达式本身就不必考虑空字符串。

一旦我们确定了一个字段是否无效，我们就可以控制它的`aria-invalid`属性来指示该状态——将它添加到一个还没有它的无效字段中，或者将它从一个有它的有效字段中删除。简单！最后，为了将这一切付诸实施，我们需要将验证函数绑定到一个`onchange`事件。它*应该*就这么简单:

```
addEvent(document, "change", function(e, target) {
  instantValidation(target);
}); 
```

然而，要做到这一点，`onchange`事件**必须冒泡**(使用一种通常被称为[事件委托](https://www.sitepoint.com/event-bubbling-javascript/)的技术)，但在 Internet Explorer 8 和更早的版本中，`onchange`事件*不会冒泡*。我们可以选择忽略这些浏览器，但我认为这将是一个遗憾，特别是当问题是如此简单的变通办法。这只是意味着有点复杂的代码——我们必须获得`input`和`textarea`元素的集合，遍历它们并将`onchange`事件分别绑定到每个字段:

```
var fields = [
  document.getElementsByTagName("input"),
  document.getElementsByTagName("textarea")
];
for (var a = fields.length, i = 0; i < a; i++) {
  for (var b = fields[i].length, j = 0; j < b; j++) {
    addEvent(fields[i][j], "change", function(e, target) {
      instantValidation(target);
    });
  }
} 
```

## 结论及其他

这就是我们所拥有的——一个简单且非侵入性的即时表单验证增强，提供可访问的可视化提示来帮助用户完成表单。你可以看看下面的演示:

在 [CodePen](https://codepen.io) 上通过 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )看笔[即时表单验证](https://codepen.io/SitePoint/pen/XgvNNe/)。