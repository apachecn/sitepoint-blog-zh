# 如何构建一个只有 API 的 JWT 驱动的 Laravel 应用程序

> 原文：<https://www.sitepoint.com/how-to-build-an-api-only-jwt-powered-laravel-app/>

Laravel API 样板文件(JWT 版)是在巨人的肩膀上制作的，比如:

*   [Dingo API](https://github.com/dingo/api)；
*   [JWT-Auth，由 tymondesigns](https://github.com/tymondesigns/jwt-auth)；
*   [巴瑞夫德](https://github.com/barryvdh/laravel-cors)的《拉勒韦尔 5》上的 CORS；

在本文中，我们将学习如何使用它为一个虚构的图书愿望列表应用程序快速创建一个全功能的 API。作为题外话，我们还将看到如何用 AngularJS 构建一个使用我们的 API 的客户端应用程序。

![Laravel Logo](img/b24fd9db18c1157e55823b1b5d7e1e96.png)

## 创建项目

本教程假设已经建立了一个工作的 PHP 环境，类似于 [Homestead 改进的](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。让我们安装 API 样板文件。我们所要做的就是克隆 [Github 库](https://github.com/francescomalatesta/laravel-api-boilerplate-jwt)，用

```
git clone https://github.com/francescomalatesta/laravel-api-boilerplate-jwt Laravel 
```

那就跑

```
composer install 
```

安装依赖项并进行所有基本的配置操作。拉韦尔和 JWT 密钥都将自动生成。

![Everything went well](img/7592b487281d0f27516a99a195d61f99.png)

没什么可做的了！让我们构建应用程序的服务器部分！

## 创建 API

在做任何事情之前，我们必须计划我们的应用程序的细节和特性。我们将建立一个基本的图书愿望列表，所以我们将使用两个主要实体:**用户**和**图书**。

**用户**将能够:

*   注册，以便创建一个新帐户；
*   如果他们已经有帐户，请登录；
*   注销，离开应用程序；
*   获得他们的图书清单；
*   将一本新书添加到愿望清单中；
*   编辑现有图书；
*   从列表中删除现有图书；

请注意，对于每个**用户**，我们将要求:

*   姓名；
*   电子邮件地址；
*   密码；

一本**书**会有一个:

*   标题；
*   作者姓名；
*   页数；

最后，每个列表将显示从最近的一本书开始的书籍。

第一步:**用户**部分。

### 构建用户部件

好消息是用户部分已经完成了。

如果我们打开`app/Api/V1/Controllers/AuthController.php`文件，我们可以看到它已经有了一个`signup`和一个`login`方法！它们不是我们在标准 new Laravel 项目的标准`AuthController`中看到的那些:这里它们被重新实现为基于令牌的注册和登录过程。

现在，让我们特别来看看`signup`方法。

```
public function signup(Request $request)
{
    $signupFields = Config::get('boilerplate.signup_fields');
    $hasToReleaseToken = Config::get('boilerplate.signup_token_release');

    $userData = $request->only($signupFields);

    $validator = Validator::make($userData, Config::get('boilerplate.signup_fields_rules'));

    if($validator->fails()) {
        throw new ValidationHttpException($validator->errors()->all());
    }

    User::unguard();
    $user = User::create($userData);
    User::reguard();

    if(!$user->id) {
        return $this->response->error('could_not_create_user', 500);
    }

    if($hasToReleaseToken) {
        return $this->login($request);
    }

    return $this->response->created();
} 
```

正如我们从代码中看到的，我们在配置文件中使用了一些选项。具体来说，我们从这个文件中获得两个值:

*   `boilerplate.signup_fields`:我们要用于注册操作的字段列表；
*   `boilerplate.signup_fields_rules`:在注册操作过程中，我们希望对我们的数据进行检查的验证规则；

我们可以在`config/boilerplate.php`中找到所有的样板选项。

这里还使用了另一个选项:`boilerplate.signup_token_release`。当设置为`true`时，令牌会在注册后自动释放。这意味着**用户可以立即开始使用你的应用**。

**注意:**对于这个项目，我们将使用 24 小时令牌。令牌生命周期的默认值是 60 分钟。这可以在`config/jwt.php`中的`ttl`值处改变。

回到我们的 API。`boilerplate.signup_fields`的实际设置是

```
'signup_fields' => [
    'name', 'email', 'password'
], 
```

…考虑到我们的需求，这很好。这里也有一些基本规则:

```
'signup_fields_rules' => [
	'name' => 'required',
	'email' => 'required|email|unique:users',
	'password' => 'required|min:6'
], 
```

到目前为止，我们还没有做任何事情。此外，我们在`app/Http/api_routes.php`文件中已经有了我们的路线。

```
// app/Http/api_routes.php

$api = app('Dingo\Api\Routing\Router');

$api->version('v1', function ($api) {

	$api->post('auth/login', 'App\Api\V1\Controllers\AuthController@login');
	$api->post('auth/signup', 'App\Api\V1\Controllers\AuthController@signup');

	...

}); 
```

我们在这里使用的路由器与我们在 Laravel 看到的普通路由器略有不同。事实上，我们正在使用定制的 Dingo API 路由器。

**用户部分现在已经完成**，无需输入任何代码。

唯一剩下的部分是`logout`程序。然而，RESTful API 根据定义是无状态的，这是我们将要在客户端实现的。

现在，*我们至少应该测试我们的两个程序*，以确保万无一失。首先要做的是**迁移我们的数据库**。在我们的终端中，让我们输入

```
 php artisan migrate 
```

让我们测试一个基本的注册程序。我们将打开一个 HTTP 客户端并调用`signup`路由。出于本文的目的，我们将使用[邮递员](https://www.getpostman.com/)。让我们填写我们的请求正文…

![Request body filled out](img/987a3bffc1e023ee99bf55fc6e0a9e39.png)

…发送并等待结果…

![Result produced](img/fd9131c63544f2d61e6ec19b84aeb2c7.png)

搞定了。我们的用户现在被添加到应用程序中。为了保险起见，我们再做一个测试。这一次为`login`。

![Logging in via postman](img/a5cac4769205ac47b29b55aedc0a9369.png)

太好了！`login`程序也运行良好！对于**用户**部分，我们不需要任何其他东西。是时候通过实现**书**部分来完成我们的 API 了。

### 构建图书部分

为了实现图书端，我们需要一个迁移来定义我们的模式结构，一个模型，最后，一个控制器来处理所有的操作。让我们从**迁移**开始。让我们打开终端并键入

```
 php artisan make:migration create_books_table --create=books 
```

并且会在`database/migrations`中创建一个新文件。然后，我们在`up()`方法中添加以下字段。

```
public function up()
{
    Schema::create('books', function (Blueprint $table) {
        $table->increments('id');

		// adding specific fields here...
        $table->string('title');
        $table->string('author_name');
        $table->integer('pages_count');

        $table->integer('user_id')->index();

        $table->timestamps();
    });
} 
```

是时候再次“迁移”了，输入

```
 php artisan migrate 
```

将新表添加到模式中。现在，让我们创建模型，用

```
 php artisan make:model Book 
```

我们结束了。由于 Laravel 中的约定，`Book`模型已经“知道”它必须与我们创建的`books`表一起工作。模型是这样的:

```
namespace App;

use Illuminate\Database\Eloquent\Model;

class Book extends Model
{
    protected $fillable = ['title', 'author_name', 'pages_count'];
} 
```

我们将把这些字段(`title`、`author_name`和`pages_count`)放入`fillable`数组中，以便在更新过程中使用`fill`方法(稍后将详细介绍)。

现在，我们必须编辑我们的`User`模型，以便定义我们将需要检索他们的相关书籍的关系。在`app\User.php`中，我们添加了以下方法:

```
public function books()
{
    return $this->hasMany('App\Book');
} 
```

最后一步是`Book`实体的资源控制器。让我们打字

```
 php artisan make:controller BookController 
```

创建新的资源控制器。Laravel 将在`app/Http/Controllers`文件夹中创建它。我们会把它移到`app/Api/V1/Controllers`。

我们还更改了名称空间:

```
namespace App\Api\V1\Controllers; 
```

其中`App`是我们为应用程序选择的基本名称空间。默认情况下，我们可以让它保持原样。

在继续之前，我们必须记住在文件的开头添加一些`use`指令:

```
use JWTAuth;
use App\Book;
use Dingo\Api\Routing\Helpers; 
```

第一个是 JWTAuth Facade，我们将使用它从令牌中检索用户数据。第二个是我们的模型。第三个是一个助手，我们将使用它快速创建每个响应，以 RESTful 方式输出到客户机。

我们还必须记住在控制器中包含这个`Helpers`特征:

```
...

class BookController extends Controller
{
    use Helpers;

... 
```

现在我们可以实现我们的方法了:

*   `index`，获取某个用户的图书列表；
*   `show`，获取给定其 id 的单本书；
*   `store`，在数据库中存储新创建的图书；
*   `update`，给定 id，存储某本书的更新；
*   `destroy`，给定 id，从数据库中删除现有图书；

先从第一个说起:`index()`。

```
public function index()
{
    $currentUser = JWTAuth::parseToken()->authenticate();
    return $currentUser
        ->books()
        ->orderBy('created_at', 'DESC')
        ->get()
        ->toArray();
} 
```

这个真的很好理解。我们从令牌开始获取用户，然后返回所有相关的书籍作为输出。仅此而已。

我们可以向前迈一步，到`store()`

```
public function store(Request $request)
{
    $currentUser = JWTAuth::parseToken()->authenticate();

    $book = new Book;

    $book->title = $request->get('title');
    $book->author_name = $request->get('author_name');
    $book->pages_count = $request->get('pages_count');

    if($currentUser->books()->save($book))
        return $this->response->created();
    else
        return $this->response->error('could_not_create_book', 500);
} 
```

像以前一样，我们用`JWTAuth`方法`parseToken()->authenticate()`获取当前用户。然后，我们创建实例，并用`save`方法保存关系。

如果一切顺利，将返回一个`Created 201`响应。否则，客户端将得到一个自定义的`500`错误。正如我们所看到的，这不仅仅是关于主体:头也将被相应地调整以遵循 RESTful 标准。

在完成我们的控制器之前，让我们做一些测试。将以下代码添加到`api_routes.php`中:

```
$api->group(['middleware' => 'api.auth'], function ($api) {
	$api->post('book/store', 'App\Api\V1\Controllers\BookController@store');
	$api->get('book', 'App\Api\V1\Controllers\BookController@index');
}); 
```

我们现在两条路线都有了！让我们回到我们的 HTTP 客户端并再次登录。使用我们的新令牌，让我们发出一些请求来存储一本新书，然后立即获取它们的列表。

我们可以通过两种方式传递令牌:作为一个简单的请求参数传递，或者在带有这个特定项的头中传递:`Authorization: Bearer {token_goes_here}`。

也就是说，这里有一个`store`请求和随后的`index`请求的例子。

![Token filled out for storing](img/ef11f57843017ffbd8476f05c0f89220.png)

…然后…

![Index lists out books](img/06ea2f2bf5c7064d2fb389f8fa8e8cd5.png)

有用！

我们可以通过实现剩下的三种方法来完成这项工作:`show`、`update`和`destroy`。

他们来了！

```
...

public function show($id)
{
    $currentUser = JWTAuth::parseToken()->authenticate();

    $book = $currentUser->books()->find($id);

    if(!$book)
        throw new NotFoundHttpException; 

    return $book;
}

public function update(Request $request, $id)
{
    $currentUser = JWTAuth::parseToken()->authenticate();

    $book = $currentUser->books()->find($id);
    if(!$book)
        throw new NotFoundHttpException;

    $book->fill($request->all());

    if($book->save())
        return $this->response->noContent();
    else
        return $this->response->error('could_not_update_book', 500);
}

public function destroy($id)
{
    $currentUser = JWTAuth::parseToken()->authenticate();

    $book = $currentUser->books()->find($id);

    if(!$book)
        throw new NotFoundHttpException;

    if($book->delete())
        return $this->response->noContent();
    else
        return $this->response->error('could_not_delete_book', 500);
}

... 
```

我们继续使用`$currentUser`作为隐含的“安全检查”。在现实世界中，你应该构建一些更健壮的东西。

也有可能抛出一些 Symfony 异常。API 将自动识别它们，并以正确的输出格式进行编码。您可以在此找到完整的支持例外列表[。](https://github.com/dingo/api/wiki/Errors-And-Error-Responses)

在`show`、`update`和`destroy`中，我们用`NotFoundHttpException`告诉客户端我们还没有找到某个资源。

现在，我们要做的就是将最后三条新路线添加到`api_routes.php`文件中。

```
$api->group(['middleware' => 'api.auth'], function ($api) {
	$api->get('books', 'App\Api\V1\Controllers\BookController@index');
	$api->get('books/{id}', 'App\Api\V1\Controllers\BookController@show');
	$api->post('books', 'App\Api\V1\Controllers\BookController@store');
	$api->put('books/{id}', 'App\Api\V1\Controllers\BookController@update');
	$api->delete('books/{id}', 'App\Api\V1\Controllers\BookController@destroy');
}); 
```

Laravel(和 Dingo)有一个快捷方法`resource()`,我们可以用这个方法用一条指令绑定我们刚刚看到的所有路线。

```
$api->resource('books', 'App\Api\V1\Controllers\BookController'); 
```

选择你最喜欢的方法。

控制器现在完成了。[全在这里](https://github.com/francescomalatesta/laravel-api-boilerplate-angular-example/blob/master/app/Api/V1/Controllers/BookController.php)！

**注意:**我们可能会在一些身体数据中使用`PUT`和`DELETE`动词时遇到一些问题。**如果身体一直空着**，尽量用`x-www-form-urlencoded`代替`form-data`。这应该可以解决问题。

服务器部分完成了！

## 结论

在这一部分中，我们将重点放在后端，并使用一个相当稳定且易于使用的样板文件来构建我们的 API 和认证+注册流程。就这样，我们的应用程序已经是客户端友好的了。拼凑一个 iOS 或 Android 应用程序，甚至是众多 JavaScript 框架中的一个，您就可以轻松地使用我们应用程序的内容。这正是我们将在第 2 部分关注的——从头开始为这个后端构建一个有角度的应用程序！敬请期待！

## 分享这篇文章