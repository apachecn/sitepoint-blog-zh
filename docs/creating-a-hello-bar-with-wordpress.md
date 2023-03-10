# 用 WordPress 创建一个 Hello 栏

> 原文：<https://www.sitepoint.com/creating-a-hello-bar-with-wordpress/>

电子邮件列表非常强大。在一个充斥着从脸书到 Snapchat 等各种社交网络的世界里，电子邮件仍然是最强大的营销渠道之一。

像 [Kissmetrics、](https://blog.kissmetrics.com/beginners-guide-email-marketing/) [Buffer、](https://blog.bufferapp.com/email-list-building) [Michael Hyatt、](http://michaelhyatt.com/formula.html)和 [Jeff Goins](http://goinswriter.com/email-list/) 这样的巨头都同意，建立和营销电子邮件列表是你能为你的品牌做出的最好的举动之一。但是实际上做起来容易做起来难。如果你尝试过从你的网站上收集电子邮件，你就会知道这个过程有多慢。

你可能听说过 [Hello Bar。它在你的网站顶部创建了一个横跨屏幕长度的细栏。读者可以方便地在选择框中输入他们的电子邮件。](https://www.hellobar.com/)

![Hello Bar](img/1c99dccc78353a85fefebf03bf7f3c8c.png)

这听起来很简单，事实也确实如此，但这正是它奏效的原因。如果你正确使用 Hello 栏，你会收到数量惊人的邮件。这些数字是疯狂的，DIYThemes 的德里克·哈尔彭在一个月内收到了 1180 封电子邮件 ( [他在社交触发器上发布了更多关于它的信息](http://socialtriggers.com/why-hello-bar-rocks-for-lead-generation/))。

唯一的缺点是对免费用户的点击限制。如果你使用免费的 Hello 栏，你的栏将被限制在 100 次点击。这意味着如果不升级，你将无法收集 101 电子邮件。还有一个水印没有专业计划是不会消失的。虽然他们的付费计划价格合理，但你不需要升级，因为你可以免费建立自己的 Hello Bar。

通过一些基本的 WordPress 操作，你可以创建你自己的 Hello Bar 克隆。我前面链接的那些巨头不一定爱 Hello Bar，他们爱的是 Hello Bar 这个概念。你可以完全免费地构建同样的概念。你也可以将它与你的电子邮件营销服务相结合。我将向您展示如何将其与 MailChimp 集成。

如果你是 WordPress 的新手，不用担心--你所要做的就是安装一些插件并改变一些设置。就是这么简单，结果就是一个 Hello Bar 风格的漂亮的、吸引人的顶栏，您可以随心所欲地修改它。

对于那些喜欢更多功能的编码方式的人，我们也将从头开始讲述如何编码一个 bar。但即使这样也不复杂。

所以如果你准备好了，让我们开始吧。

## 在 WordPress 中创建顶栏

### 1)准备好工具。

要做这个项目，你需要以下:

*   WordPress 帐户
*   MailChimp 帐户
*   用于 WordPress 插件的 MailChimp】
*   [MailChimp 顶部工具栏插件](https://wordpress.org/plugins/mailchimp-top-bar/)

### 2)连接 WordPress 和 MailChimp。

转到你的仪表板，打开 MailChimp for WordPress 插件。您应该会看到标题为 MailChimp API 设置的部分。在这个区域，API 密匙框下面有一个小链接，上面写着“在此获取 API 密匙”点击该链接并登录您的 MailChimp 帐户。

现在单击“创建密钥”您的 API 密钥现在应该显示在页面上。突出显示它并将其复制到您的剪贴板。

回到 WordPress 插件的 MailChimp。在 API 密钥框中输入您的密钥，并保存您的更改。您的 WordPress 和 MailChimp 现已连接。

### 3)修改你的顶栏。

现在我们来看看好东西。导航到顶栏插件，它出现在左边栏的“MailChimp for WP”标题下。这个插件最好的元素之一是它的简单性。你不必搞乱任何形式；这是相当字面上的即插即用。

一旦你点击顶部的工具栏插件，你会看到工具栏的设置。在这里，你可以修改你的酒吧会说什么。您可以更改栏文本、按钮文本和电子邮件占位符文本。

您还可以选择希望提交的电子邮件进入哪个列表。如果你需要一个专门的电子邮件列表专门用于你的网站注册，或者如果你想把你的列表分段，这就很方便了。

现在转到外观选项卡。虽然这个菜单没有太多的选项，但它确实很强大。您可以将工具栏放置在页面的顶部或底部。对于真正的 Hello Bar 风格，你应该把它放在顶部。

你也可以改变酒吧的大小。最大的尺寸是最接近 Hello 栏的尺寸，并且它也提供了最大的可见性。令人惊讶的是，它并不显眼。最后，你可以修改颜色，并决定你是否想要一个粘性栏。

在“消息”菜单上，您可以更改新用户将收到的不同消息的措辞，例如成功消息。您还可以在订阅时将订阅者重定向到页面。如果你想把你的用户引向一个登陆页面或者一个你作为注册奖励的免费服务，这是非常有用的。

要进行最后的一些调整，请进入高级菜单。选择您是否希望您的订户双重选择加入([，强烈推荐](https://www.elegantthemes.com/blog/resources/the-importance-and-benefit-of-double-opt-in-email-marketing))。第二个选项使您能够在有人注册时更新现有的订户。出于显而易见的原因，把这个留在“不”

保存你所有的更改，并前往你的主页！

### 4)测试所有内容，并进行最终更改。

注册你自己的列表，并确保一切正常运行。你是否被重定向到正确的页面？你的邮箱会出现在你的 MailChimp 账户里吗？如果是这样，你就可以走了！你的 Hello Bar 风格的 Bar 现在已经准备好建立你的邮件列表了。

## 用代码创建顶栏

从头开始创建一个 Hello Bar 风格的顶栏最明显的优点是灵活性。您可以添加 CSS 和 JavaScript 来修改栏，并添加额外的元素，如动画。这也是一个简单的过程，所以即使你害怕代码，你也可以这样做。

### 1)设置您的 MailChimp 表单。

这里需要做一点额外的工作。代替顶端栏插件处理表单创建，你必须自己做。

在你的 WordPress 仪表盘中，将鼠标悬停在 MailChimp for WP 标题上，从出现的菜单中选择“表单”。为您的表单添加标题，选择相应的列表，然后单击“添加新表单”。

在下一个屏幕上，您将能够编辑您的表单。你可以直接编辑代码，还有消息、设置和外观的菜单。这类似于顶栏插件。

对于这个例子，我们将使用一个简单的表单:

```
<input type="email" name="EMAIL" placeholder="Your email address" required />

<input type="submit" value="Sign up" /> 
```

一旦你按照你想要的方式设置了菜单，点击“获取简码”就在你表格的名字下面。短代码将会弹出；将其复制到您的剪贴板。

### 2)编辑 HTML。

将鼠标悬停在边栏中的外观标题上，然后选择编辑器。从这里，你会想去你的主题 header.php 文件。

您需要确定将表单放在代码中的什么位置。我把我的放在了结束标签之前。注意:我用 216 主题做实验，但是任何主题都可以。

以下是我使用的代码:

```
<div class="page-header">



< ?php echo do_shortcode('[mc4wp_form id="53"]'); ?>



</div> 
```

这增加了页眉，因此表单有足够的空间。然后通过 PHP 函数`do_shortcode()`插入表单。最后，为了美观，我将表单居中。

MailChimp 短码对每个用户来说都是唯一的，所以它不会总是显示`mc4wp_form id=“53”`，因为号码 ID 是不同的。只需将您独特的短代码粘贴到这些括号内，您就可以开始工作了。

### 3)编辑 CSS。

接下来，在`style.css`文件中，我简单地修改了页面标题，如下所示:

```
.page-header {
    height: 100px;
} 
```

仅此而已。结果是你的网站有一个功能性但粗糙的顶栏。它并不完整，但它是供您试验的基本框架。您几乎可以用 CSS 和 JavaScript 对其进行无休止的定制，如果您想这样做，请查看 Daymuse 的这篇文章。

## 结论

无论你选择使用基本的 WordPress 版本还是更加可定制的代码版本，你都可以在几分钟内拥有一个闪亮的新的顶栏。现在你可以免费享受 Hello Bar 的所有好处。

如果你正在寻找一个完全免费的、随时可用的动态栏，请查看我们自己的 [Random Hello Bar 插件](https://www.sitepoint.com/sitepoint-random-hello-bar-wordpress-plugin/)(你现在应该可以看到它的运行了)。

不管你选择走哪条路，毫无疑问，顶栏是你的网站或博客最好的营销工具之一。今天就买一个，你会在不知不觉中看到惊人的效果。

## 分享这篇文章