# Artisan 和 Laravel 迁移

> 原文：<https://www.sitepoint.com/laravel-migrations/>

对于那些努力维护数据库模式的人，或者那些在应用更新和经常恢复更新时遇到问题的人，有一个解决方案。Laravel，我以前写过的 MVC 框架，为您提供了迁移。

简而言之，迁移是包含带有`up()`和`down()`方法的类定义的文件。当执行迁移以将更改应用到数据库时，运行`up()`方法。运行`down()`方法来恢复更改。如果您需要更新数据库，您只需创建一个新的迁移，瞧。不满意吗？恢复它，你回到正轨。

这看起来很酷，不是吗？但是对于拉弗尔来说，它是如何工作的呢？首先您必须配置您的数据库连接，然后您使用 Artisan，Laravel 的命令行界面，来安装迁移表并运行、恢复、创建…迁移。

打开控制台，导航到 Laravel 安装的根目录。运行以下命令:

```
php artisan migrate:install
```

该命令使 Artisan 在数据库中创建一个特殊的表，以跟踪已经执行的迁移。

要创建新迁移，请运行以下命令:

```
php artisan migrate:make create_users_table
```

这将创建处理用户表的迁移文件。您可以在`application/migrations`文件夹中找到您的迁移文件。Artisan 将命令执行的日期和时间作为前缀添加到文件中，因此该文件将被命名为类似“2012 _ 07 _ 25 _ 071925 _ create _ users _ table . PHP”的名称。确保使用一个描述性的名称，这样一看就清楚迁移做了什么。

打开文件，您会看到一个包含两个方法`up()`和`down()`的类。

```
<?php
class Create_Users_Table
{
public function up() {
Schema::create("users", function($table) {
$table->increments("id");
$table->string("username", 32);
$table->string("email", 320);
$table->string("password", 64);
$table->timestamps();
});
}

public function down() {
Schema::drop("users");
}
}
```

`up()`方法在执行迁移时运行，并创建包含五列的 users 表。第一个是自动递增的 ID 列，后面是用于用户名、电子邮件和密码的 VARCHAR 列。`string()`的第一个参数是列的名称(例如“username”)，第二个参数是列的大小(例如 32 个字符)。最后的列是由`timestamps()`方法创建的，该方法创建“创建时间”和“更新时间”列。

`down()`方法比它的前身简单，只是告诉数据库删除 users 表。

如果这个文件只是放在那里，它什么也做不了。要执行所有未完成的迁移，请运行:

```
php artisan migrate
```

目前不可能运行特定的迁移。但是，您可以通过以下命令运行应用程序文件夹中的所有迁移:

```
php artisan migrate application
```

您可以使用相同的命令对特定的包进行同样的操作，但是要使用包的名称。

现在，假设您意识到您犯了一个设计错误，并且您想要回滚上一次迁移运行。只需键入:

```
php artisan migrate:rollback
```

现在还不可能自动回滚到特定迁移之前的某个点，所以您必须重复运行该命令，直到到达该迁移。但是，您可以通过运行以下命令来重置您曾经运行过的所有迁移:

```
php artisan migrate:reset
```

目前，该数据库将允许用户注册相同的用户名或电子邮件地址任意次。我们希望对此加以限制，只允许一个地址或用户名使用一次。当然，我们不希望回滚到之前的迁移，因为如果回滚，我们会丢失数据，所以我们创建了一个新的迁移。

```
php artisan migrate:make users_add_username_email
```

`up()`方法应该在字段上添加一个惟一的索引，如下所示:

```
<?php
public function up() {
Schema::table("users", function($table) {
$table->unique("username");
$table->unique("email");
});
}

In the <code>down()</code> method, you want simply to remove the indexes.

<?php
public function up() {
$table->drop_unique("username");
$table->drop_unique("email");
}
```

就是这样！我希望您发现 Laravel 的迁移是一个解决方案，可以避免您在管理数据库模式时所经历的所有时间。下次见！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章