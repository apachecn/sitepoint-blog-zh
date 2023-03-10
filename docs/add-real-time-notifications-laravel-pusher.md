# 如何使用 Pusher 向 Laravel 添加实时通知

> 原文：<https://www.sitepoint.com/add-real-time-notifications-laravel-pusher/>

*这篇文章由[拉菲·尤尼斯](https://www.sitepoint.com/author/yrafie/)和[沃恩·安切塔](https://www.sitepoint.com/author/wancheta/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

现代 web 用户希望了解应用程序中发生的所有事情。你不会想成为一个连通知下拉菜单都没有的网站，不仅在所有的社交媒体网站上可以找到，在其他地方也可以找到。

幸运的是，有了 Laravel 和 [Pusher](https://pusher.com) ，实现这个功能变得轻而易举。我们将在本教程中编写的代码可以在[这里](https://github.com/sitepoint-editors/laravel-pusher-notifications)找到。

![Illustration from Pusher, featuring browser and mobile device being in sync with notifications](img/20c08696f0b0691bd23a84a8384480cb.png)
*图片经由 Pusher.com*

## 实时通知

为了给用户提供良好的体验，通知应该实时显示。一种方法是定期向后端发送 AJAX 请求，并获取最新的通知(如果它们存在的话)。

一个更好的方法是利用 [WebSockets](https://www.sitepoint.com/websockets-in-your-synchronous-site/) 的能力，在通知发出的那一刻接收通知。这是我们将在本教程中使用的。

## 推进器

Pusher 是一个 web 服务，用于

> …通过 WebSockets 将实时双向功能集成到 web 和移动应用程序中。

它有一个非常简单的 API，但我们将通过[拉韦尔广播](https://laravel.com/docs/5.4/broadcasting)和[拉韦尔回声](https://github.com/laravel/echo)使它的使用更加简单。

在本教程中，我们将向现有博客添加实时通知。
基本功能类似于[通过流](https://www.sitepoint.com/real-time-laravel-notifications-follows-sure-stream/)实时发布通知。
我们将从[这个由](https://github.com/yazfield/simple-blog) [Christopher Vundi](https://www.sitepoint.com/author/cvundi/) 制作的回购开始(我只是稍微修改了一下)，这是一个简单的博客，用户可以在这里对帖子进行 CRUD。

## 该项目

### 初始化

首先，我们将克隆简单的 Laravel 博客:

```
git clone https://github.com/vickris/simple-blog 
```

然后，我们将创建一个 MySQL 数据库，并设置环境变量来让应用程序访问数据库。

让我们将`env.example`复制到`.env`并更新数据库相关变量。

```
cp .env.example .env 
```

```
DB_HOST=localhost
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret 
```

> `.env`

现在让我们安装项目的依赖项

```
composer install 
```

并运行迁移和播种命令，用一些数据填充数据库:

```
php artisan migrate --seed 
```

如果您运行应用程序并访问`/posts`，您将能够看到生成的帖子列表。
检查应用程序，注册一个用户，创建一些帖子。这是一个非常基本的应用程序，但可以完美地服务于我们的演示。

### 关注用户关系

我们希望让用户能够关注其他用户，并被用户关注，因此我们必须在用户之间创建一种`Many To Many`关系来实现这一点。

让我们制作一个将用户与用户联系起来的数据透视表。进行新的`followers`迁移:

```
php artisan make:migration create_followers_table --create=followers 
```

我们需要向该迁移添加一些字段:一个`user_id`字段表示被跟踪的用户，一个`follows_id`字段表示被跟踪的用户。

按如下方式更新迁移:

```
public function up()
{
    Schema::create('followers', function (Blueprint $table) {
        $table->increments('id');
        $table->integer('user_id')->index();
        $table->integer('follows_id')->index();
        $table->timestamps();
    });
} 
```

现在让我们迁移以创建表:

```
php artisan migrate 
```

如果你已经阅读了[流方法文章](https://www.sitepoint.com/real-time-laravel-notifications-follows-sure-stream/)，你会发现到目前为止事情几乎是相同的。在接下来的部分中，我们将使用不同的方法实现相同的 follow 功能。

让我们给`User`模型添加关系方法。

```
// ...

class extends Authenticatable
{
    // ...

    public function followers() 
    {
        return $this->belongsToMany(self::class, 'followers', 'follows_id', 'user_id')
                    ->withTimestamps();
    }

    public function follows() 
    {
        return $this->belongsToMany(self::class, 'followers', 'user_id', 'follows_id')
                    ->withTimestamps();
    }
} 
```

> `app/User.php`

既然用户模型已经有了必要的关系，`followers`返回用户的所有关注者，`follows`返回用户正在关注的每个人。

我们将需要一些助手函数来允许用户`follow`另一个用户，并检查一个用户`isFollowing`是否是一个特定用户。

```
// ...

class extends Authenticatable
{
    // ...

    public function follow($userId) 
    {
        $this->follows()->attach($userId);
        return $this;
    }

    public function unfollow($userId)
    {
        $this->follows()->detach($userId);
        return $this;
    }

    public function isFollowing($userId) 
    {
        return (boolean) $this->follows()->where('follows_id', $userId)->first(['id']);
    }

} 
```

> `app/User.php`

完美。有了模型集，就该列出用户了。

### 列出用户

让我们从设置必要的路线开始

```
//...
Route::group(['middleware' => 'auth'], function () {
    Route::get('users', 'UsersController@index')->name('users');
    Route::post('users/{user}/follow', 'UsersController@follow')->name('follow');
    Route::delete('users/{user}/unfollow', 'UsersController@unfollow')->name('unfollow');
}); 
```

> `routes/web.php`

然后，该为用户创建一个新的控制器了:

```
php artisan make:controller UsersController 
```

我们将向它添加一个`index`方法:

```
// ...
use App\User;
class UsersController extends Controller
{
    //..
    public function index()
    {
        $users = User::where('id', '!=', auth()->user()->id)->get();
        return view('users.index', compact('users'));
    }
} 
```

> `app/Http/Controllers/UsersController.php`

该方法需要一个视图。让我们创建`users.index`视图，并将这个标记放入其中:

```
@extends('layouts.app')

@section('content')
    <div class="container">
        <div class="col-sm-offset-2 col-sm-8">

            <!-- Following -->
            <div class="panel panel-default">
                <div class="panel-heading">
                    All Users
                </div>

                <div class="panel-body">
                    <table class="table table-striped task-table">
                        <thead>
                        <th>User</th>
                        <th> </th>
                        </thead>
                        <tbody>
                        @foreach ($users as $user)
                            <tr>
                                <td clphpass="table-text"><div>{{ $user->name }}</div></td>
                                @if (auth()->user()->isFollowing($user->id))
                                    <td>
                                        <form action="{{route('unfollow', ['id' => $user->id])}}" method="POST">
                                            {{ csrf_field() }}
                                            {{ method_field('DELETE') }}

                                            <button type="submit" id="delete-follow-{{ $user->id }}" class="btn btn-danger">
                                                <i class="fa fa-btn fa-trash"></i>Unfollow
                                            </button>
                                        </form>
                                    </td>
                                @else
                                    <td>
                                        <form action="{{route('follow', ['id' => $user->id])}}" method="POST">
                                            {{ csrf_field() }}

                                            <button type="submit" id="follow-user-{{ $user->id }}" class="btn btn-success">
                                                <i class="fa fa-btn fa-user"></i>Follow
                                            </button>
                                        </form>
                                    </td>
                                @endif
                            </tr>
                        @endforeach
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
@endsection 
```

> `resources/views/users/index.blade.php`

您现在可以访问`/users`页面查看用户列表。

### 跟随，还是不跟随

`UsersController`缺少`follow`和`unfollow`方法。让我们把它们做好来结束这部分。

```
//...
class UsersController extends Controller
{
    //...
    public function follow(User $user)
    {
        $follower = auth()->user();
        if ($follower->id == $user->id) {
            return back()->withError("You can't follow yourself");
        }
        if(!$follower->isFollowing($user->id)) {
            $follower->follow($user->id);

            // sending a notification
            $user->notify(new UserFollowed($follower));

            return back()->withSuccess("You are now friends with {$user->name}");
        }
        return back()->withError("You are already following {$user->name}");
    }

    public function unfollow(User $user)
    {
        $follower = auth()->user();
        if($follower->isFollowing($user->id)) {
            $follower->unfollow($user->id);
            return back()->withSuccess("You are no longer friends with {$user->name}");
        }
        return back()->withError("You are not following {$user->name}");
    }
} 
```

> `app/Http/Controllers/UsersController.php`

我们已经完成了以下功能。我们现在可以从`/users`页面关注和取消关注用户。

### 通知

Laravel 提供了一个通过多种渠道发送通知的 API。电子邮件、SMS、web 通知和任何其他类型的通知都可以使用 [Notification](https://laravel.com/docs/5.4/notifications) 类发送。

我们将有两种类型的通知:

*   关注通知:当一个用户被另一个用户关注时发送给该用户
*   帖子创建通知:当给定用户的追随者创建新帖子时发送给他们

#### 用户关注的通知

使用 artisan 命令，我们可以为通知生成迁移:

```
php artisan notifications:table 
```

让我们迁移并创建这个新表。

```
php artisan migrate 
```

我们从关注通知开始。让我们执行这个命令来生成一个通知类:

```
php artisan make:notification UserFollowed 
```

然后，我们将更新刚刚创建的通知类文件:

```
class UserFollowed extends Notification implements ShouldQueue
{
    use Queueable;

    protected $follower;

    public function __construct(User $follower)
    {
        $this->follower = $follower;
    }

    public function via($notifiable)
    {
        return ['database'];
    }

    public function toDatabase($notifiable)
    {
        return [
            'follower_id' => $this->follower->id,
            'follower_name' => $this->follower->name,
        ];
    }
} 
```

> `app/Notifications/UserFollowed.php`

通过这几行代码，我们可以实现很多目标。首先，我们要求在创建通知时注入一个`$follower`实例。

使用`via`方法，我们告诉 Laravel 通过`database`通道发送这个通知。当 Laravel 遇到这种情况时，它将在 notifications 表中创建一个新记录。

`user_id`和通知`type`是自动设置的，另外我们可以用更多的数据扩展
通知。这就是`toDatabase`的作用。返回的数组将被添加到通知的`data`字段。

最后，通过实现`ShouldQueue`，Laravel 会自动将这个通知放入一个队列中，在后台执行，这将加快响应速度。这是有意义的，因为我们将在稍后使用 Pusher 时添加 HTTP 调用。

让我们在用户被跟踪时启动通知。

```
// ...
use App\Notifications\UserFollowed;
class UsersController extends Controller
{
    // ...
    public function follow(User $user)
    {
        $follower = auth()->user();
        if ( ! $follower->isFollowing($user->id)) {
            $follower->follow($user->id);

            // add this to send a notification
            $user->notify(new UserFollowed($follower));

            return back()->withSuccess("You are now friends with {$user->name}");
        }

        return back()->withSuccess("You are already following {$user->name}");
    }

    //...
} 
```

> `app/Http/Controllers/UsersController.php`

我们可以在一个`User`模型上调用`notify`方法，因为它已经使用了[可通知的](https://laravel.com/docs/5.4/notifications#using-the-notifiable-trait)特征。任何你想要通知的模型都应该使用它来访问`notify`方法。

#### 将通知标记为已读

通知将包含一些信息和一个资源链接。例如:当用户收到一个关于新帖子的通知时，该通知应该显示一个信息性的文本，当点击时将用户重定向到该帖子，并标记为已读。

我们将制作一个中间件，检查一个请求是否有一个`?read=notification_id`输入，并将其标记为 read。

让我们用下面的命令制作一个中间件:

```
php artisan make:middleware MarkNotificationAsRead 
```

然后，让我们将这段代码放入中间件的`handle`方法中:

```
class MarkNotificationAsRead
{
    public function handle($request, Closure $next)
    {
        if($request->has('read')) {
            $notification = $request->user()->notifications()->where('id', $request->read)->first();
            if($notification) {
                $notification->markAsRead();
            }
        }
        return $next($request);
    }
} 
```

> `app/Http/Middleware/MarkNotificationAsRead.php`

为了让我们的中间件为每个请求执行，我们将把它添加到`$middlewareGroups`。

```
//...
class Kernel extends HttpKernel
{
    //...
    protected $middlewareGroups = [
        'web' => [
            //...

            \App\Http\Middleware\MarkNotificationAsRead::class,
        ],
        // ...
    ];
    //...
} 
```

> `app/Http/Kernel.php`

完成后，让我们显示一些通知。

#### 显示通知

我们必须使用 AJAX 显示通知列表，然后用 Pusher 实时更新它。首先，让我们给控制器添加一个`notifications`方法:

```
// ...
class UsersController extends Controller
{
    // ...
    public function notifications()
    {
        return auth()->user()->unreadNotifications()->limit(5)->get()->toArray();
    }
} 
```

> `app/Http/Controllers/UsersController.php`

这将返回最后 5 个未读通知。我们只需要添加一条路线就可以到达。

```
//...
Route::group([ 'middleware' => 'auth' ], function () {
    // ...
    Route::get('/notifications', 'UsersController@notifications');
}); 
```

> `routes/web.php`

现在，在标题中添加一个通知下拉列表。

```
<head>
    <!-- // ... // -->

    <!-- Scripts -->
    <script> window.Laravel = <?php echo json_encode([
            'csrfToken' => csrf_token(),
        ]); ?> </script>

    <!-- This makes the current user's id available in javascript -->
    @if(!auth()->guest())
        <script> window.Laravel.userId = <?php echo auth()->user()->id; ?> </script>
    @endif
</head>
<body>
    <!-- // ... // -->
    @if (Auth::guest())
        <li><a href="{{ url('/login') }}">Login</a></li>
        <li><a href="{{ url('/register') }}">Register</a></li>
    @else
        <!-- // add this dropdown // -->
        <li class="dropdown">
            <a class="dropdown-toggle" id="notifications" data-toggle="dropdown" aria-haspopup="true" aria-expanded="true">
                <span class="glyphicon glyphicon-user"></span>
            </a>
            <ul class="dropdown-menu" aria-labelledby="notificationsMenu" id="notificationsMenu">
                <li class="dropdown-header">No notifications</li>
            </ul>
        </li>
<!-- // ... // --> 
```

> `resources/views/layouts/app.blade.php`

我们还在脚本中添加了一个全局`window.Laravel.userId`变量来获取当前用户的 ID。

#### JavaScript 和 SASS

我们将使用 Laravel Mix 来编译 JavaScript 和 SASS。首先，我们需要安装 npm 包。

```
npm install 
```

现在让我们将这段代码添加到`app.js`中:

```
window._ = require('lodash');
window.$ = window.jQuery = require('jquery');
require('bootstrap-sass');

var notifications = [];

const NOTIFICATION_TYPES = {
    follow: 'App\\Notifications\\UserFollowed'
}; 
```

> `app/resources/assets/js/app.js`

这只是一个初始化。我们将使用`notifications`来存储所有通知对象，无论它们是通过 AJAX 还是 Pusher 检索的。

你可能猜到了，`NOTIFICATION_TYPES`包含了通知的类型。

接下来，让我们通过 AJAX“获取”通知。

```
//...
$(document).ready(function() {
    // check if there's a logged in user
    if(Laravel.userId) {
        $.get('/notifications', function (data) {
            addNotifications(data, "#notifications");
        });
    }
});

function addNotifications(newNotifications, target) {
    notifications = _.concat(notifications, newNotifications);
    // show only last 5 notifications
    notifications.slice(0, 5);
    showNotifications(notifications, target);
} 
```

> `app/resources/assets/js/app.js`

这样，我们就可以从 API 中获取最新的通知，并将它们放入下拉列表中。

在`addNotifications`中，我们使用 Lodash 将当前通知与新通知连接起来，并只显示最近的 5 个通知。

我们还需要几个函数来完成这项工作。

```
//...
function showNotifications(notifications, target) {
    if(notifications.length) {
        var htmlElements = notifications.map(function (notification) {
            return makeNotification(notification);
        });
        $(target + 'Menu').html(htmlElements.join(''));
        $(target).addClass('has-notifications')
    } else {
        $(target + 'Menu').html('<li class="dropdown-header">No notifications</li>');
        $(target).removeClass('has-notifications');
    }
} 
```

> `app/resources/assets/js/app.js`

该函数构建一个包含所有通知的字符串，并将其放入下拉列表中。
如果没有收到通知，它只显示“没有通知”。

它还向下拉按钮添加了一个类，当通知存在时，这个类将改变它的颜色。有点像 Github 的通知。

最后，一些助手函数来生成通知字符串。

```
//...

// Make a single notification string
function makeNotification(notification) {
    var to = routeNotification(notification);
    var notificationText = makeNotificationText(notification);
    return '<li><a href="' + to + '">' + notificationText + '</a></li>';
}

// get the notification route based on it's type
function routeNotification(notification) {
    var to = '?read=' + notification.id;
    if(notification.type === NOTIFICATION_TYPES.follow) {
        to = 'users' + to;
    }
    return '/' + to;
}

// get the notification text based on it's type
function makeNotificationText(notification) {
    var text = '';
    if(notification.type === NOTIFICATION_TYPES.follow) {
        const name = notification.data.follower_name;
        text += '<strong>' + name + '</strong> followed you';
    }
    return text;
} 
```

> `app/resources/assets/js/app.js`

现在我们将把它添加到我们的`app.scss`文件中:

```
//... 
#notifications.has-notifications {
 color: #bf5329
} 
```

> `app/resources/assets/sass/app.scss`

让我们汇编资产:

```
npm run dev 
```

如果您现在尝试关注某个用户，他们会收到通知。当他们点击它时，他们会被重定向到`/users`，并且通知会消失。

### 新帖子通知

当用户创建新帖子时，我们会通知关注者。

让我们从生成通知类开始。

```
php artisan make:notification NewPost 
```

让我们按如下方式更新生成的类:

```
// ..
use App\Post;
use App\User;
class NewArticle extends Notification implements ShouldQueue
{
    // ..
    protected $following;
    protected $post;

    public function __construct(User $following, Post $post)
    {
        $this->following = $following;
        $this->post = $post;
    }

    public function via($notifiable)
    {
        return ['database'];
    }

    public function toDatabase($notifiable)
    {
        return [
            'following_id' => $this->following->id,
            'following_name' => $this->following->name,
            'post_id' => $this->post->id,
        ];
    }
} 
```

> `app/Notifications/NewArticle.php`

接下来，我们需要发送通知。有几种方法可以做到这一点。我喜欢用雄辩的观察家 T2。

让我们做一个`Post`的观察者，听听它的事件。我们将创建一个新的类:`app/Observers/PostObserver.php`

```
namespace App\Observers;

use App\Notifications\NewPost;
use App\Post;

class PostObserver
{
    public function created(Post $post)
    {
        $user = $post->user;
        foreach ($user->followers as $follower) {
            $follower->notify(new NewPost($user, $post));
        }
    }
} 
```

然后，在`AppServiceProvider`中登记观察者:

```
//...
use App\Observers\PostObserver;
use App\Post;

class AppServiceProvider extends ServiceProvider
{
    //...
    public function boot()
    {
        Post::observe(PostObserver::class);
    }
    //...
} 
```

> `app/Providers/AppServiceProvider.php`

现在我们只需要将消息格式化，以 JS 格式显示:

```
// ...
const NOTIFICATION_TYPES = {
    follow: 'App\\Notifications\\UserFollowed',
    newPost: 'App\\Notifications\\NewPost'
};
//...
function routeNotification(notification) {
    var to = `?read=${notification.id}`;
    if(notification.type === NOTIFICATION_TYPES.follow) {
        to = 'users' + to;
    } else if(notification.type === NOTIFICATION_TYPES.newPost) {
        const postId = notification.data.post_id;
        to = `posts/${postId}` + to;
    }
    return '/' + to;
}

function makeNotificationText(notification) {
    var text = '';
    if(notification.type === NOTIFICATION_TYPES.follow) {
        const name = notification.data.follower_name;
        text += `<strong>${name}</strong> followed you`;
    } else if(notification.type === NOTIFICATION_TYPES.newPost) {
        const name = notification.data.following_name;
        text += `<strong>${name}</strong> published a post`;
    }
    return text;
} 
```

> `app/resources/assets/js/app.js`

瞧！用户会收到关于关注和新帖子的通知！去试试吧！

### 使用推动器进行实时操作

是时候使用 Pusher 通过 websockets 实时获取通知了。

在[pusher.com](https://pusher.com)注册一个免费推送账户，并创建一个新应用。

```
...

BROADCAST_DRIVER=pusher
PUSHER_KEY=
PUSHER_SECRET=
PUSHER_APP_ID= 
```

在`broadcasting`配置文件中设置您的帐户选项:

```
 //...
    'connections' => [

            'pusher' => [
                //...
                'options' => [
                    'cluster' => 'eu',
                    'encrypted' => true
                ],
            ],
    //... 
```

> `config/broadcasting.php`

然后我们将在 providers 数组中注册`App\Providers\BroadcastServiceProvider`。

```
// ...
'providers' => [
    // ...
    App\Providers\BroadcastServiceProvider
    //...
],
//... 
```

> `config/app.php`

我们现在应该安装 Pusher 的 PHP SDK 和 Laravel Echo:

```
composer require pusher/pusher-php-server 
```

```
npm install --save laravel-echo pusher-js 
```

我们必须设置要广播的通知数据。让我们更新一下`UserFollowed`通知:

```
//...
class UserFollowed extends Notification implements ShouldQueue
{
    // ..
    public function via($notifiable)
    {
        return ['database', 'broadcast'];
    }
    //...
    public function toArray($notifiable)
    {
        return [
            'id' => $this->id,
            'read_at' => null,
            'data' => [
                'follower_id' => $this->follower->id,
                'follower_name' => $this->follower->name,
            ],
        ];
    }
} 
```

> `app/Notifications/UserFollowed.php`

和`NewPost`:

```
//...
class NewPost extends Notification implements ShouldQueue
{

    //...
    public function via($notifiable)
    {
        return ['database', 'broadcast'];
    }
    //...
    public function toArray($notifiable)
    {
        return [
            'id' => $this->id,
            'read_at' => null,
            'data' => [
                'following_id' => $this->following->id,
                'following_name' => $this->following->name,
                'post_id' => $this->post->id,
            ],
        ];
    }
} 
```

> `app/Notifications/NewPost.php`

我们需要做的最后一件事是更新我们的 JS。打开`app.js`并添加以下代码

```
// ...
window.Pusher = require('pusher-js');
import Echo from "laravel-echo";

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: 'your-pusher-key',
    cluster: 'eu',
    encrypted: true
});

var notifications = [];

//...

$(document).ready(function() {
    if(Laravel.userId) {
        //...
        window.Echo.private(`App.User.${Laravel.userId}`)
            .notification((notification) => {
                addNotifications([notification], '#notifications');
            });
    }
}); 
```

> `app/resources/assets/js/app.js`

我们到此为止。通知是实时添加的。你现在可以玩这个应用程序，看看通知是如何更新的。

![Demo](img/f9d2cb575c6859a27bece42a3abb7ab7.png)

## 结论

Pusher 有一个非常简单的 API，使得接收实时事件变得非常容易。结合 Laravel 通知，我们可以通过多种渠道(电子邮件、短信、Slack 等)发送通知。)从一个地方。在本教程中，我们向一个简单的博客添加了用户跟踪功能，并使用前面提到的工具对其进行了增强，以获得一些流畅的实时功能。

Pusher 和 Laravel 通知还有很多功能:这些服务允许你向浏览器、手机和 IOT 设备实时发送 pub/sub 消息。还有一个在线 API 来获取用户的在线/离线状态。

请查看他们各自的文档([推手文档](https://pusher.com/docs)、[推手教程](https://pusher.com/tutorials)、[拉腊维尔文档](https://laravel.com/docs/5.4/notifications))，更深入地探索他们，发挥他们的真正潜力。

让我听听你用这些技术在评论中创造了什么。

## 分享这篇文章