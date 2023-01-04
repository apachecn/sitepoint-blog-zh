# 具有梯形迁移的数据库版本控制

> 原文：<https://www.sitepoint.com/database-versioning-ladder-migrations/>

版本控制系统对于跟踪代码中的变化是非常宝贵的，尤其是当你在团队中工作的时候。然而，大多数应用程序并不仅仅由应用程序代码组成。管理对数据库的更改总是有点困难，特别是当您添加需要更改模式的新特性时。

假设您正在处理一个模块，并意识到其中一个数据库表需要一个额外的列。您可能想打开数据库查询工具或命令行，然后简单地添加列。然而，这不会像版本控制的应用程序代码那样留下更改记录。当在团队中工作时，这种情况变得更加严重——如果一个同事在没有运行相同的数据库更新的情况下就对您的代码进行了更改，那么他们的应用程序版本完全有可能会崩溃。当你发布一个产品更新时，这个问题变得更加严重，因为它不仅会破坏你的开发伙伴的应用程序，也会破坏你的用户。

一种解决方案是使用迁移将创建和修改数据库模式的责任转移到代码中。这样，更改可以与应用程序的其余部分一起管理，我们在版本控制中视为理所当然的功能(如比较版本和保留审计跟踪)可以用于数据库更改。它还允许将这些变更无缝地合并到发布中，因为它们可以成为同一个分支或标签的一部分。

许多主要框架都有自己的迁移实现，但是对于那些没有的框架——如果你没有使用任何框架——有一个阶梯。

# 介绍梯子

Ladder 是一个创建、运行和管理数据库迁移的工具。迁移只是一个 PHP 类，因此可以和应用程序代码的其余部分一起签入版本控制。

您可以在一次迁移中对模式进行任意多的修改，尽管最好将迁移限制在一个表或一个特性中。

迁移总是按顺序运行。因此，假设您编写一个程序来创建一个`products`表，几周后创建一个新的迁移来添加一个额外的列。试图在前者之前运行后者将会产生错误。Ladder 通过对迁移进行顺序编号，以及在数据库本身中存储已经运行的迁移记录(以及运行时间)来解决这个问题。

# 装置

梯子可以通过 Composer 从 Bitbucket 或[下载](https://packagist.org/packages/drarok/ladder1)[。然而，它需要一些步骤来启动和运行。](https://bitbucket.org/drarok/ladder)

最简单的——尽管公认是最不优雅的——安装方法是下载或克隆它，并把它放在项目根目录下的一个名为`ladder`的目录中。使用 Composer 的问题是运行`composer update`会覆盖你的配置文件。

有五个配置文件，每个文件都需要手工创建；最简单的方法是复制提供的示例:

```
cp ladder/config/config.php.example ladder/config/config.php
cp ladder/config/database.php.example ladder/config/database.php
cp ladder/config/diff.php.example ladder/config/diff.php
cp ladder/config/editor.php.example ladder/config/editor.php
cp ladder/config/table.php.example ladder/config/table.php 
```

您可能只需要修改包含数据库连接细节的`database.php`和`editor.php`，您可以在其中指定您喜欢的文本编辑器，并选择在创建时自动打开新的迁移。

# 创建迁移

让我们首先创建一个迁移来创建`users`表。

在命令行上:

```
php ladder/ladder.php create create_users_table 
```

这将创建一个名为`ladder/migrations/00001_create_users_table`的文件，默认情况下，该文件包含一个基本结构，其中一些示例调用被注释掉了。如果您已经在`ladder/config/editor.php`中将`auto-edit`设置为`true`，该文件将立即在指定的文本编辑器中打开。您可以随意命名该迁移，但是能够从文件名中一目了然地看到迁移做了什么是很有帮助的。

下面是该文件可能的样子(为了清楚起见，我已经删除了被注释掉的行):

```
class Create_Users_Table_Migration_00001 extends Migration {
    protected $min_version = '0.8.1';

    public function up() {
        $this->create_table('users')
            ->column('email', 'varchar', array('limit' => 128, 'null' => FALSE))
            ->column('password', 'varchar', array('limit' => 32, 'null' => FALSE));
    }

    public function down() {
        $this->table('users')->drop();
    }   
} 
```

运行迁移时会调用`up()`方法，回滚时会调用`down()`方法——因此它总是需要执行与`up()`方法相反的操作。在这个例子中，`up()`方法创建了一个名为`users`的表，`down()`方法删除了它。

`create_table()`方法返回对新表的引用，而`Table`类有一个`column()`方法来添加新列。它有一个流畅的界面，所以你可以将它们链接在一起，同时创建多个列。您会注意到没有 ID 列——这是自动创建的——即一个自动递增的、基于整数的主键，名为`id`。

> `table()`返回一个对表的引用，但是如果它不存在的话，它也会创建一个——因此，你可以安全地将`create_table()`调用改为`table()`

让我们创建另一个迁移，这次是为了创建`roles`表:

```
php ladder/ladder.php create create_roles_table 
```

文件本身:

```
class Create_Roles_Table_Migration_00002 extends Migration {
    protected $min_version = '0.8.1';

    public function up() {
        $this->table('roles')
            ->column('nme', 'varchar', array('limit' => 128, 'null' => FALSE));            
    }

    public function down() {
        $this->table('roles')->drop();
    }   
} 
```

现在，您需要实际运行迁移。为此:

```
php ladder/ladder.php migrate 
```

如果您查看数据库，您会发现四个表:

`migrations`是自动创建的，用于跟踪已经运行的迁移
`migrations_kvdata`也是为您创建的，可以用于您的迁移的任意键/值存储
`users`和`roles`是我们刚刚添加的表。

但是，如果您密切关注角色迁移，您会注意到它创建了一个名为`nme`而不是`name`的列。此时，我们可以通过“撤销”迁移，修改类，然后再次运行它来解决这个问题。要回滚迁移:

```
php ladder/ladder.php remove 2 
```

数字 2 表示要回滚到哪个迁移，它是迁移文件名的前缀，没有前导零。

现在，您只需进行更正，然后再次运行迁移:

```
php ladder/ladder.php migrate 
```

因为`migrations`表存储了已经运行和没有运行的记录，所以您不需要担心您的第一次迁移会重新运行。

还有一种更快的方法，用`reapply`代替:

```
php ladder/ladder.php reapply 2 
```

这将调用第二次迁移的`down()`方法，然后是它的`up()`方法，一次完成。

现在让我们假设我们开发了一个新特性，它要求`users`表包含一个状态字段。为此，我们需要创建一个新的迁移:

```
php ladder/ladder.php create add_status_to_users_table 
```

迁移将如下所示:

```
class Add_Status_To_Users_Table_Migration_00003 extends Migration {
    protected $min_version = '0.8.1';

    public function up() {
        $this->table('users')
            ->column('status', 'integer', array('null' => FALSE, 'default' => 0));            
    }

    public function down() {
        $this->table('users')->drop_column('status');      
    }
} 
```

像以前一样，运行:

```
php ladder/ladder.php migrate 
```

现在，如果您检查您的数据库，您会看到`users`表有一个新列。

## 数据库播种

除了编写迁移来管理数据库模式之外，您还可以使用 Ladder 来预填充(即播种)数据库。

例如，您可以扩展`create_users_table`迁移来创建一个默认的根用户帐户:

```
public function up() {
    // as before
    $root_user_id = $this->table('users')
        ->insert(array(
                'email' => 'root@example.com',
                'password' => NULL,
            ))
            ->insert_id;

    $this->set('root_user_id', $root_user_id);
}

public function down() {
    // as before
    if ((bool) $user_id = $this->get('root_user_id')) {
        $this->table('users')->delete(array(
            'id' => (int) $user_id,
        ));
    }
} 
```

请注意，我们使用了`set()`和`get()`方法来利用梯形的键/值存储，存储用户 ID，以便我们以后可以引用它。

您还可以在迁移中从 CSV 文件导入数据，例如:

```
$this->table('users')->import_csv('/path/to/users.csv'); 
```

要更新而不是插入 CSV 数据，您应该这样做:

```
$this->table('users')->import_csv('/path/to/users.csv', TRUE, array('email')); 
```

在本例中，send 参数表示我们想要运行一个更新，第三个参数包含一个关键字段列表，用于确定要更新哪些记录。

### 添加其他数据库

您会注意到默认的数据库配置文件`ladder/config/database.php`演示了如何添加额外的数据库连接；也许是为了暂存或实时数据库。例如:

```
$config['default'] = array(
    'hostname'  => 'localhost',
    // etc
);

$config['staging'] = array(
    'hostname'  => 'server2.myreal.host.name',
    // etc
);

$config['live'] = array(
    'hostname'  => 'server2.myreal.host.name',
    // etc
); 
```

您可以指定运行梯形时要使用的连接，如下所示:

```
php ladder/ladder.php --config=live 
```

## 附加命令

### 状态

您可以使用`status`命令获得数据库的状态:

```
php ladder/ladder.php status 
```

样本输出:

```
Migration Status
Latest migration is 3
Connecting to localhost:3306... Connected. Server version 5.1.62.
<   ladder: 1
Completed in 0.285s 
```

### 差异和差异保存

您可以使用`diff-save`命令将数据库的状态转储到一个文件:

```
php ladder/ladder.php diff-save 
```

这将创建一个包含数据库当前状态的文件，作为`ladder/cache/ladder`中的序列化数组。现在，您可以手动对模式进行更改，然后使用`diff`命令将数据库恢复到本地存储的状态:

```
php ladder/ladder.php diff 
```

### 版本

最后，您可以使用以下命令获得您正在使用的梯形版本:

```
php ladder/ladder.php version 
```

## 摘要

在本文中，我介绍了用于维护数据库模式和预填充数据的梯形。为了进一步探索，看看[的比特桶页面](https://bitbucket.org/drarok/ladder)和[的网站](http://drarok.com/ladder/)。

## 分享这篇文章