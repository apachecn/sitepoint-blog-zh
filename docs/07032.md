# 在 WordPress 中设计一个多页表单:表单完成和数据报告

> 原文：<https://www.sitepoint.com/design-a-multi-page-form-in-wordpress-data-reporting/>

我们已经制作了新的多页表单，是时候应用我们需要的最后一点代码了。如果你刚刚加入我们，去回顾一下这个系列的第 1-3 部分，看看我们的一般理论、设计方法和我们使用的术语。

在本文中，我们将使用最新的值更新数据库，并显示“谢谢！”消息。我们还需要一种无需登录 phpMyAdmin 就可以查看结果的方法，所以我将向您展示一种快速简单的方法来实现这一点。

### 步骤 1:添加最新的表单输入

在这一点上，我们只是冲洗和重复。我们在[第 3 部分](https://www.sitepoint.com/design-a-multi-page-form-in-wordpress-multi-page-processing/ "Design a Multi-​​Page Form in WordPress: Multi-​​Page Processing")中完成了所有这些步骤，但现在我们需要获取位置和类别输入:

[sourcecode language="php"]

//从表单的第 4 页开始
else if $ Page = = 3){

$ location = $ _ POST[' location ']；
$ category = $ _ POST[' category ']；
$ page = $ _ POST[' page ']；
$ form _ id = $ _ POST[' form _ id ']；

$ page _ three _ table = ' shopping _ preferences '；
$ page _ three _ inputs = array(
' location ' =>$ location，
'categories' = > $category，
' page ' =>$ page
)；
$ page _ three _ where = array(
' id ' =>$ form _ id
)；

$ insert _ page _ three = $ wpdb--> update($ page _ three _ table，$page_three_inputs，$ page _ three _ where)；

}//结束表单第 4 页

[/sourcecode]

这里没什么新鲜的，不过还是复习一下吧。首先，我们从 ELSEIF 语句开始检查我们的页码。然后，我们从上一页的表单中获取文章内容。接下来，我们为 WordPress 数据库更新设置数组。最后，我们运行了我们的更新。

### 第二步:建立一个自定义的“谢谢！”消息

现在，你可以插入任何你想要的东西作为“谢谢！”消息。你可以用一些简单的东西:

[sourcecode language="php"]

$ insert _ page _ three = $ wpdb--> update($ page _ three _ table，$page_three_inputs，$ page _ three _ where)；

非常感谢！’；
echo’<p>我们非常感谢您抽出时间来填写这份调查！你们都完了！</p>’；

}//结束表单第 4 页

[/sourcecode]

或者，您可以根据他们的输入创建完全定制的响应。这是你的选择。

你可能想知道如何访问我们所有的精彩数据，并把它好好利用。有很多应用程序，但是让我们看一个简单的表单来查看这个表中的所有数据。

### 步骤 3:自定义报告—将数据转储到 HTML 表中

现在，您可能希望能够自己查看表单输入，或者为您的客户端设置一个视图。我更喜欢一个简单的 PHP 脚本，它调用数据并以表格的形式显示在页面上。这允许您的客户将数据转储到 Excel 或任何他们喜欢的软件中进行数据处理。我们将从表中所有信息的简单转储开始。

(注意:你需要能够通过 FTP 上传一个文件到你的 WordPress 站点或者使用你的域名主机来上传文件。)

使用以下内容创建一个新的 PHP 文件:

[sourcecode language="php"]

$ location = $ _ SERVER[' DOCUMENT _ ROOT ']；

包括(＄位置。/WP-config . PHP’)；
包括(＄位置。/WP-load . PHP’)；
包括(＄位置。/WP-includes/pluggable . PHP ')；
global $ wpdb；

$ form _ results = $ wpdb-> get _ results(“SELECT * FROM shopping _ preferences”)；

echo '
<thead>
<tr>
<th>ID</th>
<th>名< /th >
< th >姓< /th >
< th >邮箱< /th >
< th >电话< >学历< /th >
< th >收入</th>
th>地点</th>
th>类别</th>
T71>页</th>
th

foreach($ form _ results as $ form _ results){
echo '
<tr>
<TD>'。$form_results- > id。</TD>
<TD>’。$form_results- >名字。</TD>
<TD>’。$form_results- >姓氏。</TD>
<TD>’。$form_results- >电子邮件。</TD>
<TD>’。$form_results- >电话。</TD>
<TD>’。$form_results- >邮政编码。</TD>
<TD>’。$form_results- >性别。</TD>
<TD>’。$form_results- >年龄。</TD>
<TD>’。$form_results- >教育。</TD>
<TD>’。$form_results- >收入。</TD>
<TD>’。$form_results- >位置。</TD>
<TD>’。$form_results- >类别。</TD>
<TD>’。$form_results- >页面。</TD>
<TD>’。$form_results- >时间戳。</TD>
</tr>
’；
}

回声''；

？>

[/sourcecode]

这段代码假设你的 WordPress 安装在你的网站的根文件夹中。如果没有，请修改前三个 INCLUDE 语句以获得正确的文件夹。

让我们慢下来，看看我们刚刚做了什么。首先，我们告诉这个 PHP 脚本，在 INCLUDE 语句中可以找到所有相关的数据库信息。

接下来，我们使用内置的 get_results()函数设置一个查询。如果您更熟悉标准 SQL 和 MYSQL 语法，您可以使用这些语言运行任何查询。

然后，我们摆好桌子。使用 CSS 和 JavaScript，你可以随心所欲，但是对于这个例子，我只想给你一些基本的工具。

最后，我们运行了一个 FOREACH 语句，循环遍历查询的每个结果。您可以创建一个包含全部或部分结果的表格。因此，如果您想要一个更加定制化的表，只包含电子邮件地址、年龄和性别；它可能看起来像这样:

[sourcecode language="php"]

$ location = $ _ SERVER[' DOCUMENT _ ROOT ']；

包括(＄位置。/WP-config . PHP’)；
包括(＄位置。/WP-load . PHP’)；
包括(＄位置。/WP-includes/pluggable . PHP ')；
global $ wpdb；

$ form _ results = $ wpdb-> get _ results("从 shopping_preferences 中选择邮件、年龄、性别")；

echo '
<thead>
<tr>
<th>Email</th>
<th>年龄< /th >
< th >性别</th>
</tr>
/thead>'；

foreach($ form _ results as $ form _ results){
echo '
<tr>
<TD>'。$form_results- >电子邮件。</TD>
<TD>’。$form_results- >年龄。</TD>
<TD>’。$form_results- >性别。</TD>
</tr>
’；
}

回声''；

？>

[/sourcecode]

以这种方式创建所有您想要的定制报告，并给客户端一个到每个报告的链接。当他们需要更多报告时，只需创建一个查询，然后通过 JavaScript 完成客户端所需的任何其他计算(尽管一个结构合理的 SQL 查询应该可以满足大多数报告需求)。

### 步骤 4:保护您的报告

虽然从技术上来说*可以*让任何人通过这个网址自由执行这些报告，但显然你应该**而不是**这样做。使用上面的例子，所有的电子邮件地址、姓名和其他用户信息都暴露给了有链接的任何人。

因此，让我们聪明地使用 WordPress 结构中的报告，这为我们提供了更多的安全性。在本例中，我将要求用户以站点管理员的身份登录(其他标准选项是编辑、作者、贡献者和订阅者，按权限降序排列)。

首先，我们希望创建一个短代码，以便报告可以显示在帖子或页面上。这可以放在你的主题的 functions.php 文件或者一个单独的插件中:

[sourcecode language="php"]

add _ short code(' multipage _ email _ age _ gender _ report _ sc '，' multipage _ email _ age _ gender _ report ')；

函数 multipage _ email _ age _ gender _ report(){

$ location = $ _ SERVER[' DOCUMENT _ ROOT ']；

包括(＄位置。/aeon strong/WP-config . PHP ')；
包括(＄位置。/aeon strong/WP-load . PHP ')；
包括(＄位置。/aeon strong/WP-includes/pluggable . PHP ')；
global $ wpdb；

$ form _ results = $ wpdb-> get _ results(
"
从 shopping_preferences
")中选择邮箱、年龄、性别
；

echo '
<thead>
<tr>
<th>Email</th>
<th>年龄< /th >
< th >性别</th>
</tr>
/thead>'；

foreach($ form _ results as $ form _ results){
echo '
<tr>
<TD>'。$form_results- >电子邮件。</TD>
<TD>’。$form_results- >年龄。</TD>
<TD>’。$form_results- >性别。</TD>
</tr>
’；
}

回声''；
}

？>

[/sourcecode]

此时，任何可以访问帖子或页面的人都可以看到这些结果。如果你愿意的话，你可以使用内置的密码保护来保护每一篇文章或每一页——有时客户喜欢这样。这也正是我包含这个版本而不是直接跳到基于用户的报告的原因。

要锁定报告，使其只能由具有管理员角色的用户查看，这里有一种常用方法，通过查看管理员是否能够执行默认情况下只有管理员才能执行的任务来检查管理员是否登录。在这种情况下，它是创建用户的能力。如果他们没有创建用户的权限，他们会收到一条消息:

[sourcecode language="php"]

add _ short code(' multipage _ email _ age _ gender _ report _ sc '，' multipage _ email _ age _ gender _ report ')；

函数 multipage _ email _ age _ gender _ report(){
if(current _ user _ can(' create _ users '){

$ location = $ _ SERVER[' DOCUMENT _ ROOT ']；

包括(＄位置。/WP-config . PHP’)；
包括(＄位置。/WP-load . PHP’)；
包括(＄位置。/WP-includes/pluggable . PHP ')；
global $ wpdb；

$ form _ results = $ wpdb-> get _ results(
"
从 shopping_preferences
")中选择邮箱、年龄、性别
；

echo '
<table cell padding = " 0 " cellspacing = " 0 " border = " 1">
<thead>
<tr>
<th>Email</th>
<th>年龄< /th >
< th >性别< /th >
< /tr >

foreach($ form _ results as $ form _ results){
echo '
<tr>
<TD>'。$form_results- >电子邮件。</TD>
<TD>’。$form_results- >年龄。</TD>
<TD>’。$form_results- >性别。</TD>
</tr>
’；
}

回声''；
}//End If
else {
echo '<p>您没有权限查看此报告。</p>’；
}//End else
}//End multipage _ email _ age _ gender _ report()

[/sourcecode]

### 结论

嗯，那应该差不多够了！你应该拥有在 WordPress 中创建一个定制的多页表单的基本工具，并且能够安全地访问你或你的客户想要创建的任何定制报告。

我迫不及待地想看看你们都想出了什么！请随意分享；您甚至可能从中获得一些有价值的数据！

## 分享这篇文章