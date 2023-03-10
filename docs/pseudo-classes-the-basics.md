# 伪类——基础

> 原文：<https://www.sitepoint.com/pseudo-classes-the-basics/>

以下内容改编自亚历克西斯·戈尔茨坦、路易斯·拉扎勒斯和埃斯特尔·维尔创作的真实世界的 HTML5 & CSS3。

## 伪类

很可能你已经熟悉了一些用户交互伪类，即`:link`、`:visited`、`:hover`、`:active`和`:focus`。

### 重要:需要注意的要点

`:visited`伪类会带来安全问题，所以浏览器并不支持被访问链接的所有 CSS 属性。如果没有这些限制，恶意站点可以对访问过的链接应用一种样式，例如为每个访问过的链接应用一个独特的背景图像，以检查用户是否访问过受欢迎的站点或银行。这使得攻击者能够在未经用户允许的情况下窥探用户的浏览历史。因此，现代浏览器限制了可以用`:visited`应用的样式。

该规范明确宽容这些变化，称:因此，UAs[用户代理]可能会将所有链接视为未访问的链接，或实施其他措施来保护用户的隐私，同时以不同方式呈现已访问和未访问的链接。

为了更好的可访问性，在包含`:hover`的地方加上`:focus`，因为不是所有的访问者都会使用鼠标来浏览你的网站。

可以应用于页面上的任何元素，不仅仅是链接和表单控件。

`:focus`和`:active`与链接、表单控件、内容可编辑元素以及任何具有 tabindex 属性的元素相关。

虽然您可能已经使用这些基本的伪类有一段时间了，但是还有许多其他可用的伪类。这些伪类中有几个已经在规范中存在了很多年，但是直到浏览器开始支持新的 HTML5 表单属性使它们更加相关时才得到支持(或者说不为人所知)。

以下伪类根据属性、用户交互和表单控件状态匹配元素:

`:enabled`
一个被启用的用户界面元素，它基本上是任何支持`disabled`属性的表单控件，但目前还没有应用它。

`:disabled`
相反，一个用户界面元素是`disabled:`任何支持禁用属性且当前已应用该属性的表单控件。

`:checked`
为选中或打勾的单选按钮或复选框。

`:indeterminate`
为既不勾选也不取消勾选的表单元素。例如，如果您勾选了“全部选中”复选框以选择一组复选框，然后取消选择该组中的部分而非全部复选框，则可以将“全部选中”设置为不确定状态(使用 JavaScript ),以指示它既未选中也未取消选中。

`:target`
该选择器挑选出作为当前活动页面内锚点目标的元素。这听起来比实际更复杂:您已经知道，通过使用#字符和目标的 ID，可以在页面中链接到锚点。例如，您可以在页面中使用[跳转到内容](#content)链接，当单击该链接时，会跳转到带有内容 ID 的元素。

这将地址栏中的 URL 改为`thispage.html#contentand the` `:target`选择器现在匹配文档中以内容作为其 ID 的元素。就好像你已经临时包含了选择器#内容。我们说暂时是因为一旦用户点击了不同的锚，`:target`就会匹配新的目标。

`:default`
适用于一组相似元素中默认的一个或多个 UI 元素。例如，在页面加载时选中的一组同名单选按钮中的一个单选按钮在取消选中后将继续匹配`:default` `after another radio button in the same-named group is selected. Similarly, checkboxes that are selected on page load will continue to match` `:default`。

`:valid`
适用于基于类型、模式或其他输入属性的有效元素(正如我们在第 4 章中讨论的)。

`:invalid`
适用于空的必需元素和与类型或模式属性定义的需求不匹配的元素。

`:in-range`
适用于具有范围限制的元素，其中值在这些限制内。例如，这适用于具有最小值和最大值属性的日期/时间、数字和范围输入类型。值为空时为`:in-range`。

`:out-of-range`
与`:in-range:`相反的元素，其值在其范围的限制之外。缺失值没有超出范围，因为它们是空的。

`:required`
适用于设置了`:required`属性的表单控件。

`:optional`
适用于所有没有`:required`属性的表单控件。

`:read-only`
适用于内容不能被用户更改的元素。这是除了那些具有`contenteditable`属性集和表单字段的元素之外的大多数元素。

`:read-write`
适用于内容可由用户修改的元素，如`contenteditable` 组件和可写输入字段。

在表单控件中支持这些属性的浏览器中，浏览器对这些属性的支持是完整的；换句话说，支持 required 和 pattern 的浏览器也支持关联的`:valid`和`:invalid`伪类。

IE8 和更早版本缺乏对:checked、:enabled、:disabled 和:target 的支持。好消息是 IE9 确实支持这些选择器，但不支持用户界面选择器。IE10 和 IE11 支持`:indeterminate`、`:required`、`:optional`，但不支持`:default`、`:in-range`、`:out-of-range`、`:read-only`或`:read-write`。

虽然仍然缺乏支持，但 JavaScript 库如[selectivitizr](http://selectivizr.com/)可以帮助在 Internet Explorer 中定位这些伪类。

## 分享这篇文章