# 使用 Sentinel 消除用户授权的痛苦

> 原文：<https://www.sitepoint.com/removing-the-pain-of-user-authorization-with-sentinel/>

大多数非基本的多用户应用程序都需要一些角色和权限级别。如果你曾经使用过 WordPress，你一定注意到他们有一个超级管理员，管理员，编辑，作者等等。简化许可系统的开发和集成是 Cartalyst 的 Sentinel 包试图实现的目标。这个包提供了一个 API 来处理用户、组、权限等。在本文中，我们将使用它来创建一个小的演示应用程序。

![Cartalyst Sentinel](img/3a6e8fecbed154ef436f1933b08f62ce.png)

## 环境设置

对于本教程中的示例应用程序，我们将使用 [Slim 微框架](http://slimframework.com/)和[流浪者](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。你可以在 [Github](https://github.com/sitepoint-editors/sentinel_demo) 上查看最终的演示来看看结果。让我们从首先需要必要的包开始:

```
composer require slim/slim:~2.0
composer require twig/twig:~1.*
composer require cartalyst/sentinel:2.0.*
```

Sentinel 建议安装 [Illuminate 口才](https://github.com/illuminate/database)、[照亮事件](https://github.com/illuminate/events)、 [Symfony Http Foundation](https://github.com/symfony/HttpFoundation) 和 [ircmaxell password-compat](https://github.com/ircmaxell/password_compat) 所以让我们将它们添加到项目中。

```
composer require illuminate/database illuminate/events symfony/http-foundation ircmaxell/password-compat
```

因为处理用户、组和权限需要一些数据库交互，所以我们需要用必要的表创建数据库。如果您使用的是 Laravel，可以使用 migrate 命令安装数据库。

```
php artisan vendor:publish --provider="Cartalyst\Sentinel\Laravel\SentinelServiceProvider"
php artisan migrate
```

如果没有，您必须使用以下步骤手动完成。您可以在[文档](https://cartalyst.com/manual/sentinel/2.0#configuration)中阅读更多关于数据库安装的信息。

*   打开`vendor/cartalyst/sentinel/schema/mysql.sql`，在文件顶部添加`use DATABASENAME;`并保存。
*   使用命令`mysql -u root -p < vendor/cartalyst/sentinel/schema/mysql.sql`将文件输入 MySQL，如果需要的话输入密码，或者使用您喜欢的 SQL 编辑器/检查器(如 PhpMyAdmin、Sequel Pro 等)导入文件。

最后，让我们用一个`public/index.php`文件引导我们的应用程序:

```
<?php

require_once __DIR__.'/../vendor/autoload.php'; $app =  new \Slim\Slim();  //register bindings include_once __DIR__.'/../app/bootstrap/container.php'; include_once __DIR__.'/../app/routes.php'; $app->run();
```

## 容器绑定

在我们的`app/bootstrap/container.php`中，我们将定义我们的容器绑定。

```
$app->container->twigLoader = new Twig_Loader_Filesystem(__DIR__.'/../views');
$app->container->twig = new Twig_Environment($app->container->twigLoader, array(
    'cache' => false,
));
```

`$app`变量是一个`Slim`容器。因为我们选择使用雄辩来进行数据库交互，所以我们需要指定连接配置。

```
// app/bootstrap/container.php

$capsule = new \Illuminate\Database\Capsule\Manager();
$capsule->addConnection([
    'driver' => 'mysql',
    'host' => 'localhost',
    'database' => 'capsule',
    'username' => 'root',
    'password' => 'root',
    'charset' => 'utf8',
    'collation' => 'utf8_unicode_ci',
]);
$capsule->bootEloquent();
```

在引导雄辩之后，剩下的唯一部分就是将 Sentinel 绑定到容器。

```
// app/bootstrap/container.php

$app->container->sentinel = (new \Cartalyst\Sentinel\Native\Facades\Sentinel())->getSentinel();
```

如果您更喜欢使用静态调用，可以直接使用它。(`Sentinel::authenticate($credentials)`)

## 创建角色

第一步是定义我们的权限组。我们有`admin`角色，它赋予用户创建、更新和删除用户的权限。另一方面，`user`角色只给出更新用户的权限。

```
$app->container->sentinel->getRoleRepository()->createModel()->create(array(
    'name'          => 'Admin',
    'slug'          => 'admin',
    'permissions'   => array(
        'user.create' => true,
        'user.update' => true,
        'user.delete' => true
    ),
));

$app->container->sentinel->getRoleRepository()->createModel()->create(array(
    'name'          => 'User',
    'slug'          => 'user',
    'permissions'   => array(
        'user.update' => true
    ),
));
```

这段代码是临时的，可以放在`index.php`的底部，运行一次，然后删除。它的唯一目的是在数据库中创建角色。

## 注册页面

创建注册页面时，我们有两个选项。我们可以使用电子邮件确认，或者直接将用户设置为已验证。

```
// app/routes.php

$app->get('/', function () use ($app) {
    $app->twig->display('home.html.twig');
});
```

我们的主页包含一个表单和一组用户详细信息字段(名、姓、电子邮件、密码和密码确认)。在这里查看视图文件[的源代码](https://github.com/Whyounes/sentinel_demo/tree/master/app/views)。

然后，我们来定义一下`POST`回家路线。

```
// app/routes.php

$app->post('/', function () use ($app) {
    // we leave validation for another time
    $data = $app->request->post();

    $role = $app->container->sentinel->findRoleByName('User');

    if ($app->container->sentinel->findByCredentials([
        'login' => $data['email'],
    ])) {
        echo 'User already exists with this email.';

        return;
    }

    $user = $app->container->sentinel->create([
        'first_name' => $data['firstname'],
        'last_name' => $data['lastname'],
        'email' => $data['email'],
        'password' => $data['password'],
        'permissions' => [
            'user.delete' => 0,
        ],
    ]);

    // attach the user to the role
    $role->users()->attach($user);

    // create a new activation for the registered user
    $activation = (new Cartalyst\Sentinel\Activations\IlluminateActivationRepository)->create($user);
    mail($data['email'], "Activate your account", "Click on the link below \n <a href='http://vaprobash.dev/user/activate?code={$activation->code}&login={$user->id}'>Activate your account</a>");

    echo "Please check your email to complete your account registration.";
});
```

从容器中获取请求数据并解析 Sentinel 后，我们选择希望分配给新用户的角色。您可以按名称、slug 或 ID 查询角色。更多信息请见[文档](https://cartalyst.com/manual/sentinel/2.0#roles)。接下来，我们测试新用户是否已经注册，然后我们使用请求数据创建一个新用户，为其分配`User`角色，并创建一个新的激活。激活过程是完全可选的，我们将在后面详细讨论。为了保持函数简单，我们在这里跳过了验证过程。

### 许可

`User`角色赋予用户更新的权限。但是，如果我们想要添加或覆盖一些权限呢？

```
$user = $app->container->sentinel->create([
    'first_name' => $data['firstname'],
    'last_name' => $data['lastname'],
    'email' => $data['email'],
    'password' => $data['password'],
    'permissions' => [
        'user.update' => false,
        'user.create' => true
    ],
]);
```

Sentinel 有两种使用权限的方法:标准或严格。对于标准方法，您可以直接授予用户创建新用户的能力(`'user.create': true`)，但是当您想要拒绝一个角色的权限时，您只需要在用户的权限上授予它`false`。如果在任何地方将设置为`false`，则严格方法将否定权限。你可以在[文档](https://cartalyst.com/manual/sentinel/2.0#permissions)中阅读更多关于权限的内容。

### 用户激活

将用户保存到数据库后，您需要将激活链接发送到用户的电子邮件中。

我们有两种方法来实现激活过程。第一个将通过电子邮件发送代码和用户 ID，第二个将只发送激活码。

```
// app/routes.php

$app->post('/', function () use ($app) {
    //...

    $activation = (new Cartalyst\Sentinel\Activations\IlluminateActivationRepository)->create($user);
    mail($data['email'], "Activate your account", "Click on the link below \n <a href='http://vaprobash.dev/user/activate?code={$activation->code}&login={$user->id}'>Activate your account</a>");

    echo "Please check your email to complete your account registration.";
});
```

`IlluminateActivationRepository`上的`create`方法将返回包含激活码的数据库记录。现在，我们需要创建一个新的路由来捕捉帐户验证请求。

```
// app/routes.php

$app->get('/user/activate', function () use ($app) {
    $code = $app->request->get('code');
    $login = $app->request->get('login');
    $activation = new Cartalyst\Sentinel\Activations\IlluminateActivationRepository;

    if (!($user = $app->sentinel->findById($login)))
    {
        echo "User not found!";

        return;
    }

    if (!$activation->complete($user, $code))
    {
        if ($activation->completed($user))
        {
            echo 'User is already activated. Try to log in.';

            return;
        }
        echo "Activation error!";

        return;
    }

    echo 'Your account has been activated. Log into your account.';

    return;
});
```

第一步是测试用户是否存在。然后，我们尝试完成激活。如果出现错误，我们会检查用户是否已经被激活。

```
// app/routes.php

$app->get('/user/activate', function () use ($app) {
    $code = $app->request->get('code');

    $activationRepository = new Cartalyst\Sentinel\Activations\IlluminateActivationRepository;
    $activation = Cartalyst\Sentinel\Activations\EloquentActivation::where("code", $code)->first();

    if (!$activation)
    {
        echo "Activation error!";

        return;
    }

    $user = $app->container->sentinel->findById($activation->user_id);

    if (!$user)
    {
        echo "User not found!";

        return;
    }

    if (!$activationRepository->complete($user, $code))
    {
        if ($activationRepository->completed($user))
        {
            echo 'User is already activated. Try to log in.';

            return;
        }

        echo "Activation error!";

        return;
    }

    echo 'Your account has been activated. Log in to your account.';

    return;
});
```

第一步是使用`code`参数检索激活记录，然后我们使用激活记录中的用户 ID 抓取用户，最后一部分类似于第一种方法。你可以在[文档](https://cartalyst.com/manual/sentinel/2.0#activation)中了解更多关于激活的信息。
我更喜欢使用第二种方法进行激活，我总是避免向用户发送不必要的详细信息。

## 登录页面

登录页面包含电子邮件字段、密码，用户可以选择被记住。

```
// app/routes.php

$app->get('/login', function () use ($app) {

    $app->twig->display('login.html.twig');
});
```

在获得用户电子邮件和密码后，我们可以将它们传递给`authenticate`方法。它可能会根据错误抛出一个异常(`ThrottlingException`、`NotActivatedException`等)。你可以在[文档](https://cartalyst.com/manual/sentinel/2.0#sentinel-authenticate)中阅读更多关于`authenticate`方法的内容。但是，将来您可能会有一个`remember me`字段来自动记录用户。`authenticateAndRemember`方法类似于`authenticate`方法，默认情况下将“记住我”参数设置为`true`。

```
// app/routes.php

$app->post('/login', function () use ($app) {
    $data = $app->request->post();
    $remember = isset($data['remember']) && $data['remember'] == 'on' ? true : false;

    try {
        if (!$app->container->sentinel->authenticate([
                'email' => $data['email'],
                'password' => $data['password'],
            ], $remember)) {

            echo 'Invalid email or password.';

            return;
        } else {
            echo "You're logged in";

            return;
        }
    } catch (Cartalyst\Sentinel\Checkpoints\ThrottlingException $ex) {
        echo "Too many attempts!";

        return;
    } catch (Cartalyst\Sentinel\Checkpoints\NotActivatedException $ex){
        echo "Please activate your account before trying to log in";

        return;
    }
});
```

`authenticate`方法将在成功时返回用户，在失败时返回 false，但重要的是，它可以根据启用的[检查点](https://cartalyst.com/manual/sentinel/2.0#checkpoints)抛出异常，这些检查点就像拦截登录过程并决定您是否可以继续的中间件。在这种情况下，我们可能有一个`ThrottlingException`以防滥用登录尝试，或者一个`NotActivatedException`如果用户还没有被激活。您可以在`checkpoints`部分配置`vendor/cartalyst/sentinel/src/config`内已启用的检查点。

## 注销

注销当前用户非常简单。

```
// app/routes.php

$app->get('/logout', function () use ($app) {
    $app->container->sentinel->logout();

    echo 'Logged out successfully.';
});
```

## 管理页面

我们的管理页面不包含任何查看内容。我们将测试用户是否已连接，获取已登录的用户，然后测试他是否拥有访问该页面所需的权限。

```
// app/routes.php

$app->get('/admin/users', function () use ($app) {
    $loggedUser = $app->container->sentinel->check();

    if (!$loggedUser) {
        echo 'You need to be logged in to access this page.';

        return;
    }

    if (!$loggedUser->hasAccess('user.*')) {
        echo "You don't have the permission to access this page.";

        return;
    }

    echo 'Welcome to the admin page.';
});
```

`check`方法返回登录的用户或`false`。如果我们有一个用户，我们用所需的权限调用`hasAccess`方法。`*`别名选择任何权限。根据访问资源所需的权限，您可以使用`user.update`或`user.delete`。

## 包扎

Sentinel 包免去了用户权限和角色管理的痛苦。它还提供了添加密码提醒、密码哈希等功能。请务必查看[文档](https://cartalyst.com/manual/sentinel/2.0)以了解更多详情，在 [Github](https://github.com/sitepoint-editors/sentinel_demo) 上查看最终产品，如果您有任何问题或意见，请不要犹豫，在下面发布。

## 分享这篇文章