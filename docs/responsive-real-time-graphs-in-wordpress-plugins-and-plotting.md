# WordPress 中的响应性实时图形:插件和绘图

> 原文：<https://www.sitepoint.com/responsive-real-time-graphs-in-wordpress-plugins-and-plotting/>

在我们关于在 WordPress 站点内创建一个响应性的实时图表系列的这一部分中，我们将最终得到好的东西！最后三部分是为了让新的设计者快速上手。如果你刚刚开始，跳过太快，或者非常困惑，回到前面的部分，回顾一下。有些代码是这个项目特有的，比如让我们的图形库工作所需的 Unix 时间戳。

我已经建立了一个简单的俯卧撑跟踪器表单和数据库表，用于我们的 [Flot](http://www.flotcharts.org/) 图表。当然，您可以使用任何数据源来填充页面中的数据。但是，为了教一个新手如何开始使用表单和 phpMyAdmin，我想从头开始介绍整个过程。

一旦你在你的 WordPress 站点中有了这个系统，你就可以创建任何类型的图表数据。Flot 图形库是广泛的，非常容易实现。正如我将要展示的，你可以用我们的俯卧撑跟踪器冲洗和重复，并创建一个强大的系统来显示这些数据——所有这些都在一个响应性网站上实时进行。

### 步骤 1:获取 Flot 库

我使用 [Flot](http://www.flotcharts.org/) 制作我的图表有很多原因，但主要是因为它易于实现，并且我能够让它在任何可以想象的设备上工作。它使用 HTML5 中的 canvas 特性来绘制数据，并且能够做一些疯狂的、令人惊奇的事情。你想用什么都可以。但是，对于本教程，我们将使用 Flot。

具体来说，我们使用我的插件(WordPress 的 Flot)在 WordPress 中正确注册和运行 Flot 库。我强烈推荐使用这个插件，但是没有它你也可以实现 Flot。

WordPress 插件的 Flot】

### 第二步:发布俯卧撑的结果

在 WordPress 中，转到“帖子”的“添加新内容”，创建一个标题为“俯卧撑结果”的帖子在正文中，放入以下短代码:

```
[pushups_results_sc]
```

发布帖子就完事了。现在，文章内容将显示确切的文本“[pushoups _ results _ sc]”，因为我们实际上还没有为短代码创建函数。

### 步骤 3:创建我们的图表

在这个系列的第 2 部分中，我们在 WordPress 数据库中创建了一个新的表格，叫做俯卧撑。我们用它来存储表单中的俯卧撑信息。如果您真的熟悉 phpMyAdmin，请转到 SQL 选项卡并运行以下查询来创建我们将使用的表:

[sourcecode language="sql"]

创建表`{您的数据库} ` . ` pushops `(
` pushops _ id ` INT(10)NOT NULL AUTO _ INCREMENT，
` pushops _ count ` INT(3)NOT NULL，
` pushops _ date ` VARCHAR(15)NOT NULL，
` pushops _ WP user ` VARCHAR(100)NOT NULL，
` active ` INT(2)NOT NULL DEFAULT ' 1，
INDEX(` pushops _ id `)
)ENGINE = MYISAM

[/sourcecode]

到目前为止，一切都是关于建立基础设施，以便您可以开始提取数据并以令人敬畏的方式显示数据。从现在开始，一切都是关于图表的！

创建一个新的 PHP 文件，并将其命名为 pushups _ results.php。在粘贴以下代码后，您将把它上传到您的 WP-content/plugins/flot-for-WP/flot 文件夹:

[sourcecode language="php"]

/*
描述:这段代码是由 WordPress shortcode
调用的，它调用数据库并在 Flot 图表中显示数据。
作者:@jphornor 和@tarahornor
授权:GPL3
*/

//下面是我们的函数！
函数 pushoups _ results(){

/*
获取 db 连接。如果没有创建 connect_to_db.php
文件，在这里添加自己的数据库连接信息
*/
include(" connect _ to _ db . PHP ")；
连接 _ 到 _ 数据库()；
/*
这会获取当前登录的用户，并将用户名
分配给变量 wpuser，该变量在主查询
*/
get_currentuserinfo()中使用；
$ current _ user = WP _ get _ current _ user()；
$ WP user = $ current _ user->user _ log in；

//大考！如果用户没有登录，他们什么也得不到！
如果(！is _ user _ logged _ in())
{
echo '<a href = " '。wp_login_url()。'">登录< /a >查看俯卧撑结果！
< p > < a href= " "。' home_url()。'/wp-register.php" >如果您还没有注册< /a >。</p>’；

}
否则
{

//查询
$ SQL = MySQL _ query(" select * from pushops where ` pushops .` pushops _ WP user `= ' $ WP user '和` pushops ` . ` active `= 1 ORDER BY ` pushops ` . ` pushops _ date ` ASC ")；

/*
最小和最大查询。通常没有必要为 Flot 生成我们自己的最小和最大数据，我们下面的过程创建了一个以逗号结尾的数据集
，Flot 将其解释为 0。
*/
$ min = MySQL _ query(" select pushops _ count FROM pushops ORDER BY pushops _ count ASC limit 1 ")；
$ max = MySQL _ query(" select pushops _ count FROM pushops ORDER BY pushops _ count desc 限制 1 ")；

//我们需要计算图上的最小点
while($ row = MySQL _ fetch _ array($ min))
{
$ min _ count = $ row[' pushops _ count ']；
}
$ min _ count = $ min _ count * . 9；//这在图表上给了我们一些喘息的空间

//我们来计算一下图上的最大点
while($ row = MySQL _ fetch _ array($ max))
{
$ max _ count = $ row[' pushops _ count ']；
}
$ max _ count = $ max _ count * 1.1；//这也让我们在图表上有了一些喘息的空间

/*
现在我们在页面上生成 JavaScript 和 HTML。
这不是我最喜欢的方式，但它完成了工作。
*/
echo '<div id = " flot-container ">'；//在 layout.css 文件中为你的 WP 安装自定义宽度和高度
echo '<h1>push up Results for '。$wpuser。</h1>’；
echo '<script language = " JavaScript " type = " text/JavaScript " src = " '。'插件 _url( $path)。'/flot-for-WP/flot/jquery . js "></script>
<script language = " JavaScript " type = " text/JavaScript " src = " '。'插件 _url( $path)。'/flot-for-WP/flot/jquery . flot . js "></script>
<script language = " JavaScript " type = " text/JavaScript " src = " '。'插件 _url( $path)。'/flot-for-WP/flot/jquery . flot . resize . js "></script>
<script language = " JavaScript " type = " text/JavaScript " src = " '。'插件 _url( $path)。'/flot-for-WP/flot/excan vas . min . js "></script>
<链接 href= " '。'插件 _url( $path)。'/flot-for-WP/flot/layout . CSS " rel = " style sheet " type = " text/CSS ">T10<div id = " placeholder " style = " width:95%；身高:85%；最大宽度:100%；边距:自动；“></div>
”；
echo '
<脚本语言= " JavaScript " type = " text/JavaScript " id = " source ">

jQuery(文档)。ready(function($){
var D1 =[
{ label:“俯卧撑跟踪器”。$wpuser。”，
数据:[’；
while($ row = MySQL _ fetch _ array($ SQL))
{
echo '['。$ row[' pushops _ date ']。,' .$ row[' pushops _ count ']。],';
}
echo ']}]；
var placeholder = $(" # placeholder ")；
var plot = $。plot(placeholder，d1，
{
xaxis:
{ mode:" time " }，
points:
{ show: true，symbol: "circle"}，
lines:
{ show: true}，
legend:
{ show: false }，
yaxis:
{ min:'。$min_count。，max:'。$max_count。}
})；
})；
</脚本></div>’；
}
}
//俯卧撑结果帖中的 short code
add _ short code(' pushops _ results _ sc '，' pushops _ results ')；
MySQL _ close()；

？>

[/sourcecode]

我知道这是一吨的信息，我尽我所能添加评论。同样，这个名为 pushups _ results.php 的文件位于您的 WP-content/plugins/flot-for-WP/flot 文件夹中。我很抱歉这里的代码看起来如此压缩，但是如果你下载我的资源文件，你可以看到更有组织的格式的内容。

### 概述

虽然这是很多代码，但请花时间查看注释。有很多事情正在进行，如果你不跟踪 Flot 如何解释数据，你将很难使用自己的数据。

在这篇文章中，我们处理了一些相当密集的过程。我试图通过让你复制和粘贴来让你轻松一些，但是不要偷懒！你不能仅仅添加这个插件就得到神奇美丽的图形。所以请花些时间来回顾这段代码。

简单回顾一下，在本系列的这一部分中，我们讨论了:

*   抓住 Flot 插件(对于刚刚进入这个系列的人)
*   创建帖子以显示结果
*   将 Flot 代码添加到提取数据并正确显示数据的页面中。

信不信由你，你实际上已经基本完成了！如果你刷新你的俯卧撑结果页面，你应该会看到一个图表！

在本系列的下一部分，我将详细讨论如何使用 PHP 和 MySQL 处理 Flot。

在最后一部分，我将讨论如何设计你的图表。因此，如果您是一名开发人员，并且您的图表马上就可以工作，那么您可能希望跳到本系列的后面部分。

## 分享这篇文章