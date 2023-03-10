# 密码和注册表格的 UX 设计

> 原文：<https://www.sitepoint.com/ux-design-passwords-registration-forms/>

[![Mike Licht: Cell Phones & Blood Pressure, after Adriaen Brouwer Mike Licht, NotionsCapital.com](img/4817c78793082a84910cf0307b19d47e.png)](https://www.flickr.com/photos/notionscapital/8749833424/)

图片:迈克·李希特

表单是 2015 年网页设计的重要组成部分——尤其是因为移动设备的兴起迫使我们更加努力地改善小屏幕用户体验。

他们是推动电子商务网站转化的核心，良好的设计往往是成功注册用户和永久失去他们的区别。

从用户的角度来看，表单需要清楚地告诉用户需要什么。他们应该毫不费力地点击和输入，不管他们在什么设备上访问。它们应该显示正确对齐的标签，指示用户需要在给定字段中输入什么。

虽然要求尽可能多的信息曾经被认为是一种标准做法——因为你以后可能会需要这些信息——但今天并非如此。表单的字符长度不应超过绝对必要的长度。

[![Short forms](img/f53592a5da5d0870b593b3e1503f4521.png)](https://willscullypower.files.wordpress.com/2011/06/)

图片来源:[威尔·史卡利-鲍尔](https://willscullypower.files.wordpress.com/2011/06/short-forms.png)

研究表明，更短的表单可以[增加高达 160%的转化率](http://blog.market8.net/b2b-web-design-and-inbound-marketing-blog/all-you-need-to-know-about-forms-to-maximize-conversions)，所以一个设计糟糕的表单，或者一个需要太多数据的表单会直接影响收入。

## 用户不喜欢注册

用户越来越抵制传统的注册过程，研究表明，如果他们被迫创建一个用户帐户，高达 86%的人会改变他们的行为。

Blue Research 进行的一项研究进一步发现，在询问用户时:

*   54%的人表示他们*可能*离开该网站
*   26%的人说他们会选择另一个不需要注册的网站
*   6%的人说他们会离开并在未来避开这个网站
*   14%的人表示他们将完成注册

此外，88%的人说他们在网站注册时在表格上提供了不正确的信息，90%的人说他们没有从以前注册的网站上检索帐户信息，而是直接离开了。

来自用户的一致信息是清楚的:他们宁愿不注册，并且尽可能避免注册。更何况这种抵触情绪也越来越大。如果可以选择创建账户，许多用户会更乐意为他们所有的账户使用社交登录。

因此，我们知道表格需要简短，虽然从业务角度来看拥有大量客户信息有时很方便，但如果它严重影响收入，它就没有价值。

## 满意解决（法）

杰西卡·恩德斯在她的文章 *[《满足:这对网络表单](https://www.sitepoint.com/satisficing-mean-web-forms/)* 意味着什么》中解释道，

> 术语“满足感”指的是人类倾向于只花费成功完成一项任务所需的最少能量。这是一种潜意识现象:(大部分时间)我们不会坐下来做某件事，并想“*我要在这个*上花最少的钱”。

因此，在制作表单时，有必要在设计时考虑如何让用户满意，以避免用户出错。Jessica 建议，在以最清晰的方式向用户提出问题时，这一点尤为重要。

要克服这一点，您应该:

> 将参考框架放在问题的开头，而不是结尾

这是因为用户一旦下意识地觉得自己有了答案，就会停止阅读问题。

Jessica 说与其问下面的问题，

> 问:在过去的两年里，你在其他地方住过吗？(是/否)

你应该把它框成这样:

> 在过去的 2 年里，你在其他地方住过吗？(是/否)

这“告诉用户你对他们体验中的哪一部分感兴趣。”因此，你应该把重要的信息“预先载入”表单问题，以确保用户能够理解。

查看[全文，了解关于满足感](https://www.sitepoint.com/satisficing-mean-web-forms/)以及如何在表单设计中克服它的更详细的解释。

## 重复字段

在应用程序的不同位置重复电子邮件地址和密码的网站并不少见。这不仅会鼓励用户离开，还会让那些使用密码管理器存储密码的用户感到头疼。

这是因为密码管理器通常通过挂钩提交表单事件并存储字段名和值来工作。

如果在设计表单时为这些密码字段指定了不同的名称，那么密码管理器将无法填充表单，因为字段名不匹配。

这是一个常见的问题，为了克服它，哺乳动物建议您确保密码字段(如果您真的必须包括两个)的名称相同。对于注册时不要求用户密码，但要求用户名的网站，哺乳动物建议当用户进入网站设置密码时，使用不可见的用户名字段，如下所示。

```
<!-- Set password -->

<form action="/signup" method="post">
<input name="username" style="display:none;" type="text" value="{{ username }}"/> <input name="password" placeholder="Password" type="password"/> <input type="submit"/>
</form>
```

这意味着当新用户提交表单时，密码管理员将能够看到用户名和密码字段，因此能够存储这两个字段。

当谈到重复的电子邮件字段时，大多数网站现在都知道这些不仅经常被误用(例如，复制和粘贴已经输入到第一个字段中的内容)，而且它们还会不利地影响转换。

有些人，比如格里·加夫尼，走得更远。格里说:

> 我觉得重复电子邮件地址的要求是傲慢和不必要的。如果我真的很想要某样东西，但我错误地输入了我的电子邮件地址，我可能会注意到我没有收到我请求的服务。我实际上并不需要重复的电子邮件地址所暗示的那种牵手。

我当然认为用户不再需要那种方向。我们现在都习惯于使用 web 表单，所以不用它了。相反，应该考虑让用户更容易看到他们正在键入的内容的方法。

*   包括大量空白，以便字体清晰
*   在白色背景上使用黑色字体
*   增加输入字段的大小

您还可以考虑在显示当前电子邮件的电子邮件字段下添加一条消息

其中显示了已经输入的电子邮件地址，以及一个对话框，说明他们应该检查该地址，因为这是注册详细信息将发送到的地方。

![email duplication](img/ff76e86e800239a6dde8c2edd81f9e70.png)

图片来源:[陆克构思与设计](http://www.lukew.com/ff/entry.asp?941)

虽然图片用红色显示了电子邮件地址，但是应该小心处理，因为一些用户会认为他们犯了错误，这可能会导致沮丧。记住这一点，考虑[对这个特殊的元素进行 A/B 测试](https://www.sitepoint.com/designers-guide-a-b-testing/)，用不同的颜色呈现它。

## 表单标签

[表单标签](https://www.sitepoint.com/definitive-guide-form-label-positioning/)及其定位是注册表单上最常见的错误之一，自从移动革命开始以来，这种情况变得更加严重。

在移动设备上查看时，经常会看到表单标签与表单域有点不同步，尽管它在桌面上看起来完全没问题(通常是左对齐的标签，因此应该避免)。

标签有几种不同的定位方式，最常见的选项包括字段上方或旁边。在字段上方放置标签，无论是在内部还是外部，都会使表单比在字段旁边放置标签时长一倍。一个看起来更长的表单可能会引发满足感。

早在 4 月份，UX 运动的 Anthony 发布了“[内场顶部对齐的表单标签扫描速度最快](http://uxmovement.com/forms/why-infield-top-aligned-form-labels-are-quickest-to-scan/)”尽管这仍然肯定是有争议的，因为 [Jessica 在她 5 月的后续帖子](http://www.formulate.com.au/blog/infield-top-aligned-labels)中解释了这一点。

同样，对于不同标签位置的利弊的全面概述，请参见我们的[权威指南](https://www.sitepoint.com/definitive-guide-form-label-positioning/)。

## 更多资源

因此，形式是设计中极其重要的一部分，因此它们应该得到应有的重视。它们应该尽可能简单易用，尽可能简短。社交注册是可取的，但不应该以降低网站速度和降低 UX 为代价来优先考虑。

有益阅读:

*   杰西卡·恩德斯:[无痛账户的 3 条规则 UX 第一部分](https://www.sitepoint.com/3-rules-painless-account-ux/)和[第二部分](https://www.sitepoint.com/3-rules-painless-account-ux-login-screens/)
*   杰西卡·恩德斯:[形成标签定位的权威指南](https://www.sitepoint.com/definitive-guide-form-label-positioning/)
*   UX 运动:[如果你标记必填字段，为什么用户填写的少](http://uxmovement.com/forms/why-users-fill-out-less-if-you-mark-required-fields/)
*   Acquire Convert: [转换优化:表单深度指南](http://acquireconvert.com/conversion-optimization/)

## 分享这篇文章