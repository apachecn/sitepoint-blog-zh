# 红豆简介

> 原文：<https://www.sitepoint.com/introduction-redbean/>

## 红豆简介

谈到 RAD 和原型开发，有很多工具可以帮助项目快速启动和运行。从快速入门到微框架，从 Composer 等构建工具到一键安装程序，当然还有 ORMs 有各种各样的方法可以加快开发时间。我要看看[红豆](http://www.redbeanphp.com)，一个与众不同的 ORM，非常适合做原型。

## 输入红豆

Redbean 是一个 ORM(对象关系映射器),但它不仅仅如此——它可以动态地创建和修改底层模式。传统上，您会使用 ORM 作为预先计划好的、预先存在的数据库模式的包装器。这对于大多数项目来说没问题，但是当你在做原型的时候——或者边做边做的时候！–让它在幕后为您完成可以加快速度。

当您使用 red bean——或者用特定的术语来说，bean——创建一个对象时，只要将它保存到数据库中，模式就会自动适应。即使你试图保存一个没有对应表格的对象，这也适用！

## 装置

安装红豆最简单的方法是[下载一体化包](http://www.redbeanphp.com/downloadredbean.php)。然后，您只需要要求单个文件，`rb.php`。

也可以通过 Composer 安装；然而，该库的作者并不推荐这种方法——参见[安装页面](http://www.redbeanphp.com/installing)了解详细信息。

## 配药

使用 Redbean 的第一步是“分发”一个 bean——它基本上是一个对象。请注意，在本文的其余部分，我将交替使用术语“bean”和对象。

分发是通过总体红豆类的静态方法“`R`”完成的，该方法将类型作为参数。

例如:

```
$user = R::dispense('user');
```

这将为用户创建一个空对象，然后您可以向该对象分配属性。您也可以通过添加您想要的数字作为第二个参数来同时分配几个:

```
$users = R::dispense('user', 10);
```

在这个阶段，数据库中是否有保存它的表并不重要；一旦我们试着储存它，红豆会处理的。

让我们试试这个——注意静态方法`store`的使用，它用于写入数据库:

```
$user->name = 'Joe Bloggs';
$user->email = 'joe.bloggs@example.com';
$id = R::store($user);
print $id;
```

在大多数 ORM 中，如果您还没有创建一个表来保存用户，那么您会认为这是失败的。但是，使用 Redbean 会成功，因为它会为您创建表。如果您在此阶段查看数据库，您应该会看到如下表格:

```
user
----

id      int(11) UN PK AI
name    varchar(255)
email   varchar(255)
```

出于需要，命名约定非常严格；它是您指定的类型的一个单数的小写表示。

现在让我们看看模式是如何随着我们的迭代而变化的。用户记录可能需要一个密码字段，但是我们忘记创建了。

如果您这样做:

```
$user->password = 'secret';
R::store($user);
```

这一次，Redbean 知道如何存储用户，但是有一个数据库表无法容纳的新字段。没关系——它只是添加了一个，您的表现在将如下所示:

```
user
----

id          int(11) UN PK AI
name        varchar(255)
email       varchar(255)
password    varchar(255)
```

## 字段数据类型

Redbean 试图根据您提供的信息猜测字段的数据类型。所以如果你这样做:

```
$user->age = 30;
R::store($user);
```

您会发现年龄字段被创建为一个微小的整数。

如果后来证明一个字段类型不够用，Redbean 只需动态地修改它。如果您现在尝试将年龄指定为 3000，该列将被更改为一个普通整数。如果您将名称拼写为“thirty ”,它将被更改为 varchar。将 varchar 字段设置为大于 255 个字符，它就变成了文本字段，依此类推。

## 寻找豆子

您可以使用`load`通过主键加载特定对象:

```
$user = R::load('user', 1);
```

您可以使用`batch`方法一次加载几个对象(通过主键):

```
$users = R::batch('user', array(1, 2, 3));
```

这将返回一个 beans 数组。

您还可以使用 SQL 查找 beans。`find`方法的第二个参数本质上是从`WHERE`子句开始的 SQL，不包括`WHERE`关键字本身。例如，要查找尚未满 20 岁的用户:

```
$users = R::find('user', 'age < ?',
    array(20)
);
```

注意，我们绑定了参数，因此数组作为第三个参数。这将使用 beanss 的 ID 作为键返回一个 bean 数组。

您可以向 SQL 中添加更多子句，例如:

```
$users = R::find('user', 'age < ? ORDER BY age ASC',
    array(20)
);
```

## 包括…在内

您可以使用 count 方法找出记录的数量:

```
$number_of_users = R::count('user');
```

## 删除

要删除单个 bean，请使用`trash`:

```
R::trash($user);
```

要移除更多特定类型，使用`trashAll`:

```
R::trashAll('user');
```

要删除特定类型的所有 beans，请使用`wipe`:

```
R::wipe('user');
```

或者，清除所有东西——这在制作原型时很有用——您需要:

```
R::nuke();
```

## 关系

与任何 ORM 一样，支持对象之间的关系。

一对多关系是指使用“拥有”相关对象的概念。例如，如果订单只有一个用户，则该用户被称为“拥有”这些订单。通过使用特定的变量名，我们可以建立这样的关系:

```
$orders = R::dispense('order', 2);
$orders[0]->order_id = '000001';
R::store($orders[0]);
$orders[1]->order_id = '000002';
R::store($orders[0]);

$user->ownOrders = $orders;
R::store($user);
```

这里的关键元素是属性`ownOrders`。如果您现在检查您的数据库，您应该会发现 Redbean 已经将字段`user_id`添加到了 order 表中，同时还添加了一个相应的外键。

“拥有”订单的用户可以简单地作为属性访问，例如:

```
$user = $order->user;
print 'This order belongs to ' . $user->name;
```

为了演示多对多关系，让我们创建一些角色:

```
$roles = R::dispense('role', 3);

$roles[0]->name = 'member';  
R::store($roles[0]);
$roles[1]->name = 'admin';  
R::store($roles[1]);
$roles[2]->name = 'super_admin';
R::store($roles[2]);
```

角色不仅仅属于个人用户；它们是共享的。因此，要将前两个角色分配给用户，并在数据库中建立关系，我们可以这样做:

```
$roles = R::batch('user', array(1,2));
$user->sharedRole = $roles;
R::store($user);
```

这一次，您应该找到一个名为`role_user`的新表，它将这种关系定义为多对多。

您可以通过读取属性来获取属于用户的角色:

```
$roles = $user->sharedRole;
```

这使得角色在第一次被访问时被延迟加载。

关系还有很多，包括使用链接向关系添加属性的能力、通过链接过滤、急切加载等等——查看[文档](http://www.redbeanphp.com/)了解详细信息。

## 模型

只需遵循某些命名约定，就可以创建与 bean 类型相对应的模型。然后使用 FUSE 将模型连接到 beans 在中，通过遵循相关的命名约定，它们被融合在一起。

约定很简单；用下划线分隔“型号”和类型，例如`Model_Type`。例如，要为用户创建一个模型，您只需这样做:

```
class Model_User extends RedBean_SimpleModel { }
```

一旦您定义了一个模型，您就可以实现许多方法，这些方法将在 bean 生命周期的不同点被调用。下表说明了这一点，它将 CRUD 操作映射到您可以实现的“挂钩”:

```
R::store        $model->update()
R::store        $model->after_update()
R::load            $model->open()
R::trash        $model->delete()
R::trash        $model->after_delete()
R::dispense        $model->dispense()
```

例如，您可以通过实现`update()`来添加验证:

```
class Model_User extends RedBean_SimpleModel { 
  public function update() {
    if (strlen($this->password) < 8) {
      throw new Exception('Password must be at least 8 characters long');
    }
  }
}
```

当然，您也可以创建自己的方法，以及自定义的 GETters。

## 查询数据库

您可以像这样执行原始 SQL 查询:

```
R::exec('UPDATE user SET status = 1');
```

您可以像这样返回多维行数组:

```
R::getAll('SELECT * FROM user');
```

此外，您可以使用参数绑定:

```
R::getAll('SELECT * FROM user WHERE status = :status',
    array(':status' => 1)
);
```

您可以像这样获取单个列:

```
R::getCol('SELECT email FROM user');
```

您还可以使用表中的两列获得关联数组，如下所示:

```
R::$adapter->getAssoc('SELECT id, email FROM user');
```

## 部署应用程序

尽管可以说它最适合于原型开发——或者至少是开发——但是如果您遵循某些步骤，没有理由不能将红豆用于生产。

Redbean 动态更新模式的机制称为“流体模式”；然而，这并不适合生产，而且有相当大的性能开销。但是，您可以通过“冻结”它来关闭它:

```
R::freeze( true );
```

在进入生产阶段时，您需要遵循以下几个步骤:

*   查看列数据类型
*   查看外键和关系
*   检查索引——红豆创建了一些——并进行适当的修改

## 摘要

在本文中，我介绍了 Redbean，这是启动开发过程的一种便捷方式，它允许您摆弄域模型，而不必担心首先构建底层模式。它对于生产是否可行还有待观察——但是对于原型和快速迭代来说，它是一个很棒的小工具。还有很多我没有涉及到，所以请务必[查看文档](http://www.redbeanphp.com)。

## 分享这篇文章