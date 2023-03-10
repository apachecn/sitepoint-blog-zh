# IronMQ 和 Laravel:设置

> 原文：<https://www.sitepoint.com/ironmq-laravel-setup/>

这个由两部分组成的文章系列旨在让初学者理解如何在 Laravel 中使用推送队列。简单概述一下，我们将看到的最终解决方案是一个简单的上传照片和调整照片大小的表单。调整图像大小是一项耗时的任务，因此我们可以在后台完成，而不是让用户一直等到图像大小调整完毕。同时，我们将学习使用一个叫做 **[ngrok](https://www.sitepoint.com/use-ngrok-test-local-site/)** 的工具，这样我们就可以在本地系统中使用队列。

GitHub 上的[提供了源代码。](https://github.com/phpmasterdotcom/laravel-queues)

你也可以在一个合适的服务器上试试。

## 队列和熨斗

队列只不过是一个管道，我们在其中排列我们的作业，并按照它们被插入的顺序执行它们。假设当前我们的队列是空的，我们将作业 A 放入其中，然后放入作业 B 和作业 c，我们以相同的顺序读取队列。首先，我们从队列中取出作业 A 并完成它，然后处理下一个作业。如果您关注“读取”部分，我们总是需要检查我们的队列，看看是否有新的工作发布。如果队列本身在作业可用时通知我们，不是更好吗？像这样的队列被称为推送队列，这就是我们将在 [IronMQ](http://www.iron.io/) 中使用的。

Iron MQ 是一项在使用队列时让我们的生活变得更轻松的服务。当您创建推送队列时，您还需要向该队列提交一个订户。订户只不过是一个 URL，当队列中有一个作业可用时，就会调用这个 URL，其中包含最初提供给该作业的数据。

要了解更多关于作业队列的信息，并查看不同解决方案之间的比较，请参见本文。

## 设置和安装

在这一步中，我们通过 composer 安装 Laravel 和所有需要的依赖项。我们还在 Iron 上创建了一个帐户，并安装了一个名为 ngrok 的工具。

#### 拉勒韦尔

1.  [安装作曲者](https://getcomposer.org/doc/00-intro.md#installation-nix)。

2.  安装 Laravel

    `composer create-project laravel/laravel --prefer-dist`

    在这个命令之后，您应该会看到文件夹“laravel”。在您喜欢的文本编辑器或 IDE 中打开 laravel 文件夹。

    通过命令行进入该文件夹并运行:

    `php artisan serve`

    此时，如果您打开 [http://localhost:8000](http://localhost:8000) ，您应该会看到您的 Laravel 安装的主页。

3.  **建立数据库**:我们将使用 MySQL 作为我们的数据库。通过命令行、phpmyadmin 或您喜欢的任何工具创建一个数据库。创建数据库后，转到`app/config/app.php`并找到如下所示的部分:

    ```
    'mysql' => array(
        'driver'    => 'mysql',
        'host'      => 'localhost',
        'database'  => 'database',
        'username'  => 'root',
        'password'  => '',
        'charset'   => 'utf8',
        'collation' => 'utf8_unicode_ci',
        'prefix'    => '',
    ) 
    ```

    填写必填字段。假设您在本地系统上，您需要提供 MySQL 服务器的凭证(用户名、密码)和您创建的数据库的名称。

    运行`php artisan migrate:install`。如果您正确输入了凭据，它应该显示:迁移表创建成功。

4.  我们来做个小编。打开`app/views/hello.php`并找到以下行:

    ```
    <h1>You have arrived.</h1> 
    ```

    编辑成(可以改成任何东西！):

    ```
    <h1>I am learning how to use queues!</h1> 
    ```

    如果你打开 http://localhost:8000 ，你应该可以看到 Laravel 的标志和下面的文字:“我正在学习如何使用队列！”。

5.  打开 *composer.json* ，添加 iron(用于 iron 推送队列)和 intervention(用于图像操作)库。

    ```
    "require": {
        "laravel/framework": "4.1.*",
        "iron-io/iron_mq": "1.4.6",
        "intervention/image": "dev-master"
    }, 
    ```

    更新代码

    ```
    composer update 
    ```

6.  按照以下说明完成干预装置的安装。

    打开您的 Laravel 配置文件`config/app.php`。在`$providers`数组中添加这个包的服务提供者:`'Intervention\Image\ImageServiceProvider',`，然后将这个包的外观添加到`$aliases`数组:`'Image' => 'Intervention\Image\Facades\Image',`

    参考:[http://intervention . olivervogel . net/image/getting _ started/laravel](http://intervention.olivervogel.net/image/getting_started/laravel)

#### ngrok

在生产中，用户通过类似于[http://example.com](http://example.com)的 URL 访问你的网站。但是，大家都知道，我们的本地主机并不适用于整个互联网，因为我们将使用 Iron 来实现队列，所以我们需要将代码托管在服务器中，以便我们的网站可以被 Iron 访问。这样做意味着，每次我们对代码进行更改时，我们都必须将其上传到服务器，并测试它是否如我们预期的那样工作。如果我们能在本地系统上测试我们的代码不是更好吗？这就是为什么我们使用一个名为 ngrok 的工具，它使我们的本地主机可以通过一个特殊的 URL 访问整个互联网。我们将马上安装和使用它。

1.  Go to [ngrok](https://ngrok.com/) .

2.  下载并安装 ngrok。跟随-[如何安装 ngrok](https://ngrok.com/download) 。

    这些步骤是:

    *   下载适用于您的操作系统的 zip 文件。
    *   解压缩下载的 zip 文件
    *   您应该能够从命令行运行`./ngrok --help`。
3.  让我们让本地主机在互联网上可见。

    对于这一步，我们假设 Laravel 仍然在`http://localhost:8000`运行。(如果不是，从 Laravel 文件夹根目录下的命令行运行`php artisan serve`)

    (您需要通过另一个终端运行 ngrok 命令，因为有一个终端`php artisan serve`需要运行)

    ```
    ./ngrok 8000 
    ```

    我将端口设为 8000，因为我们的 laravel 运行在 8000 上。运行该命令后，我得到了回复:

    ```
    Tunnel Status                 online                                            
    Version                       1.6/1.5                                           
    Forwarding                    http://953ffbb.ngrok.com -> 127.0.0.1:8000        
    Forwarding                    https://953ffbb.ngrok.com -> 127.0.0.1:8000       
    Web Interface                 127.0.0.1:4040                                    
    # Conn                        2                                                 
    Avg Conn Time                 167.38ms 
    ```

    **注意**:每次运行 ngrok 得到的响应都不一样。所以，你在运行`./ngrok 8000`后看到的响应将与我在上面发布的不同。

    观察“转发”下的网址:[http://953ffbb.ngrok.com](http://953ffbb.ngrok.com)。如果你打开那个网址(对你来说是一个不同的网址)，你应该能看到你的网站上有 Laravel 的标志和你写的文字，例如:“我正在学习如何使用队列！”。

    我建议你继续运行 ngrok 并记下转发 URL，因为我们将在本教程中使用它。

    如果你碰巧在这中间重启了 ngrok，你的 URL 就会改变。这根本不是问题！只要在需要的地方使用不同的 URL。

#### 铁 MQ

在我们的项目中，我们将使用 [Iron](http://www.iron.io/) 来实现队列。要使用 Iron，我们首先需要创建一个帐户。

1.  前往[http://www.iron.io/](http://www.iron.io/)并登录

2.  创建新项目。我将我的项目命名为“Laravel Queues”。

3.  您可以在项目名称旁边看到一个“Key”按钮。点击它，将“令牌”和“项目 ID”复制到`app/config/queue.php`。

    *   **将默认值更改为“铁”**

        ```
        'default' => 'iron', 
        ```

    *   **找到下面的部分:**

        ```
        'iron' => array(
            'driver'  => 'iron',
            'project' => 'your-project-id',
            'token'   => 'your-token',
            'queue'   => 'your-queue-name',
        ) 
        ```

        从仪表板上复制您的项目 ID 和令牌并粘贴到这里，将您的队列命名为“laravel”。

        ```
        'iron' => array(
            'driver'  => 'iron',
            'project' => 'your-project-id',
            'token'   => 'your-token',
            'queue'   => 'laravel',
        ) 
        ```

4.  如上所述，我们需要为我们的队列添加一个订户。您可以前往 [Laravel 推送队列](http://laravel.com/docs/queues#push-queues)查看我们现在要关注的官方文档:

    *   我们必须使用下面的 artisan 命令*注册一个推送队列订阅者(我们必须从我们的项目根文件夹运行这个命令)*

        根据上面的引用，我们将运行`php artisan queue:subscribe laravel http://953ffbb.ngrok.com/queue/receive`

        注意，我使用的 URL 是 ngrok 生成的**转发 URL** 。您应该会看到与下面类似的回复:

        ```
        Queue subscriber added: http://953ffbb.ngrok.com/queue/receive
        ```

        如果您查看您的仪表板并点击“MQ ”,您将进入项目页面。点击“Queues”，找到一个名为“laravel”的队列。如果您单击一个队列名称，例如 laravel，您可以在 Push Queues 选项卡中看到一个订户列表，并在它下面找到`http://953ffbb.ngrok.com/queue/receive`。

    *   将该代码放入`app/routes.php`

        ```
        Route::post('queue/receive', function()
        {
            return Queue::marshal();
        }); 
        ```

来自官方文件:

> marshal 方法将负责触发正确的作业处理程序类。要将作业发送到推送队列，只需使用与传统队列相同的 Queue::push 方法。

#### 包扎

在这一部分中:

1.  我们已经安装了带有 Iron 和干预库的 Laravel。我们的 laravel 跑在`http://localhost:8000`

2.  我们安装了 ngrok，并通过为我们生成的转发 Url(让 ngrok 在终端中运行)将我们的本地主机暴露给整个互联网。

3.  我们用 Laravel 配置了 Iron，并创建了一个带有订户的队列。

在接下来的部分中，我们将做繁重的工作，并弄脏我们的手。我们将构建我们的应用程序，并完全实现作业逻辑。敬请期待！

## 分享这篇文章