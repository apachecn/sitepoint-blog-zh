# 让我们杀死密码！神奇的登陆链接拯救！

> 原文：<https://www.sitepoint.com/lets-kill-the-password-magic-login-links-to-the-rescue/>

*这篇文章由[尤尼斯·拉菲](https://www.sitepoint.com/author/yrafie/)和[沃恩·安切塔](https://www.sitepoint.com/author/wancheta)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

身份验证是多年来不断发展的东西。我们已经看到它从电子邮件-密码组合到社交认证，最后是无密码认证。实际上，更像是“仅电子邮件”认证。在无密码登录的情况下，如果提供的电子邮件确实是你的，应用程序会假设你会从收件箱中获得登录链接。

![Vector illustration of an open padlock on yellow background](img/437bd1161a11d01527f287b8a9489c0e.png)

无密码登录系统的一般流程如下:

*   用户访问登录页面
*   他们输入电子邮件地址并确认
*   链接会发送到他们的电子邮件中
*   点击链接后，他们会被重定向回应用程序并登录
*   链接被禁用

当你不记得某个应用程序的密码，但却记得你注册时使用的电子邮件时，这就很方便了。甚至 [Slack](https://slack.com/) 也采用了这种技术。

在本教程中，我们将在一个 Laravel 应用程序中实现这样一个系统。完整的代码可以在[这里](https://github.com/sitepoint-editors/passwordless-login)找到。

## 创建应用程序

让我们从生成一个新的 Laravel 应用程序开始。我在本教程中使用的是 Laravel 5.2:

```
composer create-project laravel/laravel passwordless-laravel 5.2.* 
```

如果你已经有一个包含用户和密码的 Laravel 项目，不用担心——我们不会干扰正常的授权流程，只是在已经存在的基础上创建一个层。用户仍然可以选择使用密码登录。

### 数据库设置

接下来，我们必须在运行任何迁移之前设置我们的 MySQL 数据库。

在根目录中打开您的`.env`文件，并传入主机名、用户名和数据库名:

```
[...]
DB_CONNECTION=mysql
DB_HOST=localhost
DB_DATABASE=passwordless-app
DB_USERNAME=username
DB_PASSWORD=
[...] 
```

如果你使用我们的[家园改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)盒子，数据库/用户名/密码组合为`homestead`、`homestead`、`secret`。

### 脚手架授权

Laravel 在 5.2 版本中引入的一个很棒的东西是只需一个命令就可以添加一个预制的身份验证层。让我们这样做:

```
php artisan make:auth 
```

这个命令搭建了我们认证所需的一切，即视图、控制器和路由。

### 迁移

如果我们查看`database/migrations`，我们会注意到生成的 Laravel 应用程序附带了创建`users`表和`password_resets`表的迁移。

我们不会改变任何东西，因为我们仍然希望我们的应用程序有正常的认证流程。

要创建表，请运行:

```
php artisan migrate 
```

我们现在可以提供应用程序，用户应该能够注册并使用导航中的链接登录。

### 更改登录链接

接下来，我们希望更改登录链接，将用户重定向到一个自定义登录视图，用户将在该视图中提交他们的电子邮件地址，而无需密码。

导航至`resources/views/layouts/app.blade.php`。我们在那里找到了导航部分。将带有登录链接的行(在条件的正下方，用于检查用户是否已注销)更改为:

*资源/视图/布局/app.blade.php*

```
[...]
@if (Auth::guest())
<li><a href="{{ url('/login/magiclink') }}">Login</a></li>
<li><a href="{{ url('/register') }}">Register</a></li>
[...] 
```

当用户试图在未登录的情况下访问受保护的路由时，他们应该被带到我们新的自定义登录视图，而不是普通的视图。该行为在认证中间件中指定。我们必须调整一下:

*app/Http/Middleware/authenticate . PHP*

```
class Authenticate
{
[...]
public function handle($request, Closure $next, $guard = null)
{
    if (Auth::guard($guard)->guest()) {
        if ($request->ajax() || $request->wantsJson()) {
            return response('Unauthorized.', 401);
        } else {
            return redirect()->guest('login/magiclink');
        }
    }

    return $next($request);
}
[...] 
```

注意在`else block`中，我们已经将重定向改为指向`login/magiclink`而不是普通的`login`。

## 创建神奇的登录控制器、视图和路由

我们的下一步是在 Auth 文件夹中创建`MagicLoginController`:

```
php artisan make:controller Auth\\MagicLoginController 
```

然后路线显示我们的自定义登录页面:

*app/Http/routes.php*

```
[...]
Route::get('/login/magiclink', 'Auth\MagicLoginController@show'); 
```

让我们更新我们的`MagicLoginController`以包括一个显示动作:

*app/Http/Controllers/Auth/magiclogincontroller . PHP*

```
class MagicLoginController extends Controller
{
    [...]
    public function show()
    {
        return view('auth.magic.login');
    }
    [...]
} 
```

对于新的登录视图，我们将借用普通的登录视图，但是删除密码字段。我们还将把表单的 post URL 改为指向`\login\magiclink`。

让我们在`views/auth`文件夹中创建一个神奇的文件夹来保存这个新视图:

```
mkdir resources/views/auth/magic
touch resources/views/auth/magic/login.blade.php 
```

让我们将新创建的视图更新为:

*resources/views/auth/magic/log in . blade . PHP*

```
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row">
        <div class="col-md-8 col-md-offset-2">
            <div class="panel panel-default">
                <div class="panel-heading">Login</div>
                <div class="panel-body">
                    <form class="form-horizontal" role="form" method="POST" action="{{ url('/login/magiclink') }}">
                        {{ csrf_field() }}

                        <div class="form-group{{ $errors->has('email') ? ' has-error' : '' }}">
                            <label for="email" class="col-md-4 control-label">E-Mail Address</label>

                            <div class="col-md-6">
                                <input id="email" type="email" class="form-control" name="email" value="{{ old('email') }}" required autofocus>

                                @if ($errors->has('email'))
                                    <span class="help-block">
                                        <strong>{{ $errors->first('email') }}</strong>
                                    </span>
                                @endif
                            </div>
                        </div>

                        <div class="form-group">
                            <div class="col-md-6 col-md-offset-4">
                                <div class="checkbox">
                                    <label>
                                        <input type="checkbox" name="remember"> Remember Me
                                    </label>
                                </div>
                            </div>
                        </div>

                        <div class="form-group">
                            <div class="col-md-8 col-md-offset-4">
                                <button type="submit" class="btn btn-primary">
                                    Send magic link
                                </button>

                                <a href="{{ url('/login') }}" class="btn btn-link">Login with password instead</a>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
@endsection 
```

我们将保留使用密码登录的选项，因为用户仍然可以选择密码登录。因此，如果用户从导航中单击登录，他们将被带到如下所示的登录视图:

![Example of view with login form](img/66d7554f4662fc56a34f0999a1b6da9a.png)

### 生成令牌并将它们与用户相关联

我们的下一步是生成令牌并将它们与用户相关联。当一个人提交电子邮件以便登录时，就会发生这种情况。

让我们首先创建一个路由来处理登录表单的发布操作:

*app/Http/routes.php*

```
[...]
Route::post('/login/magiclink', 'Auth\MagicLoginController@sendToken'); 
```

然后，我们在`MagicLoginController`中添加一个名为`sendToken`的控制器方法。该方法将验证电子邮件地址，将令牌与用户相关联，发送登录电子邮件，并显示一条消息，通知用户查看其电子邮件:

*app/Http/Controllers/Auth/magiclogincontroller . PHP*

```
class MagicLoginController extends Controller
{
    [...]
    /**
     * Validate that the email has a valid format and exists in the users table
     * in the email column
     */
    public function sendToken(Request $request)
    {
        $this->validate($request, [
            'email' => 'required|email|max:255|exists:users,email'
        ]);
    //will add methods to send off a login email and a flash message later
    }
    [...]
} 
```

现在我们有了一个有效的电子邮件地址，我们可以向用户发送一封登录电子邮件。但是在发送电子邮件之前，我们必须为试图登录的用户生成一个令牌。我不想让我的所有方法都在`MagicLoginController`中，因此我们将创建一个`users-token`模型来处理其中的一些方法。

```
php artisan make:model UserToken -m 
```

这个命令将使我们既是模型又是迁移。我们需要稍微调整一下迁移，添加`user_id`和`token`列。打开新生成的迁移文件，将`up`方法改为:

*数据库/迁移/{时间戳} _ create _ user _ tokens _ table . PHP*

```
[...]
public function up()
    {
        Schema::create('user_tokens', function (Blueprint $table) {
            $table->increments('id');
            $table->integer('user_id');
            $table->string('token');
            $table->timestamps();
        });
    }
[...] 
```

然后运行`migrate` Artisan 命令:

```
php artisan migrate 
```

在`UserToken`模型中，我们需要添加`user_id`和`token`作为质量可分配属性的一部分。我们还应该定义该模型与`User`模型的关系，反之亦然:

*App/UserToken.php*

```
[...]
class UserToken extends Model
{
    protected $fillable = ['user_id', 'token'];

    /**
     * A token belongs to a registered user.
     *
     * @return \Illuminate\Database\Eloquent\Relations\BelongsTo
     */
    public function user()
    {
        return $this->belongsTo(User::class);
    }
} 
```

然后在`App/User.php`中指定一个`User`只能有一个与之相关联的令牌:

*App/User.php*

```
class User extends Model
{
    [...]
    /**
     * A user has only one token.
     *
     * @return \Illuminate\Database\Eloquent\Relations\BelongsTo
     */
    public function token()
    {
        return $this->hasOne(UserToken::class);
    }
} 
```

现在让我们生成令牌。首先，我们需要在创建令牌之前通过电子邮件检索用户对象。在`User`模型中创建一个名为`getUserByEmail`的方法来处理这个功能:

*App/User.php*

```
class User extends Model
{   
    [...]
    protected static function getUserByEmail($value)
    {
        $user = self::where('email', $value)->first();
        return $user;
    }
    [...]
} 
```

我们必须将`User`和`UserToken`类的名称空间拉入我们的`MagicLoginController`中，以便能够从我们的控制器调用这些类中的方法:

*app/Http/Controllers/Auth/magiclogincontroller . PHP*

```
[...]
use App\User;
use App\UserToken;
[...]
class MagicLoginController extends Controller
{
    [...]
    public function sendToken(Request $request)
    {
        //after validation
        [...]
        $user = User::getUserByEmail($request->get('email'));

        if (!user) {
            return redirect('/login/magiclink')->with('error', 'User not foud. PLease sign up');
        }

        UserToken::create([
            'user_id' => $user->id,
            'token'   => str_random(50)
        ]);
    }
    [...]
} 
```

在上面的代码块中，我们基于提交的电子邮件检索用户对象。在讨论这一点之前，我们必须验证提交的电子邮件地址是否在`users`表中。但是，如果有人绕过验证，提交了一封不存在于我们记录中的电子邮件，我们会发送一条消息，要求他们注册。

一旦我们有了用户对象，我们就为他们生成一个令牌。

## 通过电子邮件发送令牌

我们现在可以将生成的令牌以 URL 的形式通过电子邮件发送给用户。首先，我们必须要求模型中的`Mail` Facade 帮助我们实现电子邮件发送功能。

然而，在本教程中，我们不会发送任何真正的电子邮件。只是确认应用程序可以在日志中发送电子邮件。为此，导航到您的`.env`文件，并在邮件部分设置`MAIL_DRIVER=log`。此外，我们不会创建电子邮件视图；刚从我们`UserToken`班发来一封原始邮件。

让我们在我们的`UserToken`模型中创建一个名为`sendEmail`的方法来处理这个功能。这个 URL 是由`token`、`email address`和`remember me`值组合而成的，它将在这个方法中生成:

*app/UserToken.php*

```
[...]
use Illuminate\Support\Facades\Mail;
[...]
class UserToken extends Model
{
[...]
    public static function sendMail($request)
    {
        //grab user by the submitted email
        $user = User::getUserByEmail($request->get('email'));

        if(!$user) {
            return redirect('/login/magiclink')->with('error', 'User not foud. PLease sign up');
        }

        $url = url('/login/magiclink/' . $user->token->token . '?' . http_build_query([
            'remember' => $request->get('remember'),
            'email' => $request->get('email'),
        ]));

        Mail::raw(
            "<a href='{$url}'>{$url}</a>",
            function ($message) use ($user) {
                $message->to($user->email)
                        ->subject('Click the magic link to login');
            }
        );
    }
[...]
} 
```

在生成 URL 时，我们将使用 PHP 的`http_build_query`函数来帮助我们从传递的选项数组中进行查询。在我们的情况下，它是电子邮件和记住我的价值。生成 URL 后，我们将它发送给用户。

是时候更新我们的`MagicLoginController`并调用`sendEmail`方法了:

*app/Http/Controllers/Auth/magiclogincontroller . PHP*

```
class MagicLoginController extends Controller
{
    [...]
    public function sendToken(Request $request)
    {
        $this->validate($request, [
            'email' => 'required|email|max:255|exists:users,email'
        ]);
        UserToken::storeToken($request);

        UserToken::sendMail($request);

        return back()->with('success', 'We\'ve sent you a magic link! The link expires in 5 minutes');
    }
    [...]
} 
```

我们还将为通知实现一些基本的 flash 消息。在您的`resources/views/layouts/app.blade.php`中，将此块插入到您的`content`的正上方，因为 flash 消息显示在任何其他内容之前的顶部:

*资源/视图/布局/app.blade.php*

```
[...]
<div class="container">
    <div class="row">
        <div class="col-md-8 col-md-offset-2">
            @include ('layouts.partials._notifications')
        </div>
    </div>
</div>
@yield('content')
[...] 
```

然后创建部分通知:

*resources/views/layouts/partials/_ notifications . blade . PHP*

```
@if (session('success'))
    <div class="alert alert-success">
        {{ session('success') }}
    </div>
@endif

@if (session('error'))
    <div class="alert alert-danger">
        {{ session('error') }}
    </div>
@endif 
```

在部分中，我们使用了`session`助手来帮助我们根据会话状态使用不同的通知颜色，即`success`或`error`。

此时，我们能够发送电子邮件。我们可以通过使用有效的电子邮件地址登录，然后导航到`laravel.log`文件来尝试一下。我们应该能够在日志底部看到包含 URL 的电子邮件。

接下来，我们要验证令牌并让用户登录。我们不希望出现 3 天前发出的令牌仍可用于登录的情况。

### 令牌验证和认证

现在我们有了 URL，让我们创建一个路由和控制器操作来处理当用户点击他们电子邮件中的 URL 时会发生什么:

*app/Http/routes.php*

```
[...]
Route::get('/login/magiclink/{token}', 'Auth\MagicLoginController@authenticate'); 
```

让我们在`MagicLoginController`中创建`authenticate`动作。在这个方法中，我们将对用户进行身份验证。我们将通过[路由模型绑定](http://https://laravel.com/docs/5.3/routing#route-model-binding)将令牌拉入到`authenticate`方法中。然后，我们将从令牌中获取用户。请注意，我们必须拉进控制器中的`Auth facade`才能使用`Auth`方法:

*app/Http/Controllers/Auth/magiclogincontroller . PHP*

```
[...]
use Auth;
[...]
class MagicLoginController extends Controller
{
    [...]
    public function authenticate(Request $request, UserToken $token)
        {
            Auth::login($token->user, $request->remember);
            $token->delete();
            return redirect('home');
        }
    [...]
} 
```

然后在`UserToken class`中设置我们期望的路由键名。在我们的例子中，它是令牌:

*App/UserToken.php*

```
[...]
public function getRouteKeyName()
{
    return 'token';
}
[...] 
```

现在我们有了。用户现在可以登录了。注意，在用户登录后，我们删除了令牌，因为我们不想用用过的令牌填充`user_tokens`表。

我们的下一步是检查令牌是否仍然有效。对于这个应用程序，我们将使魔法链接在 5 分钟后过期。我们将需要 [Carbon](https://www.sitepoint.com/suggesting-carbon-with-composer-date-and-time-the-right-way/) 库来帮助我们检查令牌创建时间和当前时间之间的时间差。

在我们的`UserToken`模型中，我们将创建两个方法:`isExpired`和`belongsToEmail`来检查令牌的有效性。注意，`belongsToEmail`验证只是一种额外的预防措施，确保令牌确实属于该电子邮件地址:

*App/UserToken.php*

```
[...]
use Carbon\Carbon;
[...]
class UserToken extends Model
{
    [...]
    //Make sure that 5 minutes have not elapsed since the token was created
    public function isExpired()
    {
        return $this->created_at->diffInMinutes(Carbon::now()) > 5;
    }

    //Make sure the token indeed belongs to the user with that email address
    public function belongsToUser($email)
    {
        $user = User::getUserByEmail($email);

        if(!$user || $user->token == null) {
            //if no record was found or record found does not have a token
            return false;
        }

        //if record found has a token that matches what was sent in the email
        return ($this->token === $user->token->token);
    }
    [...]
} 
```

让我们调用`MagicLoginController`中的`authenticate`方法中的 token 实例上的方法:

*app/Http/Controllers/Auth/magiclogincontroller . PHP*

```
class MagicLoginController extends Controller
{
    [...]
    public function authenticate(Request $request, UserToken $token)
        {
            if ($token->isExpired()) {
                $token->delete();
                return redirect('/login/magiclink')->with('error', 'That magic link has expired.');
            }

            if (!$token->belongsToUser($request->email)) {
                $token->delete();
                return redirect('/login/magiclink')->with('error', 'Invalid magic link.');
            }

            Auth::login($token->user, $request->get('remember'));
            $token->delete();
            return redirect('home');

        }
    [...]
} 
```

## 结论

我们已经成功地将无密码登录添加到正常的身份验证流程之上。有些人可能会认为这比正常的密码登录需要更长的时间，但使用密码管理器也是如此。

不过，无密码系统并不是在所有地方都能工作，如果你的会话超时时间很短，或者希望用户频繁登录，这可能会令人沮丧。幸运的是，这只会影响很少的网站。

难道你不认为是时候给用户提供另一种登录你下一个项目的方式了吗？

请在下面留下你的评论和问题，如果你喜欢这篇文章，记得分享给你的朋友和同事！

## 分享这篇文章