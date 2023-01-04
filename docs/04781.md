# 重新引入 FuelPHP

> 原文：<https://www.sitepoint.com/re-introducing-fuelphp/>

作为一名 PHP 开发人员，我一直是不同 PHP 框架的忠实用户，主要关注 CakePHP。最近，我觉得有必要去购买框架，并且我有很多合理的理由选择 [FuelPHP](https://fuelphp.com/) 。它有一个内置的模块化结构和完全的灵活性，重点是社区。在 Fuel 之前，我是一个 [CakePHP](https://cakephp.org) 用户，就像 Cake 一样，Fuel 是一个巨大的社区驱动框架。

![FuelPHP](img/d46e2c55d8046ef425dcd17ffc4da807.png)

## 安装燃料泵框架

要安装 FuelPHP，你唯一需要做的就是运行:`curl get.fuelphp.com/oil | sh`并用`oil create project_name`创建你的项目。

将会有可选的命令，比如`oil refine install`，它使得必要的目录可写，最后，执行`composer update`来安装依赖项。

## 什么是 FuelPHP 油？

如果你有 PHP 框架的经验，那么`oil`的概念对你来说会非常清楚。例如，Fuel 的油是 Laravel Artisan 的替代品。事实上，oil 是一个命令行工具，可以帮助快速开发、测试您的应用程序和运行多个任务。这将使您能够通过提供以下几个功能来加快开发速度:

*   生成:创建 MVC 组件、迁移等。
*   Refine:运行诸如 migrate 之类的任务，以及您自己定制的任务。
*   软件包:安装、更新和删除软件包。
*   控制台:使用交互式 shell 实时测试您的代码。
*   测试:运行 PHPUnit 测试。

点击阅读更多关于石油的信息[。](https://fuelphp.com/docs/packages/oil/intro.html)

## FuelPHP 套件

Fuel 有允许你和其他人分享你制作的包的包。它们可以在 [Packagist](https://packagist.org) 上找到，与所有 Composer 软件包一样。

有两种安装软件包的方法。可以手动甩油，也可以用 Composer。我个人更喜欢作曲家的方法。

你可以在这里阅读更多关于套餐[的内容。](https://fuelphp.com/docs/general/packages.html)

## 入门指南

如你所知，Fuel 和大多数其他框架一样使用 MVC 架构。每个框架对于特定的 mvc 部分都有自己的规则。在 Fuel 中，控制器放在`fuel/app/classes/controller`目录中，并以`controller_`为前缀。或者，他们应该扩展控制器类以获得完整的特性集。在 Fuel 中，可以通过一些前缀动作自动路由 HTTP 请求，比如定义方法中的 post 和 get。

让我们从燃料基础开始。

请确保您在`app/config/config.php`中启用了以下两个包:

```
'packages'  => array(
    'orm',
    'auth'
)
```

我们将在下一节中使用它们。默认路线是`hello`，显示你的欢迎页面。如果您想进行一些更改，只需编辑`fuel/app/config/routes.php`:

```
return array(
    '_root_'  => 'welcome/index',  // The default route
    '_404_'   => 'welcome/404',    // The main 404 route
    'hello(/:name)?' => array('welcome/hello', 'name' => 'hello')
);
```

## 向 FuelPHP 问好

现在是向燃料世界问好的好时机。首先通过 [Simpleauth](https://fuelphp.com/docs/packages/auth/simpleauth/intro.html) 的方式创建一个简单的认证 app。Simpleauth 是指包含在 auth 包中的简单身份验证系统。要使用 Simpleauth，请将`fuel/packages/auth/config/auth.php`和`simpleauth.php`复制到`fuel/app/config/`。然后，创建一个数据库表。我使用迁移而不是传统的数据库操作。我将把`core/config/migrations.php`复制到`app/config/migrations.php`，并运行下面的命令来创建一个脚手架:

```
php oil generate scaffold user username:string password:string email:string profile_fields:text  created_at:string updated_at:string last_login:integer[20]
```

这将在我们的`app/migrations`文件夹中创建一个名为`001_create_users.php`的文件，我将它编辑为:

```
namespace Fuel\Migrations;
class 001_create_users
{
    public function up()
    {
     \DBUtil::create_table('users', array(
            'id' => array('constraint' => 11, 'type' => 'int', 'auto_increment' => true),
            'username' => array('constraint' => 255, 'type' => 'varchar'),
            'password' => array('constraint' => 255, 'type' => 'varchar'),
            'email' => array('constraint' => 255, 'type' => 'varchar'),
            'last_login' => array('constraint' => 20, 'type' => 'int'),
            'profile_fields' => array('constraint' => 255, 'type' => 'varchar'),
            'created_at' => array('constraint' => 255, 'type' => 'varchar'),
            'updated_at' => array('constraint' => 255, 'type' => 'varchar')
        ), array('id'));

        $username = "AwesomeAlireza";
        $password = "@awesomeAlireza@";
        $pass_hash = \Auth::instance()->hash_password($password);
        $email = "Alireza@is-awesome.com";
        $users = \Model_User::forge(array(
            'username' => $username,
            'password' => $pass_hash,
            'email' => $email,
            'profile_fields' => '',
            'last_login' => ''
        ));

        if ($users and $users->save())
            \Cli::write("the user has been created");
         else
            \Cli::write("failed to create user");
    }
    public function down()
    {
        \DBUtil::drop_table('users');
    }
}
```

要提交这个，你只需要运行`php oil refine migrate`。

如果看到这个结果`Migrated to latest version: 1.`，说明一切顺利。之后，请在`app/classes/controller/common.php`中创建一个公共控制器:

```
class controller_common extends Controller_Template
{
    public function before()
    {
        parent::before();
        $uri_string = explode('/', Uri::string());
        $this->template->logged_in = false;

        if (count($uri_string)>1 and $uri_string[0] == 'users' and $uri_string[1] == 'login')
            return;
         else 
           {
            if(\Auth::check())
            {
                $user = \Auth::instance()->get_user_id();
                $this->user_id = $user[1];
                $this->template->logged_in = true;
            } 
            else 
                \Response::redirect('/users/login');
           }
    }
}
```

用户控制器位于`app/classes/controller/users.php`:

```
class controller_users extends Controller_Common
{
    public function action_index()
    {
        $data['users'] = Model_User::find('all');
        $this->template->title = "Users";
        $this->template->content = View::forge('users/index', $data);
    }

    public function action_login()
    {
        if (Auth::check()) 
            Response::redirect('/');
        $val = Validation::forge('users');
        $val->add_field('username', 'Your username', 'required|min_length[3]|max_length[20]');
        $val->add_field('password', 'Your password', 'required|min_length[3]|max_length[20]');

        if ($val->run())
        {
            $auth = Auth::instance();
            if ($auth->login($val->validated('username'), $val->validated('password')))
            {
                Session::set_flash('notice', 'FLASH: logged in');
                Response::redirect('users');
            } 
            else 
            {
                $data['username'] = $val->validated('username');
                $data['errors'] = 'Wrong username/password. Try again';
            }
        }
        else 
        {
            if ($_POST)
            {
                $data['username'] = $val->validated('username');
                $data['errors'] = 'Wrong username/password combo. Try again';
            } 
            else 
            {
                $data['errors'] = false;
            }
        }

       $this->template->errors = $data['errors'];
       $this->template->content = View::forge('users/login')->set($data);
    }

    public function action_view($id = null)
    {
        $data['user'] = Model_User::find($id);
        $this->template->title = "User";
        $this->template->content = View::forge('users/view', $data);
    }

    public function action_logout()
    {
        Auth::instance()->logout();
        Response::redirect('/');
    }
}
```

如你所见，控制器扩展了`Controller_Common`以便被登录限制。我还在控制器中验证了我的输入数据，但是它也可能在我们的模型中。

我们已经完成了控制器，现在是时候为我们的应用程序创建一个视图了。在 Fuel 中，视图文件位于`app/views/CONTROLLERNAME/`下。我们将创建的第一个视图是`app/views/users/login.php`:

```
<h2>Login</h2>

Login to your account using your username and password.

<div class="input required">

    <?php isset($errors) ? $errors : false; ?>

    <?php echo Form::open('users/login'); ?>

    <?php echo Form::label('Username', 'username'); ?>

    <?php echo Form::input('username', null, array('size' => 30)); ?>

</div>

<div class="input password required">

    <?php echo Form::label('Password', 'password'); ?>

    <?php echo Form::password('password', null, array('size' => 30)); ?>

</div>

<div class="submit" >
    <?php echo Form::submit('login', 'Login'); ?>
</div>
```

现在，`index.php`文件:

```
<div><?php echo $user->username; ?></div>
```

你现在唯一需要做的就是在浏览器中导航到`https://127.0.0.1/public/users/login`，你会看到一个类似
![](img/d2bc04467bed758bb399c6263870849d.png)的页面

恭喜你，你已经创建了一个简单的认证应用程序！
该代码也可以在 [Github](https://github.com/sitepoint-examples/FuelPHP) 上获得。

## 结论

如您所见，Fuel 极大地简化了 web 应用程序的构建。每个框架都有自己的优势，但是我希望这篇文章已经向您展示了一些 Fuel 的优势，这样您就可以在您的工具箱中给它它应该得到的机会。

评论？反馈？让我知道！

## 分享这篇文章