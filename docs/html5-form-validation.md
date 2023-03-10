# HTML5 表单验证

> 原文：<https://www.sitepoint.com/html5-form-validation/>

几个月前 [Sandeep 向我们介绍了 HTML Constraint API](https://www.sitepoint.com/using-the-html5-constraint-api-for-form-validation/) ，展示了我们如何使用新的 HTML5 输入类型和属性，用最少的 JavaScript 在浏览器中验证我们的表单。今天，我将带您通过使用 Constraint API 来验证一个简单的预订表单，并关注如何确保表单也是可访问的。

概括一下——或者如果你没有读过 Sandeep 的文章——随着 HTML5 的出现，大量新的输入类型和属性被添加到`<input>`标签中，允许浏览器自己为我们执行客户端验证:不需要 JavaScript。要开始使用新的输入类型和属性，除了开始使用新的输入类型和属性之外，您实际上不需要做任何事情。

严格地说，您应该确保您使用的是 HTML5 DOCTYPE，否则您会遇到 HTML 验证错误。但是他们的伟大之处在于他们都优雅地退化了。因此，如果一个旧的浏览器不支持它们，它们在 HTML 中的事实不会“破坏”任何东西，它们只是被呈现为一个`<input type=”text”>`。

**NB** 虽然客户端表单验证对于增强用户体验(快速、即时地向用户提供反馈，而无需往返服务器)非常有用，但您仍然需要验证服务器上提交的任何数据。

让我们通过一个例子来说明如何只使用浏览器的内置验证来验证表单。以这个简单的预订表格为例:

```
<form action="" method="post">
    <fieldset>
        <legend>Booking Details</legend>
        <div>
            <label for="name">Name (required):</label>
            <input type="text" id="name" name="name" value="" aria-describedby="name-format">
            <span id="name-format" class="help">Format: firstname lastname</span>
        </div>
        <div>
            <label for="email">Email (required):</label>
            <input type="text" id="email" name="email" value="">
        </div>
        <div>
            <label>Website:</label>
            <input type="text" id="website" name="website" value="">
        </div>
        <div>
            <label for="numTickets"><abbr title="Number">No.</abbr> of Tickets (required):</label>
            <input type="text" id="numTickets" name="numTickets" value="">
        </div>
        <div class="submit">
            <input type="submit" value="Submit">
        </div>
    </fieldset>
</form> 
```

[https://jsfiddle.net/ianoxley/aZ9r8/1/](https://jsfiddle.net/ianoxley/aZ9r8/1/)

注意，每个输入字段都有一个相关的`<label>`标签。label 标签的`for`属性与相关输入标签的 id 属性相匹配。这保持了 HTML 的语义，标签有助于赋予输入控件意义。这也意味着，如果您单击标签，关联的输入标记将获得焦点。

它们还有助于提高可访问性，因为标签中的文本将被屏幕阅读器用户读出:因此，通过在标签文本中添加‘required’来指示必填字段是很有用的，就像我上面所做的那样。

我还使用了 [WAI ARIA](https://www.w3.org/WAI/) `aria-describedby`属性，它有助于通知用户应该以何种格式输入他们的名字。

```
<label for="name">Name (required):</label>
<input type="text" id="name" name="name" value="" aria-describedby="name-format">
 <span id="name-format" class="help">Format: firstname lastname</span>
```

当它描述的输入字段获得焦点时，会显示这一点，提供上下文相关的帮助:

```
.help {
    display:none;
    font-size:90%;
}
input:focus + .help {
    display:inline-block;
}
```

要验证此表单，我们需要:

1.  确保已完成必填字段
2.  确保为“名称”输入的内容与名称相似
3.  确保电子邮件地址看起来像电子邮件地址
4.  检查是否提供了网站 URL，看起来像 URL
5.  确保在“门票数量”中输入了一个数字

## 必填字段

验证必填字段很简单，只需向每个必须填写的`<input>`标签添加`required`属性，即姓名、电子邮件和票据数量。例如，名称现在看起来像这样:

```
<div>
        <label for="name">Name:</label>
        <input id="name" name="name" value="" aria-describedby="name-format" required>
        <span id="name-format" class="help">Format: firstname lastname</span>
    </div>
```

如果我们现在尝试提交此表单而没有填写任何必填字段，浏览器会通知我们:

![Required attribute](img/cab9d3ff81aea1ffa29bb071dae43d8c.png)

您是否注意到我们的必填字段`<label>`标签中不再有“必填”字样？这是因为大多数屏幕阅读器现在会选择`required`属性，所以如果我们在标签标记中保留“required ”,屏幕阅读器用户会听到两次“required ”,他们会觉得很烦。

不过，有一个警告:不是所有的浏览器都能容易地实现`required`属性，所以在某些浏览器/屏幕阅读器组合中，它可能不会被正确地拾取。因此，当前的最佳实践建议用 aria-required="true "属性补充 required 属性:

```
<input id="name" name="name" value="" aria-describedby="name-format" required aria-required=”true”/>
```

## 验证数据

既然我们的用户被提示完成必填字段，我们需要确保他们提交的数据是我们要求的格式。

我们希望“Name”字段以“Firstname Lastname”的格式提交，并且只包含字母和一个空格( **NB** 在现实世界的场景中，您可能需要考虑其他地区——这个示例故意保持简单)。我们可以通过向“Name”字段添加一个模式属性来实现这一点，将它的值设置为我们希望数据与之进行比较的正则表达式:

```
<input id="name" name="name" value="" aria-describedby="name-format" required aria-required=”true” pattern="[A-Za-z-0-9]+\s[A-Za-z-'0-9]+">
```

当使用模式属性时，正则表达式的开始和结束的`^`和`$`是隐含的，不需要包含在内。

您可以通过包含一个标题属性告诉用户您需要的格式来帮助用户:

```
<input id="name" name="name" value="" aria-describedby="name-format" required aria-required=”true” pattern="[A-Za-z-0-9]+\s[A-Za-z-'0-9]+"  title="firstname lastname">
```

然后，title 属性中的文本被追加到内置验证消息中:

![Pattern attribute](img/0f40df541fd97564734e4f6987cd04aa.png)

请注意，一些屏幕阅读器/浏览器组合可能会导致除 aria-describedby 文本之外的 title 属性被读出，因此请注意这一点，例如，我发现在 IE10 中使用 NVDA 会导致 title 属性和 aria-describedby 元素的文本被读出，但在 Chrome 和 Firefox 中使用 NVDA 不会出现这种行为。只有咏叹调描述的文本被阅读。稍后我们将再次讨论这个问题，并向您展示一个使用 CSS3 的解决方案。

## 验证电子邮件、URL 和号码

为了确保我们的用户在电子邮件、网站和门票数量字段中输入正确的数据，我们可以使用 HTML5 中添加的一些新输入类型:

```
<input type="email" id="email" name="email" required>
…
<input type="url" id="url" name="url">
…
<input type="number" id="numTickets" name="numTickets" required>
```

通过指定适当的类型，我们的浏览器将为我们验证数据，并确保我们在电子邮件字段中有一个电子邮件地址，在网站字段中有一个 URL，在门票数量字段中有一个数字。

![type email](img/c14fa3c98965b6788a60ff6102ff0e7a.png)

![type url](img/8175cdc1db5d6b2a4074b90e1abd2213.png)

还要注意,`type`属性本身不再是必需的属性。如果你不指定它，`input`标签将隐含地属于`type="text"`。

假设我们想限制每个人可以购买的门票数量？我们可以通过使用`max`和`min`属性来做到这一点:

```
<input type="number" min="1" max="4" id="numTickets" name="numTickets">
```

如果用户输入小于 1 或大于 4 的数字，系统会提示他们输入允许范围内的数字。

![Max attribute](img/79f326387c618bc9faeb20d522410fdc.png)

## 使用 CSS 突出显示必填字段和无效数据

与 HTML5 提供的新的输入类型和属性相结合，CSS3 提供了一些新的伪类，我们可以使用这些伪类向用户提供可视线索，告诉他们哪些表单字段是必需的，哪些是可选的，哪些包含验证错误。

必填字段可以使用`:required`伪类:

```
input:required {
    background:hsl(180, 50%, 90%);
    border:1px solid #999;
}
```

可选字段可以使用`:optional`伪类:

```
input:optional {
    background:hsl(300, 50%, 90%);
    border:1px dotted hsl(180, 50%, 90%);
}
```

表单验证的成功或失败可以通过使用`:valid`、`:invalid`、`:in-range`和`:out-of-range`伪类向用户表示:

```
input:valid,
input:in-range {
    background:hsl(120, 50%, 90%);
    border-color:hsl(120, 50%, 50%);
}

input:invalid,
input:out-of-range {
    border-color:hsl(0, 50%, 50%);
    background:hsl(0, 50%, 90%);
}
```

![CSS3 pseudo-classes](img/27e40ebc9b916ace181adbe5733f301d.png)

还记得我之前提到的某些屏幕阅读器/浏览器组合会导致除了`aria-describedby`元素的文本之外还大声朗读 title 属性吗？解决这个问题的一种方法是从 input 元素中移除 title 属性，并使用 CSS3 `:invalid`伪类来显示`aria-describedby`文本:

```
input:focus + .help,
input:invalid + .help {
    display:inline-block;
}
```

现在，除了在输入字段获得焦点时显示帮助文本之外，我们还将在输入字段的值无效时显示帮助文本。

在做了所有这些改变后，我们的 HTML 现在看起来像这样:

```
<form action="" method="post">
    <fieldset>
        <legend>Booking Details</legend>
        <div>
            <label for="name">Name:</label>
            <input id="name" name="name" value="" required  pattern="[A-Za-z-0-9]+\s[A-Za-z-'0-9]+" aria-required="true" aria-describedby="name-format">
            <span id="name-format" class="help">Format: firstname lastname</span>
        </div>
        <div>
            <label for="email">Email:</label>
            <input type="email" id="email" name="email" value="" required aria-required="true">
        </div>
        <div>
            <label for="website">Website:</label>
            <input type="url" id="website" name="website" value="">
        </div>
        <div>
            <label for="numTickets"><abbr title="Number">No.</abbr> of Tickets:</label>
            <input type="number" id="numTickets" name="numTickets" value="" required aria-required="true" min="1" max="4">
        </div>
        <div class="submit">
            <input type="submit" value="Submit">
        </div>
    </fieldset>
</form>
```

[https://jsfiddle . net/iano oxley/vy 8ku/](https://jsfiddle.net/ianoxley/VY8KU/)

## 禁用基于浏览器的验证

您可以通过向您的`<form>`标签添加一个`novalidate`属性来禁用内置浏览器验证:

```
<form novalidate>
    …
</form>
```

[https://jsfiddle.net/ianoxley/9C2JD/](https://jsfiddle.net/ianoxley/9C2JD/)

## 跨浏览器？

好消息是所有最新的桌面浏览器和大多数移动浏览器都支持 [HTML 表单验证](http://caniuse.com/form-validation)。坏消息是，它在 Safari 中仅得到部分支持，在 iOS Safari 或默认的 Android 浏览器中完全不受支持。如果你需要支持 IE10 之前的 IE 老版本，你也找不到任何支持表单验证的版本。

那么，如果您必须支持尚不支持表单验证的浏览器，您能做些什么呢？

一种选择是什么都不做，只依赖服务器端验证。这不需要您做额外的工作，但是对于那些使用不支持的浏览器的人来说，UX 会令人满意吗？

另一种选择是继续单独使用 JavaScript 进行客户端验证，而不添加上面讨论的任何新特性。

第三种方法是使用 JavaScript 来检测浏览器是否支持表单验证，如果支持就使用它，如果不支持就退回到基于 JavaScript 的验证。像 [Modernizr](http://modernizr.com/) 这样的库可以帮助 HTML5 特性检测，但是如果你不想包含另一个 JavaScript 库，你也可以编写自己的代码:

```
// Without Moderizr
var inputElem = document.createElement('input');
if (!('required' in inputElem)) {
    // JavaScript form validation
  }

...

// With Modernizr
if (!Modernizr.input.required) {
    // JavaScript form validation
}
```

## 结论

在本文中，我们在不使用任何 JavaScript 的情况下将 HTML5 表单验证应用于预订表单客户端，并指出了一些需要警惕的可访问性问题。

然后，我们看了如何使用新的 CSS3 伪类向用户提供可视提示，告诉用户哪些字段是必需的和可选的，哪些包含有效或无效的数据。

最后，我们讲述了如何禁用 HTML 表单验证，并检测表单验证是否可用，以防我们必须支持尚未支持该功能的浏览器。

请在评论中告诉我你对这一切的看法。

*单周末自学 HTML5。SitePoint 的最新 HTML5 指南， [Jump Start HTML5](https://learnable.com/books/jshtml1?utm_source=sitepoint&utm_medium=referral&utm_content=html5-form-validation&utm_campaign=top20articles) ，免费阅读[，并提供可学试用](https://learnable.com/join/trial?utm_source=sitepoint&utm_medium=referral&utm_content=html5-form-validation&utm_campaign=top20articles)。*

## 分享这篇文章