# 为 PHP 做贡献:如何为 PHP 手册做贡献

> 原文：<https://www.sitepoint.com/how-to-contribute-to-phps-documentation/>

在这个由两部分组成的系列文章中，我们将讨论如何为 PHP 项目做出贡献。这将有望澄清那些希望更多地参与 PHP 的人需要采取什么步骤。

这第一部分将涵盖如何为 PHP 文档做贡献，包括如何申请一个[php.net](http://php.net)账户以及一旦一个账户被授予后该做什么。

![PHP logo](img/476714e0faf3d6a5f67b5f14e6fddb0b.png)

## 为什么要给 PHP 投稿？

为什么要考虑为 PHP 做贡献？

PHP 是一个开源项目，它依赖于社区投入时间的意愿。参与的人越多，整个社区受益就越多。无论是围绕语言改进文档，还是为核心贡献 bug 修复或特性，每个开发人员的累积努力很快就会累积起来。

更多地参与 PHP 也将有助于你的语言知识更上一层楼。对文档的贡献会让你对这门语言有一个更透彻的了解，对核心的贡献会让你跟上它所发生的任何变化。成为一名贡献者也将使你最终能够申请一个[php.net](http://php.net)账户，这将使你能够帮助决定这门语言的发展方向。因此，如果你喜欢使用 PHP，这绝对是一件值得做的事情。

## 关于 PHP 的文档

文档以 [DocBook](http://www.docbook.org) XML 格式维护。一般来说，对这种格式的了解很少就能对 PHP 文档有所贡献。这很容易掌握，所以您可以简单地按照文档的其他文件中使用的 XML 语法进行操作。

文档的文件夹结构如下:
![Folder structure overview](img/488db077d573bcbc0227bae678d45749.png)

**doc-base** 文件夹包含一些将基于 XML 的文档转换成其他格式的工具。除了创建自定义实体(通常在向文档添加外部链接时使用)时，您可能不需要太关心这个文件夹。

**en** 文件夹专用于英文文档(其他翻译将遵循其各自的两个字母的国家代码名称)。这个文件夹是您主要工作的文件夹。**参考**文件夹包含每个属于一个扩展的目录。每个扩展文件夹都遵循这样的惯例，要么有一个**函数**文件夹(对于过程扩展)，要么有以扩展的类命名的文件夹(对于面向对象的扩展)。每个扩展文件夹还包含一些其他文件，包括一个用于扩展登录页面的 **book.xml** 文件和一个用于保存每个功能引入时的版本信息的 **versions.xml** 文件。

有关文档结构的更多信息，参见[手册源结构](http://doc.php.net/tutorial/structure.php)页面的*文件结构*部分。

此外，虽然正在努力将文档转换到 Git，但目前仍使用 SVN 进行维护。这意味着如果你想在本地设置文档，你需要安装 SVN，并对它有一个基本的了解。

## 首次投稿者

如果你是第一次投稿，那么你会希望从使用[在线文档编辑器](https://edit.php.net)开始。这提供了一个用户界面，允许任何人登录(使用 OAuth)并向文档提交简单的补丁。一般来说，最好每次都用同一个账户登录，这样你的贡献就只保留在一个名字下(如果你决定以后申请一个 php.net 的[账户，就更容易跟踪它们)。](http://php.net)

在线编辑器几乎总是新文档贡献者的起点。通过展示提交补丁并随后被接受的能力，它展示了贡献的能力和意愿。

作为一个第一次投稿的人，在提交任何补丁之前，你也需要熟悉文档的风格指南。

### 例子

让我们解决来自[bugs.php.net](https://bugs.php.net/)的[错误#71716](https://bugs.php.net/bug.php?id=71716) 。报告指出，其中一个演示示例是不正确的，MongoDB `Client`类的命名空间不正确。

在验证了这一点之后(通过在本地运行连接脚本)，我们可以使用在线编辑器来解决这个问题:

[https://www.youtube.com/embed/X9dRL7uHkjc?rel=0](https://www.youtube.com/embed/X9dRL7uHkjc?rel=0)

有关如何使用在线编辑器的更多信息，请参见[维基编辑器的入门页面](https://wiki.php.net/doc/editor)。

## PHP 文档本地设置

然而，在线编辑器并不是对文档做出贡献的最好方式，而且它所能做的也非常有限。因此，它应该只用于较小的编辑，并作为参与项目的文档方面的第一块踏脚石。

如果你想做编辑能力之外的事情(比如从头开始记录一个函数或扩展)，或者你成为文档的频繁贡献者，那么你会想在本地建立文档并请求一个[php.net](http://php.net)账户。

在您的计算机上本地设置 PHP 文档是一次性的不便。这可以通过以下步骤完成:

1.  **创建一个文档目录**。这将用于在一个地方保存文档和与文档相关的内容:

    ```
     mkdir phpdocs
       cd phpdocs 
    ```

    剩下的步骤都假设您在 **phpdocs** 目录中，除非另有说明。

2.  **克隆文档**。利用 SVN 找到一份回购协议。在我们的例子中，我们希望得到英文手册，因此我们指定了 **doc-en** 模块(在最后):

    ```
     svn checkout https://svn.php.net/repository/phpdoc/modules/doc-en 
    ```

3.  **克隆 PhD** 。 [PhD](http://doc.php.net/phd/docs/) 是一个将 DocBook XML 格式渲染成不同输出格式的工具。

    ```
     git clone http://git.php.net/repository/phd.git 
    ```

4.  **克隆[php.net](http://php.net)网站**。这将用于在本地查看文档，以便您可以在推送之前看到网站上出现的更改。

    ```
     git clone http://git.php.net/repository/web/php.git web-php
       rm -fR web-php/manual/en
       ln -s rendered-docs/php-web web-php/manual/en 
    ```

    我们需要删除位于 **web-php/manual/en** 中的虚拟文档，然后在 **rendered-docs/php-web** 中从生成的文档文件(通过 PhD 生成)中建立一个到这里的符号链接。

5.  **设置 SVN 关键词**。DocBook 文件都在文件顶部附近有一个`<!-- $Revision: 338832 $ -->`注释。我们需要通过配置文件的自动属性，将 SVN 配置为每当文件发生更改时自动更新该值。为此，只需将下面一行添加到`~/.subversion/config`(自动属性部分在末尾附近):

    ```
     *.xml = svn:eol-style=native;svn:keywords=Id Rev Revision Date LastChangedDate LastChangedRevision Author LastChangedBy HeadURL URL 
    ```

    修订 ID 跟踪文件更改，文档翻译人员使用它来查看哪些文件需要更新。

6.  **添加工作流文件**。这是可选的，但是您会发现让这些命令在本地验证、构建和查看文档非常有用。将以下内容粘贴到名为 **ref** 的文件中:

    ```
     # Validate the DocBook XML

       php ~/phpdocs/doc-en/doc-base/configure.php

       # Build the docs

       php ~/phpdocs/phd/render.php --docbook ~/phpdocs/doc-en/doc-base/.manual.xml --package PHP --format php --output ~/phpdocs/rendered-docs

       # Run the php.net website locally

       cd ~/phpdocs/web-php
       php -S 0.0.0.0:4000 
    ```

    以上假设您的`phpdocs`文件夹位于您的主目录中——如果不是，那么相应地更新路径。

现在你已经准备好了！

## 文档工作流

设置好一切之后，是时候看看本地文档的工作流了。为此，让我们解决[错误#71866](https://bugs.php.net/bug.php?id=71866) 。

我们首先要确保版本化的回购都是最新的。这意味着在 **doc-en/en** 和 **doc-en/doc-base** 中执行一个`svn up`，在 **web-php** 和 **phd** 中执行一个`git pull`(通常，你会发现只有第一个回购 **doc-en/en** 需要更新)。

接下来，我们打开`doc-en/en/reference/mbstring/functions/mb-detect-order.xml`文件，并根据错误报告信息修改函数的返回值描述:

```
 <refsect1 role="returnvalues">
   &reftitle.returnvalues;
   <para>
-   &return.success;
+   When setting the encoding detection order, &true; is returned on success or &false; on failure.
+  </para>
+  <para>
+   When getting the encoding detection order, an ordered array of the encodings is returned.
   </para>
  </refsect1> 
```

然后，我们通过运行 docs validator 来确保我们没有破坏 docs 构建:

```
php ~/phpdocs/doc-en/doc-base/configure.php 
```

文档应该验证成功，结果，我们应该看到一张漂亮的 ASCII 猫的图片。

现在，我们既可以在本地查看更改，也可以提交更改。通常情况下，您只是想提交更改(除非您已经进行了任何重大更改以确认它们看起来不错)，但这次我们将出于教导的原因而这样做。

我们通过运行 PhD 工具来构建文档:

```
php ~/phpdocs/phd/render.php --docbook ~/php-docs/doc-en/doc-base/.manual.xml --package PHP --format php --output ~/php-docs/rendered-docs 
```

有时这个阶段第一次对我来说会失败，因为 PHP 耗尽了内存——只需简单地重新运行命令，它应该会构建得很好。

然后进入[php.net](http://php.net)网站的目录，启动本地 PHP 服务器:

```
cd ~/phpdocs/web-php
php -S 0.0.0.0:4000 
```

访问本地主机(在端口 4000 上)并浏览到`mb_detect_order()`函数，我们可以看到所做的更改:

![Changes successfully applied](img/bf5e1f4fe84fb852406ce3e3375de521.png)

既然已经做出了更改，并且我们对它们感到满意，我们可以提交它们了。以下部分假设你有一个[php.net](http://php.net)账户。如果不是这种情况，那么你不必关心这一节的其余部分(相反，你可能希望申请一个 php.net 账户[(见下文)。](http://php.net)

```
cd ~/phpdocs/doc-en/en
svn ci -m "Resolve doc bug #71866" reference/mbstring/functions/mb-detect-order.xml 
```

commit 消息引用了 doc bug，因此可以在我们正在解决的 bug 报告上代表我们进行自动注释。你也不能马上在[php.net](http://php.net)上看到你的改变，但是它们通常会在几个小时内传播到服务器。然后我们可以访问[错误#71866](https://bugs.php.net/bug.php?id=71866) ，假设我们已经登录到我们的[php.net](http://php.net)账户，导航到“开发者”标签并关闭错误报告。

这是解决文档错误的基本工作流程。又快又简单！

## 请求一个[php.net](http://php.net)账户

在本地设置好文档并查看了一般工作流程后，您可能会考虑使用 docs karma 申请一个[php.net](http://php.net)账户。(Karma 为贡献者提供了 PHP 项目不同部分的不同特权。为了直接对文档作出贡献，docs karma 需要在您的 php.net 账户[上。)](http://php.net)

在申请[php.net](http://php.net)文档账户之前，没有具体的先决条件必须满足。一般来说，虽然，帐户将只授予那些谁正在寻找积极贡献和维护文件。因此，在申请账户时，过去的贡献和当前的努力被视为能力和贡献意愿的证据。

要申请账户，请访问[账户申请页面](http://php.net/git-php.php)并仔细阅读。然后，填写表格，提交它，然后发邮件给 PHP 文档邮件列表(phpdoc@lists.php.net)说明你为什么喜欢 karma，你的 wiki 帐户用户名是什么，并提及你过去对该项目所做的任何贡献。如果你的请求成功，你将获得一个 **@php.net** 账户。

## 文档待办事项

除了修复文档中大量归档的 [bug 报告之外，手册中还有其他可以改进的地方，包括:](https://bugs.php.net/search.php?limit=30&order_by=id&direction=DESC&cmd=display&status=Open&bug_type=Documentation+Problem)

*   [翻译文档](http://doc.php.net/tutorial/translating.php)
*   扩展部分文档化的材料(通常通过添加例子)——反射扩展就是一个很好的例子。
*   记录未记录的文档——这将涉及到使用 [lxr 工具](http://lxr.php.net)深入 PHP 源代码
*   一般的页面整理——改写部分(比如删除任何“你”),删除与 PHP 4 相关的材料，将有用的注释合并到手册本身，等等。

## 一般提示

以下是一些关于投稿的提示，有些是基于我的经验，其他的只是重复上面的内容，因为它们很重要:

*   **遵循风格指南**。请确保你遵循了[的风格指南](http://doc.php.net/tutorial/style.php)，并且在投稿时，努力改正不遵循它的文档。我通常至少会在我正在处理的文件中对“我”和“你”进行快速搜索。

*   **检查切向的东西**。这通常是指解决 bug 报告——不要只是修复 bug，还要检查相关的东西是否也受到影响。这有助于确保错误得到正确解决。例如，虽然[错误#71638](https://bugs.php.net/bug.php?id=71638) 只引用了`stream_filter_append`函数，但其对应的`stream_filter_prepend`也需要[进行修正](https://github.com/salathe/phpdoc-en/commit/16b846d3377578d2b21b8d1187644045d0886836)。

*   **言简意赅**。这适用于编写和代码示例。措辞糟糕的文档和复杂的代码示例让事情变得更加难以理解。保持事情简单明了。

*   **将示例代码与其输出分开**。确保示例的输出不与示例本身放在一起。这种约定有助于保持示例的整洁，并清楚地显示其输出。例如，当清理`token_get_all`函数页面时，我不得不[将一个变量的注释转储和一个解释移出示例](https://github.com/salathe/phpdoc-en/commit/7187e86632414c6ac7bd4fa41571fe95d45f827e)。

*   **查看页面顺序**。确保页面上的内容顺序正确。这在手册中创建了一致性，使开发人员能够更容易地引用它。再一次，看看[对`token_get_all`函数](https://github.com/salathe/phpdoc-en/commit/7187e86632414c6ac7bd4fa41571fe95d45f827e)的清理，我们可以看到，changelog 部分最初在页面的底部，而它应该在 examples 部分的上面。

*   **删除对 PHP 4** 的引用。PHP 4 已经过时很久了，现在对它的引用不仅无关紧要，而且会使文档变得复杂。寻找任何提及 PHP 4 的地方(我通常在我正在处理的文件中对它进行快速 grep ),并删除任何提及它的地方。

*   **正确版本文件**。在文档中创建新文件时，请确保将其修订 id 设置为 nothing:

    ```
    <!-- $Revision$ --> 
    ```

    ([例](https://github.com/salathe/phpdoc-en/commit/9d9ced11c2c064b9d3ac8850572b562059f6237f#diff-5517c8f63eae32e2317189b1155186d2R2)。)

    除了创建新文件或翻译文档时，您不需要担心修订 ID。

如果你对某件事不确定，从查看[文档 FAQ](http://doc.php.net/tutorial/faq.php) 开始。如果这没有帮助，那么简单地发送一封电子邮件到 php-docs 邮件列表寻求支持。

再次重申，在提交对手册的任何更改之前，请检查文档构建是否通过！

## 结论

在这篇文章中，我们已经看到了贡献 PHP 文档的两个工作流。第一个是使用[在线编辑器](https://edit.php.net)来解决[错误#71716](https://bugs.php.net/bug.php?id=71716) ，第二个是使用文档的本地设置来解决[错误#71866](https://bugs.php.net/bug.php?id=71866) 。我希望这是关于如何为 PHP 文档做贡献的清晰而实用的介绍。

在本系列的第 2 部分中，我将向您展示如何了解 PHP 的内部机制。这将涉及如何通过再次采取务实的方法来修复核心中的错误。

## 分享这篇文章