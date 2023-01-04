# 具有 Laravel 5.1 和事件广播的实时应用

> 原文：<https://www.sitepoint.com/real-time-apps-laravel-5-1-event-broadcasting/>

在 Laravel 5.1 中，框架包含了一个名为广播事件的功能，使得用 PHP 创建实时应用程序变得很容易。有了这一新功能，应用程序可以将事件发布到各种基于云的实时 PubSub 解决方案，如 [Pusher](https://pusher.com/) 或 Redis。

![Laravel 5.1](img/1a2135e5e468e1b1379b2839dcc65187.png)

在本文中，我们研究了一个简单的 todo 应用程序，并使用 Laravel 的广播事件将其转换为一个实时应用程序。

## 下载并安装应用程序

最简单的入门方法是启动一个实例 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 。如果你不想使用 Homestead Improved，你必须在你的系统上安装 [git](https://www.sitepoint.com/git-for-beginners/) 和 [composer](https://www.sitepoint.com/php-dependency-management-with-composer/) 。

我们将通过克隆一个 git repo 把启动代码放在一个名为`todo-app`的目录中。

```
git clone https://github.com/cwt137/l51-todo-app todo-app
```

克隆完成后，进入`todo-app`目录。我们需要通过运行以下命令来安装所有的应用程序依赖项(根据您的编写器的设置，该命令可能会略有不同):

```
composer install
```

安装完所有的依赖项后，我们必须设置我们的数据库。

```
php artisan migrate
```

## 测试非实时应用程序

这个应用程序现在是功能性的，但是没有 Laravel 的广播事件所提供的实时功能。在两个浏览器中打开主页，并把它们放在一起，这样你就可以做一个比较。如果没有两个不同的浏览器，可以使用两个浏览器窗口。

在第一个浏览器窗口中操作待办事项列表。然后在第二个浏览器窗口中执行一些操作。您会注意到，如果不点击刷新按钮，您不在其中的另一个浏览器窗口不会更新。这是因为没有实时功能。再补充一些吧。

## 为应用添加实时功能

添加实时功能将允许两个浏览器窗口更新其内容，而无需等待点击刷新按钮。

在这个例子中，我们将定义三个 Laravel 事件类，它们在应用程序中的不同时间被触发。一个事件是创建新项目时触发的`ItemCreated`事件。第二个事件是当一个项目被更新(被标记为完成或未完成)时触发的`ItemUpdated`事件。最后一个是当一个项目从待办事项列表中删除时触发的`ItemDeleted`事件。

### 广播事件

为了广播事件，我们将创建一个常规的 Laravel 事件，但是我们将实现一个名为`ShouldBroadcast`的接口。如果 Laravel 看到一个事件类实现了`ShouldBroadcast`，它就知道这是一个广播事件。这个接口要求我们在事件类中定义一个名为`broadcastOn`的方法。它应该返回一个字符串数组，这些字符串是应该广播该事件的频道。

要创建我们需要的事件类，运行一些 artisan 命令

```
php artisan make:event ItemCreated
php artisan make:event ItemUpdated
php artisan make:event ItemDeleted
```

打开`app/Events/ItemCreated.php`，用以下代码替换内容:

```
<?php

namespace App\Events;

use App\Item;
use App\Events\Event;
use Illuminate\Queue\SerializesModels;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;

class ItemCreated extends Event implements ShouldBroadcast
{
    use SerializesModels;

    public $id;

    /**
     * Create a new event instance.
     *
     * @param Item $item
     * @return void
     */
    public function __construct(Item $item)
    {
        $this->id = $item->id;
    }

    /**
     * Get the channels the event should be broadcast on.
     *
     * @return array
     */
    public function broadcastOn()
    {
        return ['itemAction'];
    }
}
```

Laravel 的事件系统会将这个对象序列化，并使用`itemAction`通道在实时云系统上广播出来。接下来的几个事件与第一个类似。

打开`app/Events/ItemUpdated.php`，用以下代码替换内容:

```
<?php

namespace App\Events;

use App\Item;
use App\Events\Event;
use Illuminate\Queue\SerializesModels;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;

class ItemUpdated extends Event implements ShouldBroadcast
{
    use SerializesModels;

    public $id;
    public $isCompleted;

    /**
     * Create a new event instance.
     *
     * @param Item $item
     * @return void
     */
    public function __construct(Item $item)
    {
        $this->id = $item->id; 
        $this->isCompleted = (bool) $item->isCompleted;
    }

    /**
     * Get the channels the event should be broadcast on.
     *
     * @return array
     */
    public function broadcastOn()
    {
        return ['itemAction'];
    }
}
```

打开`app/Events/ItemDeleted.php`，用以下代码替换内容:

```
<?php

namespace App\Events;

use App\Item;
use App\Events\Event;
use Illuminate\Queue\SerializesModels;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;

class ItemDeleted extends Event implements ShouldBroadcast
{
    use SerializesModels;

    public $id;

    /**
     * Create a new event instance.
     *
     * @param Item $item
     * @return void
     */
    public function __construct(Item $item)
    {
        $this->id = $item->id;
    }

    /**
     * Get the channels the event should be broadcast on.
     *
     * @return array
     */
    public function broadcastOn()
    {
        return ['itemAction'];
    }
}
```

### 数据库事件

我们可以在几个地方为我们的项目触发事件。我们可以在控制器内部，或者在数据库事件触发器内部完成。在这个例子中，我们将使用数据库触发器，因为它对我来说更自然，不会用额外的东西填满控制器。此外，通过将事件触发逻辑放在数据库层中，可以在构建命令行程序或作为 cron 作业构建应用程序时触发事件。

每当一个模型被创建、在更新后被保存或者被删除时，数据库库 courage 就触发事件。我们将收听这些事件，这样我们就可以启动我们自己的广播事件。这将通过服务提供商来完成。

在`app/Providers/AppServiceProvider.php`处打开文件，并用以下代码替换内容:

```
<?php

namespace App\Providers;

use Event;
use App\Item;
use App\Events\ItemCreated;
use App\Events\ItemUpdated;
use App\Events\ItemDeleted;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Item::created(function ($item) {
            Event::fire(new ItemCreated($item));
        });

        Item::updated(function ($item) {
            Event::fire(new ItemUpdated($item));
        });

        Item::deleted(function ($item) {
            Event::fire(new ItemDeleted($item));
        });
    }

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        //
    }
}
```

### 推进器

![](img/ec1b5990ccdcc767f5aaf336cc126f3f.png)

我们将在这个例子中使用的实时云服务是 Pusher。Laravel 内置了对该服务的支持，并且最容易设置。

#### 登记

我们必须注册一个帐户来获得一组凭证。在[推送网站](https://pusher.com/)注册后，进入管理界面，创建一个名为`todo-app`的新应用。记下`app_id`、`key`和`secret`。我们以后会需要的。

#### Pusher PHP 服务器库

为了让我们的应用程序使用 Pusher，我们必须将服务器库添加到我们的项目中。这是通过 composer 完成的。

```
composer require 'pusher/pusher-php-server:2.2.1'
```

#### Java Script 语言

我们将在主页底部添加一些 JavaScript。打开主页的 blade 模板`resources/views/index.blade.php`，将下面的代码放在 body 标签的前面

```
<script src="//js.pusher.com/2.2/pusher.min.js"></script>
<script> var pusher = new Pusher("{{ env(PUSHER_KEY) }}"); </script>
<script src="js/pusher.js"></script>
```

上面的代码加载 Pusher Javascript 客户端库，通过将我们的键传递到构造函数来实例化 Pusher 对象，并加载我们的特定于 Pusher 的应用程序逻辑。

在 starter 应用程序中，根据页面上发生的事件(表单提交、点击删除图标等)删除和添加项目。这就是为什么当我们打开两个浏览器窗口时，两个窗口都没有更新。一个浏览器窗口看不到另一个浏览器窗口中正在进行的操作。为了使应用程序实时，我们将根据来自 Pusher 的事件添加和删除项目。但是首先，打开`public/js/app.js`，注释掉对`addItem()`和`removeItem()`函数的所有调用。确保不要删除文件顶部的两个函数声明。

创建文件`public/js/pusher.js`并将以下代码放入其中。

```
( function( $, pusher, addItem, removeItem ) {

var itemActionChannel = pusher.subscribe( 'itemAction' );

itemActionChannel.bind( "App\\Events\\ItemCreated", function( data ) {

    addItem( data.id, false );
} );

itemActionChannel.bind( "App\\Events\\ItemUpdated", function( data ) {

    removeItem( data.id );
    addItem( data.id, data.isCompleted );
} );

itemActionChannel.bind( "App\\Events\\ItemDeleted", function( data ) {

    removeItem( data.id );
} );

} )( jQuery, pusher, addItem, removeItem);
```

app 订阅`itemAction`频道。默认情况下，Lavavel 使用事件对象的限定类名作为 Pusher 事件名称。上面的 Javascript 代码为三个事件`App\Events\ItemCreated`、`App\Events\ItemUpdated`和`App\Events\ItemDeleted`设置了监听器。有回调来处理这些事件被触发时发生的事情。正如你所看到的，对`getItem`和`removeItem`的调用被移到了各种事件的回调中。所以现在，项目的添加或删除是基于 Pusher 事件，而不是用户事件。

## 测试实时应用

要测试这个应用程序，你需要为 Pusher 设置按键。默认情况下，Laravel 会查看环境变量来获取 Pusher 信息。打开`.env`文件，将以下几行放在底部。

```
PUSHER_KEY=YOUR_PUSHER_KEY
PUSHER_SECRET=YOUR_PUSHER_SECRET
PUSHER_APP_ID=YOUR_PUSHER_APP_ID
```

这将设置变量。其中`YOUR_PUSHER_KEY`、`YOUR_PUSHER_SECRET`、`YOUR_PUSHER_APP_ID`分别是您的推手分配的`key`、`secret`和`app_id`。

再次打开两个浏览器窗口，并在其中一个窗口中操作待办事项列表。您将看到列表在另一个浏览器窗口中自动更新，而无需点击刷新按钮。

## 最后的想法

虽然本文没有涉及，但是所使用的框架是可扩展的，如果 Laravel 不支持您的实时解决方案，可能已经有一个带有适当广播驱动程序的 composer 包。或者，您可以创建自己的广播驱动程序，并由服务提供商来加载它。

有了 Laravel 5.1 内置的新广播事件功能，PHP 开发人员现在可以更容易地创建实时应用程序。这种新的实时功能释放了许多可能性，这些可能性只有为 Node.js 等其他平台编写的应用程序才具备。

## 分享这篇文章