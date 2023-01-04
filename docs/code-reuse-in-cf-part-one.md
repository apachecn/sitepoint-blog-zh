# CF 中的代码重用——第一部分

> 原文：<https://www.sitepoint.com/code-reuse-in-cf-part-one/>

*…发帖人* [大卫·梅德洛克](https://www.sitepoint.com/forums/member.php?u=24385) *:*

嗯，大约 7 个小时后，我将乘飞机去犹他州的盐湖城，开始我的第一次滑雪之旅。我从未滑过雪或滑雪，所以这应该很有趣。我只希望不要弄坏任何重要的成员。(断了一条腿我也能活。不过，手臂骨折可能会妨碍我编码的能力，我不认为工伤补偿会涵盖这一点。)

总之，在我周末离开纳什维尔之前，我想发布一篇关于代码重用的简短博客。不久前我问你们都想在我的博客和文章中看到什么，一个回答是你们想看到一些关于创建自定义标签的东西。所以，给你。这将是一个关于如何创建一个简单标签的非常简单的描述，但是我想你会明白的，等我回来我会写更多的。

首先，任何 CFM 文件都可以是自定义标签。试试下面的练习。创建一个名为“customtag.cfm”的文件。在自定义标记内，执行以下操作:

 `<cfparam name="Attributes.Hello" default=""><cfoutput>#Attributes.Hello#</cfoutput></cfparam>` 

将其保存到您的 web 根目录，然后创建一个名为“tagtest.cfm”的文件。该文件如下所示:

 `<cf_customtag></cf_customtag>`

 `<cf_customtag hello="Hi Dave!"></cf_customtag>` 

首先让我们检查 customtag.cfm 文件。请注意，我们正在参数化(这是一个词吗？)的**属性**。你好”变量。attributes 作用域通常只在定制标记和模块中可用，尽管有一些例外，我们现在不讨论。注意我们的“tagtest.cfm”文件的第二行。我们传入*属性*“你好”。

还要注意我们调用的标签的名称:cf_customtag。ColdFusion 自定义标记(不是 CFX 标记)总是使用“cf_”然后是文件名(不包括扩展名)来调用。然后，您可以将任何想要的属性传递到标记中。这些属性完全由你来定义。请确保它们遵循 ColdFusion 变量的命名要求。

因此，您可以在这里看到定制标签的基础知识:

*   标签的名称是“cf_”，然后是模板文件的名称
*   在名为的标记中指定的属性进入标记模板中可用的“属性”范围。

同样需要注意的是，如果您将“/tagtest.cfm”移动到“/test/tagtest.cfm”中，您将得到一个错误消息，指出没有找到自定义标记模板。这是因为 ColdFusion 仅在特定位置查找自定义标签:

*   当前模板所在的目录。
*   ColdFusion Administrator 中指定的任何自定义标记目录。这意味着您既可以使用默认的“ <installpath>CustomTags”目录，也可以指定您的自定义标记位于“C:mytags”中。这是在管理员内部完成的。</installpath>

所以有个开始。下周，我们将讨论自定义标签封装和从自定义标签返回变量。与此同时，你可以访问 [CFLib](http://www.cflib.org) 和 [KodeFusion 脚本](http://www.kodefusion.com/ColdFusion_Scripts/)来查看一些免费的定制标签。

## 分享这篇文章