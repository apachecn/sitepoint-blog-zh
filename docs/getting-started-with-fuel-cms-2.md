# Fuel CMS 入门，第 2 部分

> 原文：<https://www.sitepoint.com/getting-started-with-fuel-cms-2/>

在之前的文章中，您了解了 Fuel CMS 的基础知识:如何创建视图和简单页面。然而，如今大多数网站并不那么简单，它们有博客、画廊等。在这一部分中，我将解释 Fuel CMS 模块是如何工作的，并创建一个基本的留言簿模块来演示。

## 什么是模块？

Fuel CMS 的大部分功能都打包到模块中。燃料 CMS 模块包含至少一个带有附属数据库表的模型，并可以用控制器和视图来扩展。

在 MVC 模式中，模型是定义不同数据字段的类，用于处理存储在表中的数据。有关 CodeIgniter(编写 Fuel CMS 的框架)中模型的更多信息，请参见[框架的文档](http://ellislab.com/codeigniter/user-guide/general/models.html "Models: CodeIgniter User Guide")。

在默认的 Fuel CMS 安装中，默认情况下包括标准模块。例如，博客系统由三个模块组成:文章、类别和作者。每个模块在数据库中都有自己的表。在这篇文章中，我们将创建自己的留言簿模块。每个人都可以发表新的评论，管理员可以通过管理菜单对它们进行审核。

## 编写模型

从创建目录`modules/guestbook`开始。从现在开始，我将把这个目录称为“留言簿文件夹”。

要注册模块，打开`application/config/MY_fuel_modules.php`并将以下行添加到:

```
<?php
$config['modules']['guestbook'] = array(
'model_name' => 'guestbookcomments_model',
'model_location' => 'guestbook'
);
```

我们已经用一个模型文件(我们将很快创建)的名称设置了 *model_name* 键，用新目录的名称设置了 *model_location* 键。Fuel CMS 现在会在模块/留言簿/模型中查找模型。(关于[参数](http://www.getfuelcms.com/user_guide/modules/simple "FUEL CMS User Guide : Version 0.9.3 : Simple Modules")的完整列表，请参见 Fuel CMS 的用户指南。)

接下来，使用以下 SQL 在数据库中创建 guestbook 表:

```
CREATE TABLE IF NOT EXISTS guestbook_comments (
id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
author VARCHAR(25) NULL NULL,
comment VARCHAR(100) NOT NULL,
`date` DATETIME NOT NULL,
visible ENUM('yes', 'no') NOT NULL DEFAULT 'yes',

PRIMARY KEY (id)
);
```

根据名称，每个栏的目的应该是清楚的；该表存储了留言簿评论，包括评论作者的姓名、日期以及条目是否可见(`visible`列是 Fuel CMS 特有的)。

接下来让我们把注意力转向创建模型类，它充当 PHP 和数据库之间的一种翻译器。转到留言簿文件夹，在其中创建一个名为`models`的新目录。然后在其中添加名为`guestbookcomments_model.php`的模型文件:

```
<?php
if (!defined('BASEPATH')) exit('No direct script access allowed');
require_once(FUEL_PATH . 'models/base_module_model.php');

class GuestbookComments_model extends Base_module_model
{
public function __construct()
{
parent::__construct('guestbook_comments');
}
}

class GuestbookComment_model extends Base_module_record
{
}
```

这只是我们模块的基础。`GuestbookComments_model`扩展了`Base_module_model`类，它将自动处理我们的列表和管理交互。传递给父构造函数的字符串是这个模型将使用的数据库表的名称。`GuestbookComment_model`扩展了处理单个记录的`Base_module_record`。

如果你愿意，你可以测试一下，看看它是否工作，登录到你的仪表板，从模块子菜单中选择“留言簿”。通常会有一个评论列表，但由于我们还没有任何数据，列表是空的，所以单击创建按钮添加一个。

![](img/e2feedfbabb548ccbbc98be2811bb22b.png "fuelcms-2-01")

当您返回列表时，您应该会看到刚刚添加的评论。随着越来越多的人将在留言簿中留下评论，这里是您可以对它们进行审核并使您的留言簿免受垃圾邮件影响的地方。

## 创建控制器

这一切都很酷，但你显然不想成为唯一能够添加评论的人！我们需要创建一个控制器来处理面向公众的操作。

在留言簿文件夹中创建目录`controllers`,然后在其中创建文件`guestbook.php`,如下所示:

```
<?php
class Guestbook extends CI_Controller
{
function view()
{
echo '<h1>Guestbook</h1>';
$this->load->module_model('guestbook', 'GuestbookComments_model');
$comments = $this->GuestbookComments_model->find_all();
foreach ($comments as $comment) {
echo '<h2>' . $comment->author . '</h2>';
echo $comment->comment;
}
}
}
```

您可以通过使用控制器名称，然后在 URL 中使用操作名称来访问控制器操作。因此，要查看视图，在浏览器中导航到*http://localhost/guest book/view*(或者您的开发地址)。

控制器中使用的大部分代码都是 CodeIgniter，除了`module_model()`方法。第一个参数是模块目录的名称，在我们的例子中是‘guest book ’,第二个参数是扩展了`Base_module_model`的模型类的名称。

在我们注册了模块的模型后，我们使用它的`find_all()`方法并循环显示结果。(最好在控制器中使用视图，而不是显示逻辑，但是为了本文的目的，我希望事情简单一些。)

如果没有添加新评论的选项，留言簿就没什么用了，不是吗？向控制器添加新方法:

```
<?php
public function add()
{
$this->load->library(
'form_builder',
array(
'id'=>'addComment',
'form_attrs' => array(
'method' => 'post',
'action' => 'newcomment'
),
'submit_value' => 'Add new comment',
'textarea_rows' => '5',
'textarea_cols' => '28'
)
);

$fields = array(
'name' => array(
'label' => 'Full Name',
'required' => true
),
'comment' => array(
'type' => 'text',
'label' => 'Comment',
'required' => true
)
);
$this->form_builder->set_fields($fields);
echo $this->form_builder->render();
}
```

现在，当我们转到*http://localhost/guest book/add*时，我们应该会看到这个表单。

我们使用 Fuel CMS 的表单生成器来给所有东西一个漂亮、干净的外观，避免我们自己编写 HTML。我们首先加载 form_builder 库，然后向它传递一些参数来定义表单的属性和元素。[查看文档](http://www.getfuelcms.com/user_guide/libraries/form_builder "FUEL CMS User Guide : Version 0.9.3 : Form Builder Class")获取完整列表。

表单的动作是“newcomment ”,因此我们需要在控制器中创建一个新方法来处理表单提交:

```
<?php
public function newcomment()
{
$this->load->module_model('guestbook', 'GuestbookComments_model');
$comment = $this->GuestbookComments_model->create();
$comment->author = $this->input->post('name');
$comment->date = date('Y-m-d G:i:s');
$comment->comment = $this->input->post('comment');
$comment->save();

echo 'comment successfully added';
}
```

我们加载留言簿模型，将传入的数据分配给它的成员，然后将其保存到数据库中。最后，发出一条成功消息，表明所有内容都已添加到数据库中。

## 摘要

如您所见，Fuel CMS 不像大型 CMS 平台那样为许多事情提供预打包的解决方案；作为开发人员，很多事情都取决于您。但幸运的是，由于 CodeIgniter 框架和 Fuel CMS 的助手库，这并不难。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章