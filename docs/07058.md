# 在 WordPress 中设计一个多页表单:多页处理

> 原文：<https://www.sitepoint.com/design-a-multi-page-form-in-wordpress-multi-page-processing/>

这是关于在 WordPress 中创建多页表单的系列文章[的第三部分，包括它自己的数据库表。如果你还没有熟悉我们的方法和格式，我强烈建议你略读部分](https://www.sitepoint.com/series/design-a-multi-page-form-in-wordpress/)[一](https://www.sitepoint.com/design-a-multi-page-form-in-wordpress-introduction/ "Design a Multi-​​Page Form in WordPress: Introduction")和[二](https://www.sitepoint.com/design-a-multi-page-form-in-wordpress-data-storage/ "Design a Multi-​​Page Form in WordPress: Data Storage")。

在本系列的第一部分中，我们创建了一个短代码，为我们的表单创建了一个 post/page，并创建了我们在这个过程中使用的基本函数。在第二部分中，我们创建了数据库表，插入了表单第一页的输入，并处理了第二页的数据。在第三部分中，我们将把数据更新(而不是插入)到我们为这个表单开始的行中，并展示如何根据以前的数据条目创建一个可选的表单版本。

### 步骤 1:从表单的第二页获取帖子数据

如果您还没有它，请阅读本系列的第二篇文章，并在文章末尾获得代码。从现在开始，我们将继续以此为基础。

从您的“开始表单的第 3 页”注释开始，我们将删除显示第 2 页表单输入的 echo 语句，并用我们的`UPDATE`语句替换它。

我们不想再次使用`$wpdb->insert`函数，因为那会创建一个新行。相反，我们使用`$wpdb->update`函数并加入`$form_id`号来告诉 WordPress 要更新哪一行:

[sourcecode language="php"]

//从表单的第 3 页开始
else if $ Page = = 2){

$ gender = $ _ POST[' gender ']；
$ age = $ _ POST[' age ']；
$ education = $ _ POST[' education ']；
$ income = $ _ POST[' income ']；
$ page = $ _ POST[' page ']；
$ form _ id = $ _ POST[' form _ id ']；

$ page _ two _ table = ' shopping _ preferences '；
$page_two_inputs = array(
'性别' =>$性别，
'年龄' =>$年龄，
'学历' =>$学历，
'收入' =>$收入，
' page ' =>$ page
)；
$ page _ two _ where = array(
' id ' =>$ form _ id
)；

$ insert _ page _ two = $ wpdb--> update($ page _ two _ table，$page_two_inputs，$ page _ two _ where)；

};//结束格式的第 3 页

[/sourcecode]

首先，我们使用`ELSEIF`来测试页码。由于第二页已经完成，我们想插入表单数据。

接下来，我们从表单中获取`POST`数据，并将其分配给变量。太简单了，对吧？

然后，我们开始通过`$wpdb`将数据分配给数组进行插入。我们有 WordPress 在更新行时需要的表、输入和`where`信息。

最后，我们调用`$wpdb->update`函数，这样就好了。嗯，差不多了。

### 步骤 2:评估表单结果

虽然这是可选的，但我想继续给你这些工具以供将来参考。我将使用嵌套的`IF`语句来评估第二页表单数据的结果。根据人的性别，我们将显示不同的表格。

记住性别有三种可能:无，男，女。所以我们必须有三个`IF`语句。

对于女性受访者，与男性相比，我会有不同的选择。如果这个人把自己的性别列为“无”，他们就可以得到所有的选项。

(注意:请忽略这些表单选项中明显的性别歧视——它们只是为了展示动态表单，而不是对性别进行任何类型的区分或陈述。)

[sourcecode language="php"]

//从表单的第 3 页开始
else if $ Page = = 2){

$ gender = $ _ POST[' gender ']；
$ age = $ _ POST[' age ']；
$ education = $ _ POST[' education ']；
$ income = $ _ POST[' income ']；
$ page = $ _ POST[' page ']；
$ form _ id = $ _ POST[' form _ id ']；

$ page _ two _ table = ' shopping _ preferences '；
$page_two_inputs = array(
'性别' =>$性别，
'年龄' =>$年龄，
'学历' =>$学历，
'收入' =>$收入，
' page ' =>$ page
)；
$ page _ two _ where = array(
' id ' =>$ form _ id
)；

$ insert _ page _ two = $ wpdb--> update($ page _ two _ table，$page_two_inputs，$ page _ two _ where)；

echo '

<form method="post" action="’ . $this_page .’">
<label for = " location " id = " location ">你喜欢怎样购物？</label>
<select name = " location "/>
<option value = " nothing " selected>挑选你喜欢的</option>
<option value = " Ebay ">Online–Ebay</option>
<option value = " eretailer ">Online–零售商</option>
<option value = " Classifieds " "</form>

if($ gender = = " nothing "){
echo
'<label for = " category " id = " category ">你最喜欢买什么？</label>
<select name = "类别"/ >
<option value = " nothing "选中>挑选你喜欢的</option>
<option value = " ebay ">衣服</option>
<option value = " eretailer ">鞋子</option>
<option value = " classifieds ">珠宝</option>
<option value = " classifieds " "
}
if($ gender = = 0){
echo
'<label for = " category " id = " category ">你最喜欢买什么？</label>
<select name = " category "/>
<option value = " nothing " selected>挑选你喜欢的</option>
<option value = " store ">运动档位</option>
<option value = " classifieds ">电脑–桌面</option>
<
}
if($ gender = = 1){
echo
'<label for = " category " id = " category ">你最喜欢买什么？</label>
<select name = " category "/>
<option value = " nothing " selected>挑选你喜欢的</option>
<option value = " store ">Sports Gear</option>
<option value = " classifieds ">电脑-桌面< /option
}

回显'
<输入 type="hidden" value= " 3 " name = " page "/>
<输入 type = " hidden " value = " '。$form_id。" name="form_id" / >

<input type="submit">
</形式>’；

};//结束表单的第 3 页

[/sourcecode]

正如您从`IF`语句中所看到的，我们评估了表单性别输入第二页的值，并动态地提供了一个定制的表单。希望你的想象力开始爆发，你可以看到这个小系统对你来说是多么强大！

在这一点上，我们可能还想测试一下，看看我们是否已经在数据库表中获得了我们期望的所有数据。所以，让我告诉你一个快速检查的方法。

### 第三步:检查你的数据库

我通常在测试期间保持 phpMyAdmin 工具打开，但是如果你是新手，这是向你展示一些查询 WordPress 的其他方法的好时机。因此，让我们从数据库中转储数据，看看我们到目前为止存储了什么，并检查它是否收集了我们期望的数据。

我们将使用`$wpdb->select`来查询数据库并显示结果。在我们的结束表单标记的正下方，但在“表单的第 3 页结束”注释的上方，添加以下内容:

[sourcecode language="php"]

//让我们检查一下我们的数据
$ data _ check = $ wpdb->get _ row(" SELECT * FROM shopping _ preferences WHERE id = ' $ form _ id ' ")；

echo '
< p > id:'。$data_check- > id。</p>
<p>first _ name:'。$data_check- >名字。< /p >
< p >姓氏:'。$data_check- >姓氏。< /p >
< p >邮箱:'。$data_check- >邮箱。< /p >
< p >电话:'。$data_check- >手机。< /p >
< p >邮编:'。$data_check- >邮政编码。< /p >
< p >性别:'。$data_check- >性别。< /p >
< p >年龄:'。$data_check- >年龄。< /p >
< p >学历:'。$data_check- >教育。< /p >
< p >收入:'。$data_check- >收入。< /p >
< p >位置:'。$data_check- >位置。< /p >
< p >类别:'。$data_check- >类别。</p>
<p>page:'。$data_check- >页面。< /p >
< p >时间戳:'。$data_check- >时间戳。</p>’；

[/sourcecode]

在这个例子中，我们列出了表中的所有信息。请注意，位置和类别将是空白的，因为我们还没有提交第三页的表单。请记住，我没有限制查询，所以如果您已经运行了大量测试，您将会看到所有的结果。

在本系列的下一篇文章中，我们将创建我们的“谢谢！”页面并用最后一点信息更新数据库。我还将向您展示如何查询数据库并生成一个供您自己使用的报告来查看问卷结果。

## 分享这篇文章