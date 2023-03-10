# 在 WordPress 中设计多页表单:简介

> 原文：<https://www.sitepoint.com/design-a-multi-page-form-in-wordpress-introduction/>

你有没有发现自己需要在 WordPress 中创建一个自定义的多页表单？我知道…一直都知道！虽然您可以使用各种插件来完成工作，但它们很少能完美地满足您的需求。我发现，对我来说，做好每件事的最简单的方法——从表单到数据到报告——就是自己从头开始创建。

在这一系列文章中，我将带您从头开始创建多个页面表单。信不信由你，如果你了解一些基本的 PHP 并知道你的 HTML 表单基础，这是相当简单的。(如果你是一名 PHP 老手或者有兴趣成为一名，你应该去我们的 PHP 合作伙伴网站，[PHPMaster.com](https://www.sitepoint.com)

我们将使用 POST 方法将信息从表单传递回自身，并使用 PHP 中的 IF 语句显示表单的下一部分。当用户提交表单数据时，该页面将随表单的下一页一起更新，同时存储提交的信息。

结果是用户停留在同一个页面上，您可以根据他们的回答有条件地(甚至动态地)生成表单的下一页。或者，为了简单起见，您可以保持表单字段静态。

### 为什么是多页表单？

您可能需要多页表单的原因有很多。在某些情况下，你可能不希望每页超过 3-5 个问题。在其他情况下，您可能只想根据他们最初的反应收集额外的信息。例如，如果用户表示他们未满 18 岁，您可能不想问某些问题，或者您可能希望根据男性或女性回答者显示不同的表单。

此外，多页表单允许您分阶段收集信息。如果用户因为某种原因放弃问卷，你至少收集了一些可能有用的信息。表单废弃是很常见的，多页表单收集为您提供了重要的数据，比如表单最常被废弃的地方、后续工作的用户的基本联系信息等等。

### 个案研究

出于规划目的，我将收集用户及其购物偏好的基本信息。所以，这是我们的形式将如何进展:

*   第 1 页–基本联系信息
    *   姓名(名和姓)，
    *   电子邮件，
    *   电话，
    *   邮政区码
*   第 2 页–社会经济数据
    *   性别
    *   年龄
    *   教育
    *   收入
*   第 3 页–购物偏好
    *   位置(在线、商店)
    *   最喜欢的类别
*   第 4 页-“谢谢！”登录页面

我将把它设置在我们的 functions.php 文件中，这对于测试来说很好，但是当你准备在你的站点上运行它时，你应该考虑把它做成一个插件或者至少把这段代码从你的 functions.php 文件中分离出来，并使用 include_once 语句来链接你的 functions.php 文档中的文件。

我使用这种方法的部分原因是为了避免钻研 FTP 和其他在 WordPress 中创建自定义功能的不太相关的方法。所以，对于你们这些老兵，请忍受我不太理想的方法。

### 步骤 1:设置一个短代码

这是可选的，但它确实让我更容易使用短代码来显示这些多页表单。所以，这就是我要教的方法，如果你愿意，你可以实现你自己的方法。

在您的 functions.php 文件中，添加以下内容:

[source code language = " PHP "]
add _ short code(' multipage _ form _ sc '，' multipage _ form ')；
函数 multipage _ Form(){
echo '<H3>新表单</H3>'；
}；
[/源代码]

这让 WordPress 知道你可以把短代码 *multipage_form_sc* 放到一篇文章或页面中，并且 WordPress 应该在找到短代码时执行函数 multipage_form。因为我经常有几个用于给定站点的多页表单，所以我给这些表单起了唯一的名字，例如 multipage_form_userinfo_sc 和它对应的函数 multipage_form_userinfo。

### 第二步:建立一个帖子或页面

创建希望多页表单存在于其上的新文章或页面。将短代码插入表单，发布它，然后查看页面。您应该会看到我们在 multipage_form()函数中创建的“新表单”消息。

在你的文章/页面编辑器中，你的短代码应该是这样的:

```
[multipage_form_sc]
```

### 步骤 3:第一个表单元素

让我们开始创建第一个表单元素。为了把重点放在多页表单的概念上，而不是放在 CSS 或其他我们以后可以深入研究的吸引眼球的东西上，我将保持这个非常简单。

所以，下面是我们可以粘贴到 multipage_form()函数中的基本信息:

[source code language = " PHP "]
add _ short code(' multipage _ form _ sc '，' multipage _ form ')；
函数 multipage_form(){
全局$ wpdb
$ this _ page = $ _ SERVER['请求 _ URI ']；
$ page = $ _ POST[' page ']；
if($ page = = NULL){
echo '<form method = " post " action = " '。$this_page。">
<label for = " First _ Name ">First Name:</label>
<input type = " text " Name " id = " First _ Name "/>
<label for = " Last _ Name " id = " Last _ Name ">Last _ Name:</label>
<input type = " text " Name = " Last >
<label for = " first _ name " id = " first _ name ">邮政编码:</label>
<input type = " text " name = " Zip _ Code " id = " Zip _ Code "/>
<input type = " hidden " value = " 1 " name = " page "/>
<input type = " submit "/<
}//结束表单第 1 页
else if $ Page = = 1){
$ first _ name = $ _ POST[' first _ name ']；
$ last _ name = $ _ POST[' last _ name ']；
$ email = $ _ POST[' email ']；
$ phone = $ _ POST[' phone ']；
$ zip _ code = $ _ POST[' zip _ code ']；你已经看到第二页了！< /h3 >
< p >这里是你的表单输入:< /p >
< p >名字:'。$first_name，”< /p >
< p >姓氏:'。$last_name，”< /p >
< p >邮箱:'。$电子邮件。< /p >
< p >电话:'。$手机。< /p >
< p >邮编:'。$zip_code。</p>’；
}//结束表单
}的第 2 页；
[/sourcecode]

这是一大堆代码，所以让我们来重点讨论一下。

首先，我们有我们正在寻找的初始 POST 语句——$ this _ page 是我们当前进行表单处理的页面，而$page 是页码。我用“1”代表第一页，“2”代表第二页，等等。一旦我们存储了这些信息，我们将有一个完整的页码记录。我们还将使用页码来测试我们在表单的哪一页，并相应地显示信息。

接下来，我们有第一个 IF 语句来测试我们在哪个页码上。如果它是空的，我们显示表单的第一页。这就是我们的基本表单元素——标签和输入。我包含了页码的隐藏表单值，但是您也可以添加各种额外的隐藏表单字段来收集用户名(如果他们已经登录)以及您可能需要的其他信息。

之后，我们有从 ELSEIF 语句开始的第二页信息。我们将继续使用 ELSEIF 语句来处理和显示表单数据。如果您有很多很多页面，您可以使用 SWITCH 语句，但是 if 格式在这种情况下对我们来说很好。

在第二页上，我们获取表单输入并显示它们以供测试。在下一篇文章中，我们将把这些信息存储在一个数据库表中，并显示表单的下一页。

### 尝试一下

用上面的代码保存你的 functions.php 文件(不要错过任何东西；一个错误的分号或大括号会使你的网站崩溃！)转到您在步骤 2 中创建的帖子或页面，填写表格的第一页，然后单击“提交”按钮。瞧啊。

### 接下来是！

在本系列的下一期中，我们将跳转到 phpMyAdmin 并在数据库中创建一个表，开始存储我们收集的所有这些精彩的信息。不要担心，我会保持它非常基本，甚至会为您编写 MySQL 脚本，如果您对这方面的东西是全新的。

你知道我们有一个专门致力于掌握 PHP 的网站吗？查看我们在[PHPMaster.com](https://www.sitepoint.com)的合作伙伴网站。

## 分享这篇文章