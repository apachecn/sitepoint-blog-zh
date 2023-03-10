# 用 Laravel 和 Recurly 创建基于订阅的网站，第 1 部分

> 原文：<https://www.sitepoint.com/creating-subscription-based-website-laravel-recurly-1/>

安排和处理支付很容易，但不要认为定期支付是微不足道的。事情会变得非常复杂。你需要决定如何处理失败(一个人有多少次失败的支付尝试？)，账单细节必须保持最新，升级和降级计划会带来各种各样的问题。然后，可能是最重要的问题，经常性付款-你需要保持你的访客的付款细节文件。这带来了安全性、合规性和法律问题。

幸运的是，Recurly 是一项处理大部分支付问题的服务，也是处理经常性支付的服务。使用它的 JavaScript 库，您可以创建安全的表单来提交和更新 Recurly 的账单信息——包括所有重要的信用卡详细信息——它安全地提交信息，独立于您的应用程序。

一旦您设置了付款的金额和频率，该服务会定期安排和接收付款，直到客户决定取消。Recurly 还处理计划变更，计算并进行额外收费或退款。

在这个由两部分组成的系列中，我将一步步向您展示如何使用 Laravel(一个基于 PHP5 的框架)和 Recurly 支付处理服务创建一个基于付费订阅的会员网站。首先，我们将创建一个简单的站点，包括基本的注册、认证、用户角色和权限。然后，我们将添加支付处理，并将其绑定到注册过程中，允许人们购买不同的会员等级。

## 设置应用程序

首先创建一个新的项目文件夹，然后运行以下命令:

```
composer create-project laravel/laravel recurly --prefer-dist
```

这将使用 Composer 创建一个新的 Laravel 项目，Composer 下载框架及其依赖项。

我们还需要一些额外的库，所以在`composer.json`的 require 部分添加以下内容并运行`composer.phar update`。

```
"machuga/authority": "dev-develop",
"machuga/authority-l4" : "dev-master",
"recurly/recurly-client": "2.1.*@dev"
```

这将下载权限库，我们将使用它来定义用户角色和权限，以及 Recurly 客户端库。

接下来，通过在`app/config/database.php`中指定适当的模式名、用户名、密码和主机名/地址来配置数据库连接。Laravel 可以与许多数据库配合使用，例如 MySQL、Postgres、SQLite 和 SQL Server。

我们还需要一个用户表。Laravel 提供了迁移，一种以编程方式创建和修改数据库表的方法，而不是手工设置。运行以下命令:

```
php artisan migrate:make create_users_table
```

这将在`app/database/migrations`文件夹中创建迁移文件。文件是用一个基本的大纲创建的，但是让我们用我们的表定义来充实它:

```
public function up()
{
    Schema::create('users', function($table) {
        $table->increments('id');
        $table->string('email')->unique();
        $table->string('name');
        $table->string('password');
        $table->timestamps();
    });
}

public function down()
{
    Schema::drop('users');
}
```

回到命令行并运行:

```
php artisan migrate
```

查看数据库，您应该会发现 Laravel 已经为您创建了 users 表。

## 为角色和权限设置权限

为了确定用户拥有的帐户类型以及授予他们的权限，我们将为用户分配一个角色。我们已经通过 Composer 下载了 Authority 来帮助解决这个问题；我们只需要再执行一些步骤来完全配置它。

在`app/config/app.php`中，向提供者添加以下行:

```
'Authority\AuthorityL4\AuthorityL4ServiceProvider',
```

将以下内容添加到别名中:

```
'Authority' => 'Authority\AuthorityL4\Facades\Authority',
```

并发布权限配置文件:

```
php artisan config:publish machuga/authority-l4
```

我们稍后将返回到配置文件。现在，我们需要创建一些额外的数据库表。幸运的是，这个包包含了它自己的迁移。使用以下命令运行它们:

```
php artisan migrate --package="machuga/authority-l4"
```

您会发现有三个额外的表:`permissions`、`roles`和`role_user`。

我们还需要创建模型来表示角色和权限。我们暂时保持简单。在`app/models/Role.php`中:

```
<?php
class Role extends Eloquent
{
}
```

而在 app/models/Permission.php 中:

```
<?php
class Permission extends Eloquent 
{
}
```

现在我们需要修改已经创建的`User`模型类，将用户与角色和权限关联起来。将以下几行添加到`app/models/User.php`:

```
public function roles() {
    return $this->belongsToMany('Role');
}

public function permissions() {
    return $this->hasMany('Permission');
}

public function hasRole($key) {
    foreach($this->roles as $role){
        if ($role->name === $key) {
            return true;
        }
    }
    return false;
}
```

现在让我们用一些数据预先填充数据库。打开`app/database/seeds/DatabaseSeeder.php`并粘贴以下内容:

```
<?php
class DatabaseSeeder extends Seeder
{
    public function run() {
        Eloquent::unguard();
        $this->call('UserTableSeeder');
        $this->command->info('User table seeded!');
        $this->call('RoleTableSeeder');
        $this->command->info('Role table seeded!');
    }
}

class UserTableSeeder extends Seeder
{
    public function run() {
        DB::table('users')->delete();
        User::create(array(
            'email'    => 'joe.bloggs@example.com',
            'name'     => 'Joe Bloggs',
            'password' => Hash::make('password')
        ));
    }

}

class RoleTableSeeder extends Seeder
{
    public function run() {
        DB::table('roles')->delete();
        Role::create(array('name' => 'admin'));
        Role::create(array('name' => 'pending'));
        Role::create(array('name' => 'member'));
        Role::create(array('name' => 'bronze'));
        Role::create(array('name' => 'silver'));
        Role::create(array('name' => 'gold'));
    }
}
```

然后通过运行以下命令为数据库设定种子:

```
php artisan db:seed
```

## 创建布局

现在我们将创建整体页面布局。下载 Twitter Bootstrap 并将源文件放在`public`文件夹中——将`js`文件移动到`public/js/libs`。

创建包含以下内容的文件`app/views/layouts/default.blade.php`:

```
<!DOCTYPE html>
<html lang="en">
 <head>
  <meta charset="utf-8">
  <title>Subscription Site Tutorial</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="">
  <meta name="author" content="">

  <!-- Le styles -->
  <link href="/css/bootstrap.css" rel="stylesheet">  
  <link href="/css/style.css" rel="stylesheet"> 
 </head>

 <body>
  <div class="navbar navbar-inverse navbar-fixed-top">
   <div class="navbar-inner">
    <div class="container">
     <button type="button" class="btn btn-navbar" data-toggle="collapse" data-target=".nav-collapse">
      <span class="icon-bar"></span>
      <span class="icon-bar"></span>
      <span class="icon-bar"></span>
     </button>
     <a class="brand" href="#">Subscription Site Tutorial</a>
     <div class="nav-collapse collapse">
      <ul class="nav">
       <li class="active"><a href="#">Home</a></li>
       <li><a href="#about">About</a></li>
       <li><a href="#contact">Contact</a></li>
      </ul>
     </div><!--/.nav-collapse -->
    </div>
   </div>
  </div>

  <div class="container">

    @if(Session::has('success'))
    <div class="alert alert-success">
      <button type="button" class="close" data-dismiss="alert">&times;</button>
      {{ Session::get('success') }}
    </div>    
    @endif

    @if(Session::has('error'))
    <div class="alert alert-error">
      <button type="button" class="close" data-dismiss="alert">&times;</button>
      {{ Session::get('error') }}
    </div>    
    @endif

    @yield('content')

  </div> <!-- /container -->

  <!-- Le javascript
  ================================================== -->
  <!-- Placed at the end of the document so the pages load faster -->
  <script src="https://code.jquery.com/jquery-1.10.2.min.js"></script>
  <script src="/js/libs/bootstrap.min.js"></script>
 </body>
</html>
```

这是非常基本的东西——我们的页面内容在显示`@yield('content')`的行中输出。

接下来，创建文件`public/css/style.css`:

```
body {
    padding-top: 50px;
}

p.logged-in {
    color: white;
    margin-top: 0.5em;
}
```

最后，让我们为应用程序创建一个基本的主页。创建文件`app/views/home/index.blade.php`:

```
@extends('layouts.default')
@section('content')
  <h1>Subscription Site Tutorial</h1>
@stop
```

`@extends`声明告诉 Laravel 使用我们刚刚创建的默认布局，通过包装在`@section`声明中的`@yield`命令将内容注入内容区域。

不要忘记在`app/routes.php`中相应地更改默认路线:

```
Route::get('/', function() {
	return View::make('home/index');
});
```

## 建立登录机制

我们已经有了我们的用户和一个帐户，所以现在我们需要登录的能力。在`app/routes.php`中，添加一条路线到登录页面:

```
Route::get('/auth/login', function() {
    return View::make('auth/login');
});
```

现在创建视图，`app/views/auth/login.blade.php`。`.blade.php`扩展表明我们将使用 Laravel 附带的刀片模板库，它比普通 PHP 更干净。在后台，这些模板文件根据需要被编译成 PHP。

```
@extends('layouts.default')
@section('content')
  <h1>Please Log in</h1>
  {{ Form::open(array('url' => 'auth/login')) }}
  {{ Form::label('email', 'E-Mail Address') }}
  {{ Form::text('email') }}
  {{ Form::label('password', 'Password') }}
  {{ Form::password('password') }}
  <div class="form-actions">
  {{ Form::submit('Login', array('class' => 'btn btn-primary')) }}
  </div>
  {{ Form::close() }}
  <p>Not a member?  <a href="/user/register">Register here</a>.</p>
@stop
```

如果您在浏览器中浏览到 */auth/login* ，您应该会看到一个简单的登录表单。

为了处理登录，我们需要建立一个 POST 路由。Laravel 中的认证轻而易举；我们只是这样做:

```
Route::post('/auth/login', function() {
    $email = Input::get('email');
    $password = Input::get('password');

    if (Auth::attempt(array('email' => $email, 'password' => $password))) {
        return Redirect::to('/')->with('success', 'You have been logged in');
    }
    else {
        return Redirect::to('auth/login')->with('error', 'Login Failed');
    }

    return View::make('auth/login');
});
```

所有的神奇都发生在`Auth::attempt()`；如果登录成功，就会创建一个会话，并且可以通过静态方法`Auth::user()`访问`User`对象的填充实例。

注销方法同样简单:

```
Route::get('/auth/logout', function() {
    Auth::logout();
    return Redirect::to('/')->with('success', 'You have successfully logged out');
});
```

## 基本注册

我们在这一部分的最后一个任务是建立一个基本的注册过程。在`app/routes.php`中创建注册路线:

```
Route::get('/user/register', function() {
    return View::make('user/register/index');
});
```

现在创建视图`app/views/user/register/index.blade.php`:

```
@extends('layouts.default')
@section('content')
  {{ Form::open(array('url' => 'user/register')) }}

  {{ Form::label('email', 'E-Mail Address') }}
  {{ Form::text('email') }}
  {{ $errors->first('email') }}

  {{ Form::label('name', 'Your name') }}
  {{ Form::text('name') }}
  {{ $errors->first('name') }}

  {{ Form::label('password', 'Password') }}
  {{ Form::password('password') }}
  {{ $errors->first('password') }}

  {{ Form::label('password_confirmation', 'Repeat') }}
  {{ Form::password('password_confirmation') }}

  <div class="form-actions">
  {{ Form::submit('Register', array('class' => 'btn btn-primary')) }}
  </div>

  {{ Form::close() }}
@stop
```

这里有几点需要注意:

*   `$errors`对象被自动传递给视图，不管提交中是否有错误。如果视野没问题，就什么都不会打印出来。
*   通过将`_confirmation`追加到一个字段的名称，很容易将两个字段一起验证；即确认用户已经正确地重新输入了选择的密码。

现在让我们实现 POST 操作:

```
Route::post('/user/register', function() {
    $validator = Validator::make(
        Input::all(),
        array(
            'name' => array('required', 'min:5'),
            'email' => array('required', 'email', 'unique:users'),
            'password' => array('required', 'confirmed')
        )
    );

    if ($validator->passes()) {
        $user = new User();
        $user->name     = Input::get('name');
        $user->email    = Input::get('email');
        $user->password = Hash::make(Input::get('password'));
        $user->save();

        $role_pending = Role::where('name', '=', 'pending')->first();
        $user->roles()->attach($role_pending);

        Auth::login($user);
        return Redirect::to('/')->with(
            'success',
            'Welcome to the site, . Auth::user()->name . '!'
        );
    }
    else {
        return Redirect::to('user/register')->with(
            'error',
            'Please correct the following errors:'
        )->withErrors($validator);
    }
});
```

这都是相当基本的东西——我们创建一个验证器，传递给它带有`Input::all()`的 POST 变量和一组验证规则。如果验证通过，我们创建一个新用户，将他们分配到待定角色，让他们登录，然后将他们重定向到首页。

如果验证失败，我们重定向回表单，创建一个错误 flash 消息，并传递来自验证器的错误消息——它们将在视图中的`$errors`变量中可用。

## 结论

在这一部分中，我们一步一步地构建了订阅站点的基本框架。它能够注册一个基本帐户，登录和注销。在下一部分中，我们将递归地集成付费订阅计划。

## 分享这篇文章