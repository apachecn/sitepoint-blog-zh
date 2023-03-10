# 跨浏览器 XForms

> 原文：<https://www.sitepoint.com/cross-browser-xforms/>

XForms 是 HTML 表单的复杂而强大的替代品，它严重依赖于尖端的 XML 技术。由于它的复杂性，浏览器实现它的速度通常很慢。但是随着 ie 浏览器的工作插件和火狐浏览器的[预发布扩展](http://www.mozilla.org/projects/xforms/)的推出，是时候给这项技术另一个视角了。

上次我提到 XForms 时，这种技术——至少在主流浏览器中——似乎永远不会出现。它需要一个插件才能在 Internet Explorer 中运行，Mozilla 找不到愿意为它工作的人，苹果和 Opera 都因为各种原因认为它不实用。

然后，在去年 8 月，Novell 和 IBM [宣布](http://www.mozilla.org/press/mozilla-2004-08-10.html)他们将开发支持 Mozilla 浏览器的 XForms。现在，这些努力终于开始得到回报。那么这是否意味着我们可以开始编写跨浏览器工作的 XForms 了？

为了找到答案，我为 Internet Explorer 6 安装了最新版本的 [FormsPlayer](http://www.formsplayer.com/) ，为 Firefox 1.5 Beta 2 安装了 Mozilla 的 XForms 扩展[预览版](http://www.mozilla.org/projects/xforms/#HowDownload)，然后开始编写 XForms。

XForms 可以适合 XHTML 文档，但是 Firefox 扩展要求文档使用真正的 XHTML MIME 类型(`application/xhtml+xml`)。很容易使用 PHP(或另一种服务器端语言)来查看浏览器是否支持它，并发送适当的 HTTP 头:

```
if (stristr($_SERVER['HTTP_ACCEPT'], "application/xhtml+xml")) {
  header("Content-type: application/xhtml+xml");
} 
```

要在 XHTML 文档中识别 XForms 标签，需要为标签声明一个名称空间前缀。在这个过程中，我们还将为 [XML 事件](https://www.w3.org/TR/xml-events/)声明一个前缀，因为 XForms 使用这个标准来处理用户界面事件:

```
<html 
    xmlns:xforms="https://www.w3.org/2002/xforms"
    xmlns:ev="https://www.w3.org/2001/xml-events">
```

由于 Internet Explorer 不完全支持 XHTML，所以在使用任何 XForms 标记之前需要加载 FormsPlayer 插件，并告诉它 XForms 将使用哪个名称空间前缀。为此，在文档的`<head>`中需要一个`<object>`标记和一个 XML 处理指令:

```
<object id="FormsPlayer" width="0" height="0"
    classid="CLSID:4D0ABA11-C5F0-4478-991A-375C4B648F58">
</object>
<?php echo '<?import namespace="xforms" implementation="#FormsPlayer"?>'; ?>
```

我已经用 PHP 输出了处理指令，以防止它在 PHP 的[短标签特性](http://au3.php.net/manual/en/ini.core.php#ini.short-open-tag)被启用时导致 PHP 错误。

现在我们可以开始写一些 XForms 了。该技术的基本前提是定义一个或多个模型——XML 文档框架——然后定义使用一组用户界面元素用数据填充它们的表单。

下面是一个简单登录表单的 XForms 模型:

```
<xforms:model id="login">
  <xforms:instance>
    <login xmlns="">
      <username />
      <password />
    </login>
  </xforms:instance>
  <xforms:bind nodeset="/login/username" required="true()" />
  <xforms:submission action="submit.php" method="post" id="loginsubmit"
      includenamespaceprefixes="#default" />
</xforms:model>
```

`<xforms:instance>`标签包含要填充的 XML 文档框架。`<xforms:bind>`标签表示`username`标签需要一个值，表单才能被提交。`<xforms:submission>`标签说明了完成的文档应该提交到哪里以及如何提交(在本例中，是对`submit.php`的 POST 请求)。

我们还可以创建第二个模型——相当于第二个旧式 HTML 表单——允许新访问者注册帐户:

```
<xforms:model id="newuser" schema="newuser.xsd">
  <xforms:instance>
    <newuser xmlns="">
      <username />
      <email />
    </newuser>
  </xforms:instance>
  <xforms:bind nodeset="/newuser/username" required="true()" />
  <xforms:bind nodeset="/newuser/email" required="true()" />
  <xforms:submission action="submit.php" method="post" id="newusersubmit"
      includenamespaceprefixes="#default" />
</xforms:model>
```

这里的主要区别是`<xforms:model>`标签上的`schema`属性。虽然我们可以使用`<xforms:bind>`标签来生成提交所需的特定值，但是这个属性让我们可以控制值何时被认为是有效的。指定的文件(本例中为`newuser.xsd`)使用 [XML 模式](https://www.w3.org/XML/Schema)标准来描述必须组成提交文档的数据值。

这就是 XForms 失去浏览器制造商大量支持的地方。XML Schema 是一个复杂的标准，既难学又难实现，因为它让您对 XML 文档中可接受的值的种类拥有难以置信的控制权。例如，我们可以使用正则表达式来要求有效的电子邮件地址:

```
<xsd:element name="email" type="emailtype" />
<xsd:simpleType name="emailtype">
  <xsd:restriction base="xsd:string">
    <xsd:pattern value="[-._a-zA-Z0-9]+@([a-zA-Z0-9]+.)+[a-zA-Z]" />
  </xsd:restriction>
</xsd:simpleType>
```

不幸的是，由于实现 XML Schema 所涉及的工作量很大，目前的 Mozilla XForms 支持不支持这样的约束。然而，就像基于 JavaScript 的表单验证逻辑一样，XForms 在提交后必须在服务器端进行验证，因此这不仅仅是一个麻烦，更是一个障碍。

回到我们的 XHTML 文档，我们现在可以将一些表单组件放在一起，使用户能够填写和提交我们的两个模型。首先，我们想让访问者选择是否登录或注册一个新帐户:

```
<xforms:group>
  <xforms:trigger>
    <xforms:label>Log In</xforms:label>
    <xforms:toggle case="logincase" ev:event="DOMActivate" />
  </xforms:trigger>
  <xforms:trigger>
    <xforms:label>Register</xforms:label>
    <xforms:toggle case="newusercase" ev:event="DOMActivate" />
  </xforms:trigger>
</xforms:group>
```

这里的每个`<xforms:trigger>`标签将创建(至少在桌面 Web 浏览器中)一个带有指定标签(`<xforms:label>`)的按钮。当这些按钮被点击时(其中的
产生一个`DOMActivate`事件)，标签`<xforms:toggle>`将显示一个`case`——许多可用子表单中的一个。其中一个允许用户登录，另一个允许新用户注册。

```
<xforms:switch>
  <xforms:case id="logincase">
    ...login form...
  </xforms:case>
  <xforms:case id="newusercase">
    ...registration form...
  </xforms:case>
</xforms:switch>
```

这是登录表单:

```
<xforms:input ref="/login/username" model="login">
  <xforms:label>Username</xforms:label>
  <xforms:hint>6-32 characters (letters, numbers, or underscores)</xforms:hint>
</xforms:input>
<xforms:secret ref="/login/password" model="login">
  <xforms:label>Password</xforms:label>
  <xforms:hint>The password you enter will not be displayed</xforms:hint>
</xforms:secret>            
<xforms:submit submission="loginsubmit">
  <xforms:label>Log In</xforms:label>
</xforms:submit>
```

它为用户名字段使用了一个`<xforms:input>`标签，为密码字段使用了一个`<xforms:secret>`标签，为提交按钮使用了一个`<xforms:submit>`标签。每个字段都有`ref`和`model`属性，它们指向我们希望这个字段设置的模型中的特定元素。提交按钮指示触发哪个`<xforms:submission>`标签——提交哪个模型以及如何提交。

那么，我们的两种支持 XForms 的浏览器如何应对这一切呢？

![XForms default rendering in both browsers](img/aef3e1762a25ee0d8ce0833073a413a5.png)

关于我们对无样式 HTML 表单的期望，真的。标签、字段和按钮采用默认尺寸和外观进行内联布局。就像 HTML 表单一样，XForms 应该使用 CSS 样式。不幸的是，这是跨浏览器的美好开始分崩离析。

XForms 标准的要点是表单用户界面的外观是由显示表单的浏览器或设备决定的。该规范没有说明 CSS 应该如何应用于 XForms 字段、按钮等等。因此，不同的 XForms 实现需要不同的 CSS。

假设当表单域包含无效值时，您希望将表单域的标签加粗为红色。以下是 FormsPlayer 的操作方法:

```
xforms:input.invalid xforms:label,
xforms:secret.invalid xforms:label {
    color: red;
    font-weight: bold;
}
```

很简单，尽管它不符合在 CSS 选择器中表示 XML 名称空间的标准。但是 Firefox 需要，所以它需要以下代码:

```
@namespace xf url("https://www.w3.org/2002/xforms");
xf|input[invalid] xf|label,
xf|secret[invalid] xf|label {
    color: red;
    font-weight: bold;
}
```

还要注意，Firefox XForms 扩展目前使用属性来标识无效字段等状态。在扩展的最终版本中，它将使用伪类:

```
@namespace xf url("https://www.w3.org/2002/xforms");
xf|input:invalid xf|label,
xf|secret:invalid xf|label {
    color: red;
    font-weight: bold;
}
```

而这只是冰山一角。在这个领域开发出更好的标准之前，基本上每个想要支持的 XForms 实现都需要一个单独的样式表。对于这种情况，我们可以使用 Internet Explorer 的[条件注释](https://www.sitepoint.com/microsoft-says-de-hack-your-css/)功能，仅在该浏览器中应用 FormsPlayer 样式表:

```
<link rel="stylesheet" type="text/css" href="xforms-mozilla.css" />
<!--[if IE]>
<link rel="stylesheet" type="text/css" href="xforms-formsplayer.css" />
<![endif]-->
```

经过一点试验，我能够设置样式表，使表单在两种实现中都看起来相当不错。

![XForms styled rendering in both browsers](img/9f9be6b68b7773d250021295afab89f8.png)

正如您所看到的，当 Mozilla XForms 实现公开可用时，可以让 Internet Explorer 用户安装插件的开发人员可以很快找到这项技术的实际应用。

然而，浏览器支持仍然是一个棘手的问题，苹果和 Opera 正在努力在他们的下一个主要浏览器版本中支持不太雄心勃勃的 Web Forms 2.0，而 Internet Explorer 中的原生支持则遥遥无期。FormsPlayer 启动时会显示一个非常难看的广告作为浏览器工具栏，除非你用工具>管理附加组件……手动禁用它，所以我不想让我的用户安装它。

尽管如此，XForms 仍然是一项引人注目的技术——我们在这里看到的例子只是触及了 XForms 能力的皮毛。如果您曾经必须用三种不同的语言编写一个五页的表单，其中包含完整的客户端验证和应该在响应其他选择时出现和消失的字段，那么 XForms 正是您所需要的。它可以轻而易举地完成这样的任务。

好奇？为自己找一个这里讨论过的 XForms 实现，[亲自尝试一下例子](https://www.sitepoint.com/examples/xforms/)。[下载代码](https://www.sitepoint.com/examples/xforms/xforms-code.zip)并进行修补，然后前往 [XForms 网站](https://www.w3.org/MarkUp/Forms/)并阅读一两篇教程。

## 分享这篇文章