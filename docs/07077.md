# 在 WordPress 中设计一个多页表单:数据存储

> 原文：<https://www.sitepoint.com/design-a-multi-page-form-in-wordpress-data-storage/>

在多页表单设计系列的第二篇文章中，我们将创建存储自定义表单数据所需的数据库表。我们还将深入研究存储表单数据的第一页并向用户显示第二页的过程。我们使用一些非常基本的 PHP 和 SQL 来实现我们的设计目标。(如果你有兴趣学习更多关于 PHP 的知识，我们的合作伙伴网站，【PHPMaster.com】的[有各种各样的指南，从](https://www.sitepoint.com "PHPMaster")[初学者](https://www.sitepoint.com/category/php-tutorials/beginner/)到[专家](https://www.sitepoint.com/category/php-tutorials/expert/)。)

### 步骤 1:使用 phpMyAdmin 创建一个数据库表

如果您从未使用过 phpMyAdmin，这对您来说是一大步。我知道这可能令人望而生畏，但是定制表单开发通常意味着您将需要定制数据库表。虽然你可以使用现有的、内置的 WordPress 数据表，并将这些信息存储为用户元数据，但你必须经历更多的考验才能做到这一点。最后，避免 phpMyAdmin 比学习它要困难得多。

所以，去你的域名主机提供商，登录，并进入你的主机控制面板。您应该会看到 phpMyAdmin 或其他一些数据库管理工具的按钮或链接。因为绝大多数域托管提供商都使用 phpMyAdmin，所以我将用它作为我的例子。

登录 phpMyAdmin 后，转到 WordPress 安装的 SQL 选项卡，粘贴以下代码:

[sourcecode language="sql"]
创建表` shopping _ preferences `(
` id ` INT(7)NOT NULL AUTO _ INCREMENT，
` first _ name ` VARCHAR(50)NOT NULL，
` last _ name ` VARCHAR(50)NOT NULL，
`email` VARCHAR( 50 ) NOT NULL，
`phone` VARCHAR( 12 ) NOT NULL，
`zip_code` VARCHAR( 5 ) NOT NULL，
`gender` INT( 1
` location ` VARCHAR(50)NOT NULL，
` categories ` VARCHAR(400)NOT NULL，
`page` INT( 1 ) NOT NULL，
` TIMESTAMP ` TIMESTAMP NOT NULL DEFAULT CURRENT _ TIMESTAMP，
主键(` id ')
)
[/source code]

当然，您可以根据需要修改这段代码，但是这将获得一个新的数据表，并允许您从我们的多页表单的输入开始添加内容。

### 步骤 2:添加第一页数据

对于这一步，我们将完成两件事:

1.  将第一页表单输入发送到我们在步骤 1 中创建的数据库表中
2.  检索表单数据的 ID，以便我们可以在用户填写表单时不断添加更多信息。

为了将数据添加到我们的数据库表中，我们将使用内置的 WordPress $wpdb。虽然你可以创建一个 MySQL INSERT 脚本，但是在使用 WordPress 数据库时，最好使用它们精心设计的功能。

这是一个简单的过程，也是有史以来最不直观的过程。一旦您掌握了使用$wpdb 的诀窍，您就会变得很好。

首先，我们需要从表单的第一页获取文章数据。如果您曾经使用过表单，这是一个熟悉的过程。

插入过程从使用分配给变量的数组格式定义列开始。然后插入函数从那里获取它。

[source code language = " PHP "]
//开始表单第 2 页
else if $ Page = = 1){
//抓取用户提供的帖子数据
$ first _ name = $ _ POST[' first _ name ']；
$ last _ name = $ _ POST[' last _ name ']；
$ email = $ _ POST[' email ']；
$ phone = $ _ POST[' phone ']；
$ zip _ code = $ _ POST[' zip _ code ']；
//为我们即将到来的插入函数
$ page _ one _ table = ' shopping _ preferences '分配表和输入；
$ page _ one _ inputs = array(
' first _ name ' =>$ first _ name，
' last _ name ' =>$ last _ name，
'email' = > $email，
'phone' = > $phone，
'zip_code' = > $zip_code，
' page ' =>$ page
)；
//将数据插入新行
$ Insert _ page _ one = $ wpdb->Insert($ page _ one _ table，$ page _ one _ inputs)；
//抓取我们插入的行的 ID 以备后用
$ form _ ID = $ wpdd->insert _ ID；
echo ' < h3 >您已经进入第 2 页了！< /h3 >
这是您的表单输入:
名字:'。$first_name。姓氏:'。$last_name。电子邮件:'。$电子邮件。
电话:'。$手机。邮政编码:'。$zip_code。
表单 ID:'。$form_id。";
}//结束表单
[/sourcecode]的第 2 页

在代码的最后一部分，我们对数据进行了一些初步检查，显示了关于进入表单第二页的消息，然后向提供输入值的用户显示了存储的输入值。如果我们有一个表单 ID 值，我们就成功地插入了一行！

### 步骤 3:添加“第二页”表单

因此，我们从表单的第一页插入了一行数据，现在我们准备收集更多的信息。这一次，我们想获得一些社会经济数据。即使用户在这一点上依赖我们，我们仍然有一些有用的信息，我们可以用它们来联系他们。

在上面的$form_id 代码之后，我们将替换其余的代码，并添加我们的奇特表单的第二页:

[source code language = " PHP "]
echo '<form method = " post " action = " '。$this_page。">
<标签 for="gender" id="gender" >性别:</标签>
<选择 name="gender" / >
<选项 value="nothing "选中>选择性别</选项>
<选项 value="0" >女</选项>
<选项 value="1" >男< >
<label for = " Education " id = " Education ">Education:</label>
<select name = " Education "/ >
<option value = " nothing "选中>选择教育程度</option>
<option value = " Some _ High _ School ">某高中</option>
<option value = " all _ High _ School ">高中文凭/GED</option>
<option value = " Some _ School 有的研究生</选项>
<选项 value="all_doc" >博士学位</选项>
</选择>
<标签为= "收入" id= "收入">收入:</标签>
<选择名称= "收入"/ >
<选项 value="nothing "选中>选择收益范围</选项>
<选项 value="10000 "选中>小于 10000 美元</选项>
<选项 value="25000 "选中>10000-25000 美元< $form_id。" name = " form _ id "/>
<input type = " submit "/>
</form>'；
}//结束表单第 2 页
[/sourcecode]

为了简洁起见，我将“年龄”选项留在空白处，这样我们就不会有一个包含大量选项的长表单。最终版本将有一个下拉菜单。

### 步骤 4:构建第 3 页处理程序

现在，让我们抓住第二页的信息，确保我们已经抓住了我们所需要的。出于测试目的，我们将在页面上显示它。

需要另一个 ELSEIF 语句来测试页码。只需将它放在前面代码示例的“结束第 2 页”注释之后:

[source code language = " PHP "]
else if($ page = = 2){
$ gender = $ _ POST[' gender ']；
$ age = $ _ POST[' age ']；
$ education = $ _ POST[' education ']；
$ income = $ _ POST[' income ']；
$ page = $ _ POST[' page ']；
$ form _ id = $ _ POST[' form _ id ']；
echo ' $ gender:'。$性别。";
echo ' $ age:'。$年龄。”；呼应' $education:'。$教育。";
echo ' $ income:'。$收入。”；
回显' $page:'。$page。";
回显' $form_id:'。$form_id。；
}
[/源代码]

请确保您的函数仍有结束符号“}；”大括号——很容易复制并粘贴到它的顶部。缺少这些左大括号或右大括号中的任何一个都会破坏整个窗体，所以要小心操作。

### 结论

刷新您的表单，看一看！我们越来越接近了！您已经有了一个两页的表单，它成功地从第一页到第二页收集并存储了数据。这是巨大的第一步。

在下一篇文章中，我将向您展示如何用第二页的输入更新数据库，以及如何显示表单的可选版本——一个用于男性，一个用于女性。

为了完整起见，下面是我们目前掌握的代码:

[source code language = " PHP "]
add _ short code(' multipage _ form _ sc '，' multipage _ form ')；
函数 multipage_form(){
全局$ wpdb
$this_page = $_SERVER['请求 _ URI ']；
$ page = $ _ POST[' page ']；
if ( $page == NULL ) {
echo '

<form action="’ . $this_page .’" method="post"><label id="first_name" for="first_name">名字:</label>
<输入 id= "名字" type="text" name= "名字"/ >
<标签 id= "姓氏" for= "姓氏">姓氏:</标签>
<输入 id= "姓氏" type="text" name= "姓氏"/ >
<标签 id="email" for="email" 【T43 >
<label id = " first _ name " for = " first _ name ">邮政编码:</label>
<input id = " Zip _ Code " type = " text " name = " Zip _ Code "/>
<input type = " hidden " name = " page " value = " 1 "/>
<input type = " submit "/><
}//结束表单第 1 页
//开始表单第 2 页
else if $ Page = = 1){
$ first _ name = $ _ POST[' first _ name ']；
$ last _ name = $ _ POST[' last _ name ']；
$ email = $ _ POST[' email ']；
$ phone = $ _ POST[' phone ']；
$ zip _ code = $ _ POST[' zip _ code ']；
$ page _ one _ table = ' shopping _ preferences '；
$ page _ one _ inputs = array(
' first _ name ' =>$ first _ name，
' last _ name ' =>$ last _ name，
'email' = > $email，
'phone' = > $phone，
'zip_code' = > $zip_code，
' page ' =>$ page
)；
$ insert _ page _ one = $ wpdb->insert($ page _ one _ table，$ page _ one _ inputs)；
$ form _ id = $ wpdb->insert _ id；
回声</form>

<form action="’ . $this_page .’" method="post"><label id="gender" for="gender">性别:</label></form>

选择 GenderFemaleMale

<label id="age" for="age">年龄:</label>
<输入 id = " Age " type = " text " name = " Age "/>
<标签 id = " Education " for = " Education ">学历:</标签>

选择教育水平一些高中文凭/大学文凭一些研究生院毕业一些研究生院博士学位

<label id="income" for="income">收入:</label>

选择收入范围 10，000 美元以下 10，000 美元到 25，000 美元到 50，000 美元 50，000 美元到 75，000 美元以上

<input type="hidden" name="page" value="2">
<输入 type = " hidden " name = " form _ id " value = " '。$form_id。"/ >
<输入 type="submit" / >

；
}//结束表单第 2 页
//开始表单第 3 页
else if $ Page = = 2){
$ gender = $ _ POST[' gender ']；
$ age = $ _ POST[' age ']；
$ education = $ _ POST[' education ']；
$ income = $ _ POST[' income ']；
$ page = $ _ POST[' page ']；
$ form _ id = $ _ POST[' form _ id ']；echo '$gender:'。$性别。";
echo '$age:'。$年龄。”；
echo '$education:'。$教育。";
echo '$income:'。$收入。”；
回显' $page:'。$page。";
回显' $form_id:'。$form_id。";
}；//结束表单第 3 页
}//结束 multipage_form()函数
[/sourcecode]

## 分享这篇文章