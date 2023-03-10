# 如何用 RestDB 构建基本的 Twitter 分析应用

> 原文：<https://www.sitepoint.com/twitter-analytics-restdb/>

![How to Build a Basic Twitter Analytics App with RestDB](img/5a6beb4a94cb7537a8e27adaa8563f7f.png)

*本文由 [RestDB](https://restdb.io/) 赞助。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

Twitter Analytics 是一个有用的工具——它可以让你关注哪些有用，哪些没用，并尝试自己的方式，获得令人羡慕的追随者数量。

但是，如果您想使用自己帐户中的数据来执行一些更符合您特定需求的分析，该怎么办呢？这并不特别容易——但这是可能的。我们将使用 RestDB 来帮助我们获取和存储数据，并看看我们的追随者发生了什么。

你是否经常想知道为什么有些账户似乎关注了你，却在几分钟(或几天)后又不关注你了？可能不是你说的那样——他们只是*跟随者耕作*。

跟随者农业是一种众所周知的社交媒体黑客，它利用了那些一旦有人追随他们就#followback 的人。大品牌、名人和想成为名人的人利用了这一点，因为这使他们的追随者数量高，但追随者数量低，从而使他们看起来受欢迎。

![Outline of twitter logo](img/6a5587b53574bc028d61928f480f2317.png)

在这篇文章中，我们将构建一个应用程序，让您通过 Twitter 登录，获取您的关注者，并将上次获取的关注者列表与刷新的列表进行比较，以识别新的不关注者，并计算他们关注的持续时间，可能会自动识别农民。

## 拔靴带

像往常一样，我们将使用[家园改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)进行高质量的本地环境设置。如果你有一个你觉得舒服的设置，请随意使用你自己的设置。

```
git clone https://github.com/swader/homestead_improved hi_followfarmers
cd hi_followfarmers
bin/folderfix.sh
vagrant up; vagrant ssh 
```

一旦虚拟机配置完毕，我们发现自己也在其中，让我们启动一个 [Laravel](https://www.sitepoint.com/premium/courses/laravel-5-2930?aref=bskvorc) 应用程序。

```
composer create-project --prefer-dist laravel/laravel Code/Project
cd Code/Project 
```

## 使用 Twitter 登录

为了使用 Twitter 登录成为可能，我们将使用[sociate](https://www.sitepoint.com/easily-add-social-logins-to-your-app-with-socialite/)包。

```
composer require laravel/socialite 
```

根据说明，我们还应将其登记在`config/app.php`:

```
'providers' => [
    // Other service providers...

    Laravel\Socialite\SocialiteServiceProvider::class,
], 
```

```
'Socialite' => Laravel\Socialite\Facades\Socialite::class, 
```

最后，我们需要在 http://apps.twitter.com/app/new 注册一个新的 Twitter 应用程序

![Registering a new Twitter app](img/6f347fd98dce8ba028bbde15e7e6af69.png)

…并将秘密凭证添加到`config/services.php`:

```
 'twitter' => [
        'client_id' => env('TWITTER_CLIENT_ID'),
        'client_secret' => env('TWITTER_CLIENT_SECRET'),
        'redirect' => env('TWITTER_CALLBACK_URL'),
    ], 
```

自然，我们需要将这些环境变量添加到项目根目录下的`.env`文件中:

```
TWITTER_CLIENT_ID=keykeykeykeykeykeykeykeykey
TWITTER_CLIENT_SECRET=secretsecretsecret
TWITTER_CALLBACK_URL=http://homestead.app/auth/twitter/callback 
```

接下来，我们需要在`routes/web.php`中添加一些登录路径:

```
Route::get('auth/twitter', 'Auth\LoginController@redirectToProvider');
Route::get('auth/twitter/callback', 'Auth\LoginController@handleProviderCallback'); 
```

最后，让我们将这些路由引用的方法添加到`app/Http/Controllers/Auth`内的`LoginController`类中:

```
 /**
     * Redirect the user to the GitHub authentication page.
     *
     * @return Response
     */
    public function redirectToProvider()
    {
        return Socialite::driver('twitter')->redirect();
    }

    /**
     * Obtain the user information from GitHub.
     *
     * @return Response
     */
    public function handleProviderCallback()
    {
        $user = Socialite::driver('twitter')->user();

        dd($user);
    } 
```

`dd($user);`是为了方便测试认证是否顺利，当然，如果你访问`/auth/twitter`，你应该能够授权应用程序，并在屏幕上看到你的账户的基本信息:

![Basic Twitter User Information](img/bb34456cd8537e3e1bec46501542d505.png)

## 关注者列表

有很多方法可以获得一个帐户的追随者名单，但没有一个是令人愉快的。

### Twitter 仍然讨厌开发者

自从 Twitter 对开发者的[大战](https://www.engadget.com/2015/07/14/twitter-rethinks-developer-stance/)(剧透:[自从那篇文章发表以来](https://blog.twitter.com/2017/building-the-future-of-the-twitter-api-platform)几乎没有什么变化)，获取人们的追随者的完整列表就成了一场彻头彻尾的噩梦。事实上，API 费率限制如此之低，以至于人们求助于第三方数据聚合器来购买数据，甚至抓取关注者页面。我们将走“白帽子”路线，并通过他们的 API 遭受损失，但如果你有其他获得追随者的方法，请随意使用，而不是下面概述的方法。

Twitter API 提供了 [`/followers/list`](https://dev.twitter.com/rest/reference/get/followers/list) 端点，但是由于每个调用最多只返回 20 个关注者，并且每 15 分钟只允许 15 个请求，我们每小时最多只能提取 1200 个关注者——这是不可接受的。相反，我们将使用 [`followers/ids`](https://dev.twitter.com/rest/reference/get/followers/ids) 端点一次获取 5000 个 id。这同样受到每 15 分钟 15 个电话的限制，但给了我们更多的喘息空间。

记住 ID 很重要！= Twitter 句柄。id 是数值，代表一段时间内的唯一帐户，甚至跨不同的句柄。因此，对于每个取消关注者的 ID，我们必须进行一次额外的 API 调用来找出他们是谁(用户查找批量 API 将会派上用场)。

### 基本 API 通信

社交名媛只对登录有用。实际上，与 API 的通信并不简单。鉴于 Laravel 预装了 [Guzzle](https://www.sitepoint.com/blog/) ,安装 Guzzle 的 Oauth 订户(允许我们通过 Oauth1 协议使用 Guzzle)是最简单的解决方案:

```
composer require guzzlehttp/oauth-subscriber 
```

一旦有了这些，我们就可以更新我们的`LoginController::handleProviderCallback`方法来进行测试:

```
 public function handleProviderCallback()
    {
        $user = Socialite::driver('twitter')->user();

        $stack = HandlerStack::create();

        $middleware = new Oauth1([
            'consumer_key' => getenv('TWITTER_CLIENT_ID'),
            'consumer_secret' => getenv('TWITTER_CLIENT_SECRET'),
            'token' => $user->token,
            'token_secret' => $user->tokenSecret
        ]);

        $stack->push($middleware);

        $client = new Client([
            'base_uri' => 'https://api.twitter.com/1.1/',
            'handler' => $stack,
            'auth' => 'oauth'
        ]);

        $response = $client->get('followers/ids.json', [
            'query' => [
                'cursor' => '-1',
                'screen_name' => $user->nickname,
                'count' => 5000
            ]
        ]);

        dd($response->getBody()->getContents());
    } 
```

在上面的代码中，我们首先创建一个中间件堆栈，它将仔细检查请求，通过所有的中间件，并输出最终版本。我们可以将其他中间件放入这个堆栈，但是现在，我们只需要 Oauth1。

接下来，我们创建 Oauth1 中间件，并传入所需的参数。我们已经得到了前两个——它们是我们之前在`.env`中定义的键。最后两个是从经过身份验证的 Twitter 用户实例中获得的。

然后，我们将中间件推入堆栈，并将堆栈附加到 Guzzle 客户端。通俗地说，这意味着“当这个客户端发出请求时，在将请求发送到它们的最终目的地之前，通过栈中的所有中间件提取请求”。我们还告诉客户端总是使用 oauth 进行身份验证。

最后，我们使用所需的查询参数对 API 端点进行 GET 调用:要开始的页面(-1 是第一页)、要拉追随者的用户以及要拉多少追随者。最后，我们将这个输出显示在屏幕上，看看我们是否得到了我们需要的东西。果然，这是我账户最近的 5000 名粉丝:

![Screenshot of 5000 Twitter user IDs](img/a2c0a5f438f2e3b7f6be2c74d603596a.png)

现在我们知道我们的 API 调用正在传递，我们可以和 Twitter 对话了，是时候让一些循环获得当前用户的完整列表了。

### PHP 方面——获得所有关注者

由于 API 允许每 15 分钟打 15 次电话，为了简单起见，我们现在将帐户大小限制为 7 万个追随者。

```
 $user = Socialite::driver('twitter')->user();

        if ($user->user['followers_count'] > 70000) {
            return view(
                'home.index',
                ['message' => 'Sorry, we currently only support accounts with up to 70k followers']
            );
        } 
```

*注意:`home.index`是我为这个例子制作的任意视图文件，包含一个指令:`{{ $message }}`。*

然后，我们遍历 API 返回的`next_cursor_string`值，通过其他 id 分页。

![Wow, much numbers, very follow, wow.](img/112f6d2d60f8fb20c12866e65cda29b1.png)

很多数字，非常符合，哇。

幸运的话，这应该会很快执行——取决于 Twitter 的 API 响应能力。

拥有多达 7 万名追随者的每个人现在都可以获得授权后生成的追随者的完整列表。

如果我们需要支持更大的账户，相对简单的做法是每隔 15 分钟(在 API 限制重置后)为每 75k 个关注者重复这个过程，并将结果缝合在一起。当然，在给定关注者数量的情况下，几乎肯定会有人在那个窗口中关注/不关注，所以很难保持准确。在这种情况下，更容易关注最后的 75k 个关注者，并且只分析这些关注者(API 根据最后关注者自动排序)，或者找到另一种可靠地获取关注者的方法，绕过 API。

### 清理

在 LoginController 中有这个逻辑有点尴尬，所以让我们把它移到一个单独的服务中。我为这个例子创建了`app/Services/Followers/Followers.php`，内容如下:

```
<?php

namespace App\Services\Followers;

use GuzzleHttp\Client;
use GuzzleHttp\HandlerStack;
use GuzzleHttp\Subscriber\Oauth\Oauth1;

class Followers
{

    /** @var string */
    protected $token;

    /** @var string */
    protected $tokenSecret;

    /** @var string */
    protected $nickname;

    /** @var Client */
    protected $client;

    public function __construct(string $token, string $tokenSecret, string $nickname)
    {
        $this->token = $token;
        $this->tokenSecret = $tokenSecret;
        $this->nickname = $nickname;

        $stack = HandlerStack::create();

        $middleware = new Oauth1(
            [
                'consumer_key' => getenv('TWITTER_CLIENT_ID'),
                'consumer_secret' => getenv('TWITTER_CLIENT_SECRET'),
                'token' => $this->token,
                'token_secret' => $this->tokenSecret,
            ]
        );

        $stack->push($middleware);

        $this->client = new Client(
            [
                'base_uri' => 'https://api.twitter.com/1.1/',
                'handler' => $stack,
                'auth' => 'oauth',
            ]
        );
    }

    public function getClient()
    {
        return $this->client;
    }

    /**
     * Returns an array of follower IDs for a given optional nickname.
     *
     * If no custom nickname is provided, the one used during the construction
     * of this service is used, usually defaulting to the same user authing
     * the application.
     *
     * @param string|null $nickname
     * @return array
     */
    public function getFollowerIds(string $nickname = null)
    {
        $nickname = $nickname ?? $this->nickname;

        $response = $this->client->get(
            'followers/ids.json', [
                'query' => [
                    'cursor' => '-1',
                    'screen_name' => $nickname,
                    'count' => 5000,
                ],
            ]
        );

        $data = json_decode($response->getBody()->getContents());
        $ids = $data->ids;

        while ($data->next_cursor_str !== "0") {

            $response = $this->client->get(
                'followers/ids.json', [
                    'query' => [
                        'cursor' => $data->next_cursor_str,
                        'screen_name' => $nickname,
                        'count' => 5000,
                    ],
                ]
            );
            $data = json_decode($response->getBody()->getContents());
            $ids = array_merge($ids, $data->ids);
        }

        return $ids;
    }

} 
```

然后我们可以清理 LoginController 的`handleProviderCallback`方法:

```
 public function handleProviderCallback()
    {
        $user = Socialite::driver('twitter')->user();

        if ($user->user['followers_count'] > 70000) {
            return view(
                'home.index',
                ['message' => 'Sorry, we currently only support accounts with up to 70k followers']
            );
        }

        $flwrs = new Followers(
            $user->token, $user->tokenSecret, $user->nickname
        );
        dd($flwrs->getFollowerIds());
    } 
```

这样做仍然是错误的方法，所以让我们进一步改进。为了保持用户登录，让我们将令牌、密码和昵称保存到会话中。

```
 /**
     * Get and store token data for authorized user.
     *
     * @param Request $request
     * @return Response
     */
    public function handleProviderCallback(Request $request)
    {
        $user = Socialite::driver('twitter')->user();

        if ($user->user['followers_count'] > 70000) {
            return view(
                'home.index',
                ['message' => 'Sorry, we currently only support accounts with up to 70k followers']
            );
        }

        $request->session()->put('twitter_token', $user->token);
        $request->session()->put('twitter_secret', $user->tokenSecret);
        $request->session()->put('twitter_nickname', $user->nickname);
        $request->session()->put('twitter_id', $user->id);

        return redirect('/');
    } 
```

我们将所有信息保存到会话中，使用户有效地登录到我们的应用程序，然后我们重定向到主页。

现在让我们创建一个新的控制器，并给它一个简单的使用方法:

```
artisan make:controller HomeController 
```

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class HomeController extends Controller
{
    public function index(Request $request)
    {
        $nick = $request->session()->get('twitter_nickname');
        if (!$nick) {
            return view('home.loggedout');
        }

        return view('home.index', $request->session()->all());
    }
} 
```

简单吧？观点也很简单:

```
{{--index.blade.php--}}
<h1>FollowerFarmers</h1>

<h2>Hello, {{ $twitter_nickname }}! Not you? <a href="/logout">Log out!</a></h2>

<p>I bet you'd like to see your follower stats, wouldn't you?</p> 
```

```
{{--loggedout.blade.php--}}
<h1>FollowerFarmers</h1>

<h2>Hello, stranger!</h2>

<p>You're currently logged out. How about you <a href="/auth/twitter">log in with Twitter </a> to get started?</p> 
```

我们还需要向`routes/web.php`添加一些路线:

```
Route::get('/', 'HomeController@index');
Route::get('/logout', 'Auth\LoginController@logout'); 
```

有了这个，我们可以检查我们是否登录，我们可以很容易地注销。

*注意，为了安全起见，注销路由应该只接受带有 CSRF 令牌的 POST 请求——为了开发过程中的简单性，我们采用 GET 方法，并在以后对其进行修改。*

诚然，这不是最漂亮的东西，但我们在这里建立一个演示——一旦逻辑完成，真实的东西可以得到视觉上的抛光。

### 注册服务提供商

通常的做法是注册一个服务提供商，以便以后更容易访问，所以让我们就这么做吧。我们的服务在没有令牌和秘密的情况下无法实例化(例如，在用户使用 Twitter 登录之前)，因此我们需要使它[延迟](https://laravel.com/docs/5.4/providers#deferred-providers)——换句话说，它将只在需要时被创建，并且我们将确保我们不需要它，直到我们有了那些值。

```
artisan make:provider FollowerServiceProvider 
```

```
<?php

namespace App\Providers;

use App\Services\Followers\Followers;
use Illuminate\Support\ServiceProvider;

class FollowerServiceProvider extends ServiceProvider
{

    protected $defer = true;

    public function register()
    {
        $this->app->singleton(
            Followers::class, function ($app) {
            return new Followers(
                session('twitter_token'), session('twitter_secret'),
                session('twitter_nickname')
            );
        }
        );
    }

    public function provides()
    {
        return [Followers::class];
    }
} 
```

如果我们在登录视图中放入一个简单的计数回显:

```
{{ count($ids) }} 
```

…并修改`HomeController`以现在使用该服务提供者:

```
...

        return view(
            'home.index', array_merge(
                $request->session()->all(),
                ['ids'=> resolve(Followers::class)->getFollowerIds()]
            )
        ); 
```

…然后我们测试了一下，果然有效。

![Basic views](img/6d709402ec3ea9b04109d7ddeccaf371.png)

## 数据库ˌ资料库

现在我们有了一个简洁的服务来提取关注者列表，我们可能应该把它们保存在某个地方。我们可以将它保存到本地 MySQL 数据库中，甚至是一个平面文件，但是为了性能和可移植性，我这次使用了不同的东西: [RestDB](http://restdb.io) 。

RestDB 是一个即插即用的托管数据库服务，易于配置和使用，让您可以自由选择托管平台。由于不需要写入本地文件系统的数据库，您可以轻松地将我们正在构建的应用程序推送到 Google Cloud Engine 或 Heroku。在[模板](https://restdb.io/templates)的帮助下，你可以立即建立一个博客，一个登陆页面，一个网络表单，一个日志分析器，甚至一个邮件系统——见鬼，该服务甚至支持在线编辑的 MarkDown，让你几乎可以在他们的服务上拥有一个基于 [MarkDown 的博客](https://restdb.io/blog/create-a-technical-blog-with-markdown-and-restdb)。

RestDB 有一个免费层，第一个月几乎没有限制，所以您可以彻底测试它。我正在开发的数据库基于一个基本的计划(由 RestDB 团队提供)。

### 设置 RestDB

与其他数据库服务不同，对于 RestDB，考虑记录数量限制很重要。基本计划提供了 10000 条记录，如果我们将每个登录用户的关注者保存为一个单独的条目，或者甚至将每个用户的关注者列表保存为每 15 分钟一个单独的条目，那么这些记录将很快耗尽。这就是我选择以下计划的原因:

*   每个新用户都将是`accounts`集合中的一条记录。
*   每个新的关注者列表将成为`follower-lists`集合中的一条记录，并且将成为`accounts`的[子记录](https://restdb.io/docs/parent-child-relations#restdb)。
*   最大频率为每 15 分钟(或更长时间，如果用户需要更长的时间来返回并登录到应用程序)，将生成一个新的列表，与上一个列表相比，一个新的列表以及与上一个列表的差异将被保存。
*   每个用户最多可以保存 100 条历史记录

也就是说，让我们根据[快速启动文档](https://restdb.io/docs/quick-start)创建新的`follower-lists`系列。创建集合后，让我们添加一些字段:

*   一个*必填* `followers` *正文*字段。文本字段支持正则表达式验证，由于我们将使用逗号分隔的列表来存储追随者 id，我们可以应用这样的正则表达式来确保数据始终有效:`^(\d+,\s?)*(\d+)$`。这将只匹配用逗号分隔的数字，但没有尾随逗号的行。你可以在这里看到它的动作[。](https://regex101.com/r/BL41ey/1)
*   一个*文本*类型的`diff_new`字段，将包含自上次输入以来的新关注者列表。将应用与`followers`相同的正则表达式限制，只是更新为可选的，因为有时与最后一个条目`(^(\d+,\s?)*(\d+)$)?`相比没有区别。
*   一个*文本*类型的`diff_gone`字段，它将包含自上次输入以来的未关注者列表。将应用与`diff_new`相同的正则表达式限制。

我们的系列应该是这样的:

![The followers collection](img/40e1e639b23cde5d70acebcfd98b8ef4.png)

现在让我们创建父集合:`accounts`。

*注意:你可能想知道为什么我们不直接使用内置的`users`集合。这是因为该收集仅用于[验证 Auth0 用户](https://restdb.io/docs/authentication-of-external-users#restdb)。那里的字段会对我们有用，但是根据文档，我们没有对那个数据库的写权限，我们需要它。那么为什么不直接用 Auth0 登录，用 RestDB 访问数据呢？请随意采取这种方法——我个人觉得依赖一个第三方服务来完成我的应用程序的关键部分就足够了，两个对我来说太多了。*

我们需要的字段是:

*   `twitter_id`，用户的 Twitter 账号 ID。*所需数量*。
*   `settings`，一个*必需的 JSON* 字段。这将保存所有用户的帐户特定设置，如刷新间隔，电子邮件频率等。

添加完这些之后，让我们添加一个新的`follower_lists`字段，并将其定义为`follower-lists`集合的子关系。在*属性*下，我们应该选择“的孩子”。这个名字有点令人困惑——尽管选项上写着“追随者列表的孩子”,但孩子是`follower-lists`。

您可能已经注意到我们没有在任何地方使用时间戳字段，比如`created_at`。这是因为 RestDB 会自动为每个集合创建它们，以及其他一些字段。要检查这些*系统字段*，点击每个集合设置表右上角的“显示系统字段”选项:

![System fields shown](img/38ebabcc420e64fc26b8c82354cc2f24.png)

当查询数据库时，在有效负载中获取这些字段需要我们在 API URLs 中使用`?metafields=true`参数。

我们现在准备开始结合 PHP 和 RestDB 端。

## 保存和读取 RestDB

为了能够与 RestDB 交互，我们需要一个 API 键。我们可以按照这里的说明[去拿。所有选项都应该保留默认值，启用所有 REST 方法。然后，密钥应保存到`.env`:](https://restdb.io/docs/apikeys-and-cors)

```
RESTDB_KEY=keykeykey 
```

`accounts`的想法如下:

*   当用户第一次授权 Twitter 时，应用程序将**读取**提供的 Twitter ID 的账户集合，如果不存在，它将**写入**一个新条目。
*   然后，用户被重定向到欢迎屏幕，该屏幕将包含一条确认帐户创建(如果已创建)的消息，并提供重定向到`/dashboard`。

让我们首先创建一个用于与数据库对话的 RestDB 服务。

```
<?php
// Services/Followers/RestDB.php

namespace App\Services\Followers;

use GuzzleHttp\Client;
use GuzzleHttp\ClientInterface;
use Psr\Http\Message\ResponseInterface;

class RestDB
{
    /** @var ClientInterface */
    protected $client;

    /**
     * Sets the Guzzle client to be used
     *
     * @param ClientInterface $client
     * @return $this
     */
    public function setClient(ClientInterface $client)
    {
        $this->client = $client;
        return $this;
    }

    /**
     * @return ClientInterface
     */
    public function getClient()
    {
        return $this->client;
    }

    /**
     * Configures a default Guzzle client so it doesn't need to be injected
     * @return $this
     */
    public function setDefaultClient()
    {
        $client = new Client([
            'base_uri' => 'https://followerfarmers-00df.restdb.io/rest/',
            'headers' => [
                'x-apikey' => getenv('RESTDB_KEY'),
                'content-type' => 'application/json'
            ]
        ]);
        $this->client = $client;
        return $this;
    }

    /**
     * Returns user's account entry if it exists. Caches result for 5 minutes
     * unless told to be `$fresh`.
     *
     * @param int $twitter_id
     * @param bool $fresh
     * @return bool|\stdClass
     */
    public function userAccount(int $twitter_id, bool $fresh = false)
    {
        /** @var ResponseInterface $request */
        $response = $this->client->get(
            'accounts', [
                'body' => '{"twitter_id": ' . $twitter_id . ', "max": 1}',
                'query' => ['metafields' => true],
                'headers' => ['cache-control' => $fresh ? 'no-cache' : 'max-age:300'],
            ]
        );

        $bodyString = json_decode($response->getBody()->getContents());

        if (empty($bodyString)) {
            return false;
        }

        return $bodyString[0];
    }

    /**
     * Creates a new account in RestDB.
     *
     * @param array $user
     * @return bool
     */
    public function createUserAccount(array $user)
    {
        /** @var ResponseInterface $request */
        $response = $this->client->post('accounts', [
            'body' => json_encode([
                'twitter_id' => $user['id'],
                'settings' => array_except($user, 'id')
            ]),
            'headers' => ['cache-control' => 'no-cache']
        ]);

        return $response->getStatusCode() === 201;

    }
} 
```

在这个服务中，我们定义了设置要使用的 Guzzle 客户端的方法，以及定义默认客户端的快捷方法。这个默认消息还包括默认的授权头，并将内容类型设置为 JSON，这就是我们正在与之通信的内容。我们还演示了对 RestDB 的基本读写。

`userAccount`方法直接在`accounts`记录中搜索 Twitter ID，如果找到则返回记录，否则返回 false。注意`metafields`查询参数的使用——这让我们获取`_created`和其他[系统字段](https://restdb.io/docs/querying-with-the-api#restdb)。还要注意，除非传入了`$fresh`参数，否则我们会将结果缓存 5 分钟，因为用户信息很少会改变，我们可能会在一次会话中多次需要它。`createUserAccount`方法获取一组用户数据(其中最重要的是`id`键)并创建帐户。注意，我们正在寻找状态`201`，它意味着`CREATED`。

让我们也创建一个 ServiceProvider 并将服务注册为 singleton。

```
artisan make:provider RestdbServiceProvider 
```

```
<?php

namespace App\Providers;

use App\Services\Followers\RestDB;
use Illuminate\Support\ServiceProvider;

class RestdbServiceProvider extends ServiceProvider
{
    /**
     * Register the application services.
     *
     * @return void
     */
    public function register()
    {
        $this->app->singleton(
            'restdb', function ($app) {
            $r = new RestDB();
            $r->setDefaultClient();

            return $r;
        }
        );
    }
} 
```

最后，让我们更新我们的 LoginController。

```
 // ...
        $request->session()->put('twitter_id', $user->id);

        $rest = resolve('restdb');
        if (!$rest->userAccount($user->id)) {
            if ($rest->createUserAccount(
                [
                    'token' => $user->token,
                    'secret' => $user->tokenSecret,
                    'nickname' => $user->nickname,
                    'id' => $user->id,
                ]
            )) {
                $request->session()->flash(
                    'info', 'Your account has been created! Welcome!'
                );
            } else {
                $request->session()->flash(
                    'error', 'Failed to create your account :('
                );
            }

        }

        // ...

        return redirect('/'); 
```

在`LoginController`的`handleProviderCallback`方法中，我们首先获取(解析)服务，用它来检查用户是否有帐户，如果没有就创建它，如果成功或失败就将消息刷新到会话中。

让我们将这些 flash 消息放到视图中:

```
{{--index.blade.php--}}
@isset($info)
    <p>{{ $info }}</p>
@endisset

@isset($error)
    <p>{{ $error }}</p>
@endisset
... 
```

如果我们测试一下，毫无疑问，我们的新记录就产生了:

![A new account record is created](img/9a3c609bee4677f942050b4215875897.png)

现在我们来献一个`/dashboard`。这个想法是:

*   当用户登录时，他们会看到一个“仪表板”链接。
*   点击此链接将依次:
    *   从 RestDB 获取他们最新的`follower-lists`条目
    *   如果自上一个条目创建以来已经过去了超过 15 分钟，或者用户根本没有条目，则会获取新的关注者列表。新列表将被保存。如果不是第一个条目，则会为新的关注者和非关注者生成一个差异。
    *   如果用户在过去 15 分钟内进行了刷新，他们将被重定向到控制面板
*   当用户访问这个仪表板时，他们的所有关注者列表 RestDB 条目都会被获取
*   该应用程序检查记录中的所有 diff 条目，并为未关注者生成报告，显示他们在离开之前关注用户多长时间的信息。
*   一旦获取了报告的这些 id，就会通过`/users/lookup`端点获取他们的信息，以获取他们的头像和 Twitter 句柄。
*   如果一个账户已经被关注了一天或更短时间，它会被标上红色，这意味着很有可能有追随者。1–5 天为橙色，5–10 天为黄色，其他为中性。

让我们首先更新索引视图，并添加一条新的路由。

```
// routes/web.php
Route::get('/dashboard', 'HomeController@dashboard'); 
```

```
{{--index.blade.php--}}

...

<p>I bet you'd like to see your follower stats, wouldn't you?</p>

Go to <a href="/dashboard">dashboard</a>. 
```

我们需要一种方法来获取用户的最后一个`follower_lists`条目。因此，在 RestDB 服务中，我们可以添加以下方法:

```
 /**
     * Get the last follower_lists entry of the user in question, or false if
     * none exists.
     *
     * @param int $twitter_id
     * @return bool|\stdClass
     */
    public function getUsersLastEntry(int $twitter_id)
    {
        $id = $this->userAccount($twitter_id)->_id;
        /** @var ResponseInterface $request */
        $response = $this->client->get(
            'accounts/' . $id . '/follower_lists', [
                'query' => [
                    'metafields' => true,
                    'sort' => '_id',
                    'dir' => -1,
                    'max' => 1,
                ],
                'headers' => ['cache-control' => 'no-cache'],
            ]
        );

        $bodyString = json_decode($response->getBody()->getContents());

        return !empty($bodyString) ? $bodyString[0] : false;

    } 
```

我们要么返回 false，要么返回最后一个条目。注意，我们按`_id`元字段排序，从最新的到最早的(`dir=-1`)，最多取 1 个条目。这些参数都在这里解释[。](https://restdb.io/docs/querying-with-the-api#restdb)

现在让我们把注意力转向`HomeController`中的`dashboard`方法:

```
 public function dashboard(Request $request)
    {
        $twitter_id = $request->session()->get('twitter_id', 0);
        if (!$twitter_id) {
            return redirect('/');
        }

        /** @var RestDB $rest */
        $rest = resolve('restdb');
        $lastEntry = $rest->getUsersLastEntry($twitter_id);

        if ($lastEntry) {
            $created = Carbon::createFromTimestamp(
                strtotime($lastEntry->_created)
            );
            $diff = $created->diffInMinutes(Carbon::now());
        }

        if ((isset($diff) && $diff > 14) || !$lastEntry) {
            $followerIds = resolve(Followers::class)->getFollowerIds();         $rest->addFollowerList($followerIds, $lastEntry, $twitter_id);
        }

        dd("Let's show all previous lists");

    } 
```

好吧，这是怎么回事？首先，我们做一个简单的检查，看看用户是否仍然登录——`twitter_id`必须在会话中。如果没有，我们重定向到主页。然后，我们获取 Rest 服务，获取账户的最后一个追随者列表条目(要么是一个对象，要么是`false`)，然后如果它存在，我们计算它有多长时间了。如果超过 14 分钟，或者如果条目根本不存在(意味着这是该帐户的第一个条目)，我们将获取一个新的关注者列表并保存它。我们如何拯救它？通过向 Rest 服务添加一个新的`addFollowerList`方法。

```
 /**
     * Adds a new follower_lists entry to an account entry
     *
     * @param array $followerIds
     * @param \stdClass|bool $lastEntry
     * @param int $twitter_id
     * @return bool
     * @internal param array $newEntry
     */
    public function addFollowerList(
        array $followerIds,
        $lastEntry,
        int $twitter_id
    ) {
        $account = $this->userAccount($twitter_id);

        $newEntry = ['followers' => implode(', ', $followerIds)];

        if ($lastEntry !== false) {
            $lastFollowers = array_map(
                function ($el) {
                    return (int)trim($el);
                }, explode(',', $lastEntry->followers)
            );

            sort($lastFollowers);
            sort($followerIds);

            $newEntry['diff_gone'] = implode(
                ', ', array_diff($lastFollowers, $followerIds)
            );
            $newEntry['diff_new'] = implode(
                ', ', array_diff($followerIds, $lastFollowers)
            );

        }

        try {
            /** @var ResponseInterface $request */
            $response = $this->client->post(
                'accounts/' . $account->_id . '/follower_lists', [
                    'body' => json_encode($newEntry),
                    'headers' => ['cache-control' => 'no-cache'],
                ]
            );
        } catch (ClientException $e) {
            // Log the exception message or something
        }

        return $response->getStatusCode() === 201;
    } 
```

这个首先获取用户帐户，以在 RestDB 中找到帐户记录的 ID。然后，它用当前追随者 id 的正确格式化(内爆)字符串初始化`$newEntry`变量。接下来，如果有最后一个条目，我们:

*   通过分解字符串并清除空白，将这些 id 放入一个合适的数组中。
*   对当前和过去的跟随者数组进行排序，以便进行更有效的区分。
*   获取差异并将其添加到`$newEntry`。

然后，我们保存条目，通过使用先前获取的 ID 定位特定的帐户条目，并继续进入`follower_lists`的子集合。

为了验证这一点，我们可以伪造一些数据。让我们将`HomeController::dashboard`的`$followerIds`部分改为:

```
 $count = rand(50, 75);
            $followerIds = [];
            while ($count--) {
                $flw = rand(1, 100);
                if (in_array($flw, $followerIds)) $count++; else
                $followerIds[] = $flw;
            } 
```

这将生成 50-75 个从 1 到 100 的随机数。好到让我们有所不同。如果我们现在登录时点击 url `/dashboard`,我们应该会得到初始条目。

![Initial entry](img/a3539f6da6764246e14212d521112201.png)

如果我们从`if`块中去掉 15 分钟的限制，再刷新两次，我们总共生成了 3 个条目，看起来有所不同:

![3 entries with diffs](img/3a9e69471e560ef6dacfe2acc9a8740a.png)

该是最后一集了。让我们分析这些条目，并确定一些跟随者农民。

## 最后一段

因为它在上下文中是有意义的，所以我们将把这个逻辑放到`Followers`服务中。让我们创建一个`analyzeUnfollowers`方法。它将接受任意数量的条目，并在所有条目上循环执行其逻辑。然后，如果我们以后想提供一种更快的方法来检查自上次登录会话以来的最后一点信息，我们可以简单地传递最后两个条目而不是所有条目，逻辑保持不变。

```
 public function analyzeUnfollowers(array $entries)
    {
        ...
    } 
```

为了识别未关注者，我们查看最近的`diff_gone`，查找自上次我们检查关注者列表以来已经离开的所有人，然后在以前条目的`diff_new`数组中找到他们。这让我们知道他们在离开前跟踪了我们多长时间。在使用条目时，我们还需要将`diff_gone`和`diff_new`条目变成数组，以便于查找。

```
 /**
     * Accepts an array of entries (stdObjects) ordered from newest to oldest.
     * The objects must have the properties: diff_gone, diff_new, and followers,
     * all of which are comma delimited strings of integers, or arrays of integers.
     * The property `_created` is also essential.
     *
     * @param array $entries
     * @return array
     */
    public function analyzeUnfollowers(array $entries)
    {
        $periods = [];
        $entries = array_map(
            function ($entry) {
                if (is_string($entry->diff_gone)) {
                    $entry->diff_gone = $this->intArray($entry->diff_gone);
                }
                if (is_string($entry->diff_new)) {
                    $entry->diff_new = $this->intArray($entry->diff_new);
                }

                return $entry;
            }, $entries
        );

        $latest = array_shift($entries);

        for ($i = 0; $i < count($entries); $i++) {
            $cur = $entries[$i];
            $curlast = array_last($entries) === $cur;
            if ($curlast) {
                $matches = $latest->diff_gone;
            } else {
                $matches = array_intersect(
                    $cur->diff_new, $latest->diff_gone
                );
            }
            if ($matches) {
                $periods[] = [
                    'matches' => array_values($matches),
                    'from' => (!$curlast) ? Carbon::createFromTimestamp(strtotime($cur->_created)) : 'forever',
                    'to' => Carbon::createFromTimestamp(strtotime($latest->_created))
                ];
            }
        }

        return $periods;
    }

    /**
     * Turns a string of comma separated values, spaces or no, into an array of integers
     *
     * @param string $string
     * @return array
     */
    protected function intArray(string $string): array
    {
        return array_map(
            function ($el) {
                return (int)trim($el);
            }, explode(',', $string)
        );
    } 
```

当然，我们需要一种方法来获取所有的追随者列表条目。我们将`getUserEntries`方法放入`Rest`服务中:

```
 /**
     * Gets a twitter ID's full list of follower list entries
     *
     * @param int $twitter_id
     * @return array
     */
    public function getUserEntries(int $twitter_id): array
    {
        $id = $this->userAccount($twitter_id)->_id;
        /** @var ResponseInterface $request */
        $response = $this->client->get(
            'accounts/' . $id . '/follower_lists', [
                'query' => [
                    'metafields' => true,
                    'sort' => '_id',
                    'dir' => -1,
                    'max' => 100,
                ],
                'headers' => ['cache-control' => 'no-cache'],
            ]
        );

        $bodyString = json_decode($response->getBody()->getContents());

        return !empty($bodyString) ? $bodyString : [];

    } 
```

*有可能某些账户上的关注者数量会导致大量下载，从而降低应用程序的运行速度。因为我们只真正需要 diff 字段，所以我们可以只针对那些带有`h`参数的字段，如本页底部的[所述](https://restdb.io/docs/querying-with-the-api#restdb)。*

然后，如果我们出于调试的目的，修改了`dashboard`方法…

```
 $entries = $rest->getUserEntries($twitter_id);
        dd($followers->analyzeUnfollowers($entries)); 
```

输出如下所示。很明显，我们的 5 个假关注者只关注了我们 5 秒钟，而其余的人在我们注册这项服务之前就已经在关注我们了(即`forever`)。

![Analyzed unfollowers](img/c042bf26c194b869e7dcac896412c0e6.png)

最后，我们可以分析我们得到的时间段——很容易识别短的时间段，并按照本文开头所述对它们进行颜色编码。因为这已经是一篇相当长的文章了，我将把那部分，以及关于使用 Twitter 的[用户查找 API](https://dev.twitter.com/rest/reference/get/users/lookup) 将 id 转换成用户句柄的部分作为家庭作业。Protip:如果您用完了该部分的查询调用，您可以使用 user_id 参数抓取他们的[迷你配置文件！](https://twitter.com/intent/user?user_id=125083073)

## 结论

我们构建了一个简单的应用程序来跟踪一个人跟踪你的时间，并将他们标记为跟随者农民，所有这些都没有使用本地数据库——RestDB 为我们提供了极高的性能、可伸缩性和对本地服务的独立性。

我们可以对这款应用进行许多升级:

*   在后台自动刷新关注者列表的 cronjob
*   大量缓存以节省 API 调用并提高速度
*   允许用户保留更多条目的高级账户订阅
*   一个将推文与未关注相匹配的仪表盘，向你展示是什么促使某人离开了你的推特圈
*   多账户支持
*   Instagram 支持

你还能想到这个系统的哪些升级？欢迎在 Github 上给 app [投稿！](https://github.com/Swader/followerfarmers)

## 分享这篇文章