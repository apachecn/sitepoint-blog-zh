# PredictionIO 和 Lumen:构建电影推荐应用

> 原文：<https://www.sitepoint.com/predictionio-and-lumen-building-a-movie-recommendation-app/>

在简介中，我们介绍了 PredictionIO 的基础知识并安装了它的依赖项。在这一部分，我们将构建电影推荐应用程序。

![Final app screenshot](img/1b64edd63a56bdb6abb692b5259e2259.png)

## 环境配置文件

在您的应用程序目录中，创建一个`.env`文件并添加以下配置:

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=some-random-key

PIO_KEY=your-pio-app-key
TMDB_KEY=your-tmdb-api-key

CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_DRIVER=database 
```

确保用唯一的随机密钥替换`APP_KEY`的值。因为我们使用的是 Lumen，所以您可以通过执行`php artisan key:generate`来生成一个。另外，用您创建的 PredictionIO 应用程序的键替换`PIO_KEY`的值，用 TMDB 网站提供的 API 键替换`TMDB_KEY`。

## 从 TMDB 导入数据

我们将使用 PredictionIO SDK 导入数据，所以我们首先需要告诉 Lumen 使用它。在`lumen/app`下创建一个`Classes`目录。然后在里面创建一个`Pio.php`文件并添加以下代码。

```
<?php
namespace App\Classes;

use predictionio\EventClient;
use predictionio\EngineClient;

class Pio
{

    public function eventClient()
    {

        $pio_accesskey = env('PIO_KEY');
        $pio_eventserver = 'http://127.0.0.1:7070';

        return new EventClient($pio_accesskey, $pio_eventserver);

    }

    public function predictionClient()
    {

        $pio_predictionserver = 'http://127.0.0.1:8192';

        return new EngineClient($pio_predictionserver);

    }

} 
```

此类将作为 PredictionIO 事件客户端和引擎客户端的容器。这样，我们就不必在每次需要使用它们的时候实例化这些类。

事件客户端用于与负责为我们的应用程序收集数据的事件服务器进行对话。客户端需要应用密钥和运行事件服务器的 URL。默认情况下，它运行在端口 7070 上。

另一方面，引擎客户端用于与负责提供推荐的引擎进行对话。

接下来，打开`bootstrap/app.php`文件，取消负责处理会话的中间件的注释。这允许我们为当前正在评价一些电影的用户保存一个惟一的 ID。

```
$app->middleware(
    [
//Illuminate\Cookie\Middleware\EncryptCookies::class,
//Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        Illuminate\Session\Middleware\StartSession::class,
//Illuminate\View\Middleware\ShareErrorsFromSession::class,
//Laravel\Lumen\Http\Middleware\VerifyCsrfToken::class,
    ]
); 
```

创建一个新的控制器，命名为`AdminController.php`。控制器存储在`app/Http/Controllers`目录中。将它设置为使用我们之前创建的`Pio`类。

```
<?php 
namespace App\Http\Controllers;

use Laravel\Lumen\Routing\Controller as BaseController;
use App\Repos\Pio;

class AdminController extends BaseController
{

} 
```

创建一个`importMovies`方法。我们将使用这种方法从 TMDB API 导入电影:

```
 public function importMovies(Pio $pio)
    {

        $index = 1;
        $pio_eventclient = $pio->eventClient();

        $http_client = new \GuzzleHttp\Client();
        $es_client = new \Elasticsearch\Client();

        for ($x = 1; $x <= 100; $x++) {

            $movies_url = 'https://api.themoviedb.org/3/movie/popular?api_key=' . env(
                    'TMDB_KEY'
                ) . '&page=' . $x;

            $movies_response = $http_client->get($movies_url);
            $movies_body = $movies_response->getBody();

            $movies_result = json_decode($movies_body, true);
            $movies = $movies_result['results'];

            if (!empty($movies)) {

                foreach ($movies as $row) {

                    $id = $row['id'];
                    $title = $row['title'];
                    $poster_path = '';
                    if (!empty($row['poster_path'])) {
                        $poster_path = $row['poster_path'];
                    }

                    $moviedetails_url = 'https://api.themoviedb.org/3/movie/' . $id . '?api_key=' . env(
                            'TMDB_KEY'
                        );

                    $moviedetails_response = $http_client->get(
                        $moviedetails_url
                    );
                    $movie_details_body = $moviedetails_response->getBody();

                    $movie = json_decode($movie_details_body, true);

                    $overview = $movie['overview'];
                    $release_date = $movie['release_date'];

                    $genre = '';
                    if (!empty($movie['genres'][0])) {
                        $genre = $movie['genres'][0]['name'];
                    }

                    $popularity = $movie['popularity'];

                    $movie_data = [
                        'itypes' => 1,
                        'tmdb_id' => $id,
                        'title' => $title,
                        'poster_path' => $poster_path,
                        'overview' => $overview,
                        'release_date' => $release_date,
                        'genre' => $genre,
                        'popularity' => $popularity,
                    ];

                    $pio_response = $pio_eventclient->setItem(
                        $index, $movie_data
                    );

                    //create elasticsearch index
                    $params = [];
                    $params['body'] = $movie_data;
                    $params['index'] = 'movierecommendation_app';
                    $params['type'] = 'movie';
                    $params['id'] = $index;
                    $es_res = $es_client->index($params);

                    $index++;
                }
            }
        }
    } 
```

打破它

1.  我们将`Pio`类注入到`importMovies`方法中

2.  将索引初始化为 1。这将作为我们将要导入的电影的唯一 ID。

3.  调用`Pio`类中的`eventClient`方法。这将初始化 PredictionIO 事件客户端，我们可以使用它来保存电影数据。

4.  创建 Guzzle HTTP 客户端、PredictionIO 事件客户端和 ElasticSearch 客户端的新实例。

    ```
     $index = 1;
      $pio_eventclient = $pio->eventClient();

      $http_client = new \GuzzleHttp\Client();
      $es_client = new \Elasticsearch\Client(); 
    ```

5.  创建一个将执行 100 次的循环。这允许我们获得大约 2000 部电影，因为我们向 TMDB API 发出的每个请求都会返回 20 部电影。循环的每次迭代都会改变`$x`的值，我们用它来访问每次迭代的下一页。

    ```
     for ($x = 1; $x <= 100; $x++) {
    	    ...
    	} 
    ```

6.  在循环内部，我们使用 Guzzle 向 TMDB API 发出请求。因为我们想得到尽可能多的喜欢，所以我们请求有史以来最受欢迎的电影。我们从`.env`文件中获取`api_key`的值。然后我们使用 Guzzle 提供的`get`方法来执行请求。然后我们使用`getBody`方法得到响应体。这基本上是一个包含所有 20 部电影及其细节的 JSON 字符串。我们使用`json_decode`将它转换成一个数组，并提取结果。

    ```
     $movies_url = 'https://api.themoviedb.org/3/movie/popular?api_key=' . env('TMDB_KEY') . '&page=' . $x;

        $movies_response = $http_client->get($movies_url);
        $movies_body = $movies_response->getBody();

        $movies_result = json_decode($movies_body, true);
        $movies = $movies_result['results']; 
    ```

7.  检查它是否真的包含任何东西。如果是，那么我们继续循环所有返回的电影，这样我们可以通过向 TMDB API 发出另一个请求来获得更多的细节。一旦我们提出请求，我们就提取我们需要的细节。

    ```
    if (!empty($movies)) {

        foreach ($movies as $row) {

            $id = $row['id'];
            $title = $row['title'];
            $poster_path = '';
            if (!empty($row['poster_path'])) {
                $poster_path = $row['poster_path'];
            }

            $moviedetails_url = 'https://api.themoviedb.org/3/movie/' . $id . '?api_key=' . env(
                    'TMDB_KEY'
                );

            $moviedetails_response = $http_client->get($moviedetails_url);
            $movie_details_body = $moviedetails_response->getBody();

            $movie = json_decode($movie_details_body, true);

            $overview = $movie['overview'];
            $release_date = $movie['release_date'];

            $genre = '';
            if (!empty($movie['genres'][0]['name'])) {
                $genre = $movie['genres'][0]['name'];
            }

            $popularity = $movie['popularity'];
        }
    } 
    ```

8.  构造一个数组，包含我们要提供给 PredictionIO 的细节，然后调用`setItem`方法保存它。该方法接受我们希望分配给项目的惟一 ID 和实际数据作为第二个参数。

    ```
    $movie_data = array(
     'itypes' => 1,
     'tmdb_id' => $id,
     'title' => $title,
     'poster_path' => $poster_path,
     'overview' => $overview,
     'release_date' => $release_date,
     'genre' => $genre,
     'popularity' => $popularity
    );

    $pio_response = $pio_eventclient->setItem($index, $movie_data); 
    ```

9.  索引 ElasticSearch 服务器中的电影数据。我们稍后将使用它来显示我们将推荐给用户的随机电影的细节，以及 PredictionIO 将返回的实际推荐。注意，我们使用`$index`作为 ID，所以我们还需要在循环的每次迭代中递增它。

    ```
    $params = array();
    $params['body']  = $movie_data;
    $params['index'] = 'movierecommendation_app';
    $params['type']  = 'movie';
    $params['id']    = $index;
    $es_res = $es_client->index($params);

    $index++; 
    ```

10.  最后，我们可以添加将要访问的路线，以便开始导入一些电影。打开`app/Http/routes.php`文件，添加如下路线:

    ```
    $app->get('/movies/import', 'AdminController@importMovies'); 
    ```

之后，`/movies/import`路径在浏览器中变得可访问，开始从 TMDB 导入一些电影。如果你不想这样做，你也可以使用[命令](https://laravel.com/docs/5.2/artisan)。

这可能需要一段时间才能完成，所以去喝杯咖啡或看一集你最喜欢的节目。

### 随机挑选电影

现在我们有了一些电影，我们准备向用户展示一些随机的电影。首先，创建一个新的控制器，将其命名为`HomeController.php`，然后添加以下代码:

```
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Laravel\Lumen\Routing\Controller as BaseController;
use App\Repos\Pio;

class HomeController extends BaseController
{

    public function index(Pio $pio)
    {

    }

} 
```

在`index`方法中，使用 PHP 内置的`uniqid`方法生成一个惟一的 ID，然后将其分配给`user_id`会话项。同样，用值`0`初始化`movies_viewed`。这将代表我们已经向当前用户展示的电影数量。随着随机电影的出现，我们会增加它。然后，我们使用事件客户端将用户保存到数据库中。我们可以通过调用接受用户 ID 作为参数的`setUser`方法来实现。最后，我们呈现了`index`页面。

```
$user_id = uniqid();

session(array('user_id' => $user_id, 'movies_viewed' => 0));

$pio_eventclient = $pio->eventClient();
$pio_eventclient->setUser($user_id);

return view('index'); 
```

不要忘记在 routes 文件中添加相应的路由:

```
$app->get('/', 'HomeController@index'); 
```

这是我们正在渲染的`index`页面的代码:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title></title>
  <link rel="stylesheet" href="/assets/css/bootstrap.min.css">
  <link rel="stylesheet" href="/assets/css/style.css">
</head>
<body>
  <div id="wrapper">
    <div class="navbar navbar-default">
      <div class="navbar-header">
        <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-responsive-collapse">
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
        </button>
        <a class="navbar-brand" href="">Movie Recommender</a>
      </div>
      <div class="navbar-collapse collapse navbar-responsive-collapse">
        <ul class="nav navbar-nav">
          <li><a href="/">Home</a></li>
        </ul>
      </div>
    </div>

    <div class="container">

      <div class="row">

        <div id="movie-container" class="col-md-10 col-centered">

        </div>

      </div>

      <script id="movie-template" type="text/x-handlebars-template"> <div class="col-md-8">
          <img src="http://image.tmdb.org/t/p/w500{{_source.poster_path}}">
        </div>
        <div class="col-md-4">
          <h3>{{_source.title}}</h3>
          <div class="release-date">
            {{_source.release_date}}
          </div>
          <div class="genre">
            Genre: {{_source.genre}}
          </div>
          <div class="overview">
            {{_source.overview}}
          </div>
          <div class="button-container">
            <button class="btn btn-success btn-block btn-next" data-id="{{_id}}" data-action="like">Like</button>
            <button class="btn btn-danger btn-block btn-next" data-id="{{_id}}" data-action="dislike">Dislike</button>
            <a href="/movies/recommended" class="show-recommendations">Show Recommendations</a>
          </div>
        </div> </script>

      <span class="label label-success"></span>

    </div>
  </div>
  <script src="/assets/js/jquery.min.js"></script>
  <script src="/assets/js/bootstrap.min.js"></script>
  <script src="/assets/js/handlebars.min.js"></script>
  <script src="/assets/js/main.js"></script>
</body>
</html> 
```

从上面的代码中可以看出，我们主要使用客户端模板来呈现电影的细节。对于这个应用程序，我们使用[手柄](http://handlebarsjs.com/)。我们使用 ajax 加载每部电影的细节。

从上面的代码中，你可以看到我们正在使用[引导](http://getbootstrap.com/)进行造型。我们还有整个应用程序的基本样式，它被添加到`style.css`文件中:

```
.col-centered {
  float: none;
  margin: 0 auto;
}

.button-container {
  margin-top: 20px;
}

.show-recommendations {
  display: none;
}

#recommended-movies > div {
  height: 1000px;
} 
```

对于脚本，我们使用 [jQuery](http://jquery.com/) 、Bootstrap 的 JavaScript 文件、[手柄](http://handlebarsjs.com/)和应用程序的主 JavaScript 文件。

对于主 JavaScript，我们有以下代码:

```
var movie_src = $("#movie-template").html();
var movie_template = Handlebars.compile(movie_src);

function getRandomMovie(request_data){

  request_data = typeof request_data !== 'undefined' ? request_data : {};

  $.post('movie/random', request_data, function(response){
    var data = JSON.parse(response);

    var movie_html = movie_template(data);
    $('#movie-container').html(movie_html);

    if(data.has_recommended){
      $('.show-recommendations').show();
    }

  });
}

getRandomMovie();

$('#movie-container').on('click', '.btn-next', function(){
  var self = $(this);
  var id = self.data('id');
  var action = self.data('action');
  getRandomMovie({'movie_id' : id, 'action' : action});

}); 
```

分解一下，我们首先编译 Handlebars 模板，它存储在 ID 为`movie-template`的 div 中:

```
var movie_src = $("#movie-template").html();
var movie_template = Handlebars.compile(movie_src); 
```

然后我们声明`getRandomMovie`方法。这将接受`request_data`作为可选参数。在函数内部，我们使用 jQuery 的`post`方法向`movie/random`路径发出一个`POST`请求。这将从服务器返回 JSON 格式的随机电影数据。然后我们使用`JSON.parse`方法将它转换成 JavaScript 可以使用的对象。完成后，我们将它提供给我们之前编译的 Handlebars 模板，然后更新`movie-container` div 的内容。如果返回的数据有`has_recommended`项，我们会显示一个链接，将用户引导到显示 PredictionIO 推荐的电影的页面。

```
function getRandomMovie(request_data){

  request_data = typeof request_data !== 'undefined' ? request_data : {};

  $.post('movie/random', request_data, function(response){
    var data = JSON.parse(response);

    var movie_html = movie_template(data);
    $('#movie-container').html(movie_html);

    if(data.has_recommended){
      $('.show-recommendations').show();
    }

  });
} 
```

一旦脚本被加载，我们执行函数来加载第一个随机电影。

```
getRandomMovie(); 
```

然后我们用`btn-next`类监听按钮上的`click`事件。如果你还记得之前 app 的概述，我们有两个按钮:*喜欢*和*不喜欢*。那些按钮有`btn-next`类。因此，每次点击这些按钮时，都会执行下面的代码。它所做的是调用`getRandomMovie`函数并提供电影 ID 和动作。动作的值可以是*喜欢*或*不喜欢*:

```
$('#movie-container').on('click', '.btn-next', function(){
  var self = $(this);
  var id = self.data('id');
  var action = self.data('action');
  getRandomMovie({'movie_id' : id, 'action' : action});
}); 
```

回到服务器端，我们现在准备编写从数据库中获取一部随机电影的代码。首先，声明一个新的路由来响应对`movie/random`路径的`POST`请求:

```
$app->post('/movie/random', 'HomeController@randomMovie'); 
```

在上面的代码中，我们使用了之前用于呈现应用程序主页的同一个控制器。但是这次我们使用的是`randomMovie`方法。因此，请在您的`app/Http/controllers/HomeController.php`文件中声明它。我们将在这个方法中使用`Request`类，所以我们将它作为参数传入。这允许我们获得在 HTTP 请求中传递的用户输入。并且不要忘记通过`Pio`课程。

```
public function randomMovie(Request $request, Pio $pio) {
  ...
} 
```

在`randomMovie`方法里面:

1.  我们获得请求的细节，然后检查是否已经设置了用户会话。如果有一个用户会话，我们将获得当前用户观看的电影数量。

2.  声明 ElasticSearch 客户端的一个新实例，然后我们通过使用 PHP 的`mt_rand`函数生成一个从 1 到 1000 的随机值来获得一个随机电影。如果您还记得以前，当我们将电影导入到 ElasticSearch 时，我们使用一个索引作为 ID 的值，对于循环的每一次迭代，我们都增加 1。这就是为什么这行得通。一旦我们得到响应，我们只需提取我们需要的细节。

3.  检查请求中是否提供了`movie_id`。如果有，那么就说明用户在给电影评分。

4.  调用 PredictionIO 事件客户端中的`recordUserActionOnItem`方法。它接受动作作为它的第一个参数。如果你还记得的话，之前我们定制了引擎，使其可以接受*喜欢*或*不喜欢*作为一种评分形式。这就是我们所指的行动。第二个参数是执行操作的用户的 ID，第三个参数是正在评级的电影的 ID。

5.  增加观看的电影，然后检查是否已经观看了 20 部电影。如果是这样，那么我们向电影细节传递一个附加字段`has_recommended`。然后，将在客户端检查该字段是否存在，以显示推荐页面的链接。接下来，我们将观看的电影保存到会话中，然后返回电影细节。

```
if (session('user_id')) {

    $movies_viewed = session('movies_viewed');

    $es_client = new \Elasticsearch\Client();

    $search_params['index'] = 'movierecommendation_app';
    $search_params['type'] = 'movie';
    $search_params['id'] = mt_rand(1, 1000);
    $movie = $es_client->get($search_params);

    if (!empty($request->input('movie_id'))) {

        $user_id = session('user_id');
        $movie_id = $request->input('movie_id');
        $action = $request->input('action');

        $pio_eventclient = $pio->eventClient();
        $pio_eventclient->recordUserActionOnItem($action, $user_id, $movie_id);

        $movies_viewed += 1;
        if ($movies_viewed == 20) {
            $movie['has_recommended'] = true;
        }
        $movie['movies_viewed'] = $movies_viewed;

        session(['movies_viewed' => $movies_viewed]);
    }

    return $movie;
} 
```

### 推荐电影

既然我们已经完成了学习阶段，是时候继续编写推荐阶段的代码了。

首先，创建一个新的路由来响应在`/movies/recommended`路径上的 GET 请求:

```
$app->get('/movies/recommended', 'HomeController@recommendedMovies'); 
```

在`recommendedMovies`方法里面:

1.  创建预测引擎客户端的新实例。请注意，这与我们目前使用的事件客户端不同，因为它实际上是用于从引擎获取预测结果的。记住这一点，我们现在使用`sendQuery`方法发出请求。它接受一个数组作为它的参数。这个数组应该包含`user`和`num`作为它的元素。`user`是用户 ID，`num`是我们希望引擎返回的电影数量。

2.  如果请求成功，我们使用`array_map`方法只提取电影 id。`array_map`方法接受一个函数，该函数返回我们想要的项和我们想要操作的数组作为它的第二个参数。这将返回一个电影 id 数组。

3.  创建一个新的 ElasticSearch 客户端实例，并执行一个对`movierecommendation_app`索引的请求。然后，我们可以将电影 id 作为搜索查询进行传递。接下来，我们使用`search`方法并传入搜索参数。这将返回由 PredictionIO 引擎客户端返回的电影的详细信息。

4.  重置观看的电影数，并将用户 ID 设置为`null`，这样下次有人使用该应用程序时，它将创建一个全新的用户。最后，我们渲染`recommended_movies`视图并传入电影细节。

```
public function recommendedMovies(Pio $pio){

  $recommended_movies = array();

  try{
    $user_id = session('user_id');

    $pio_predictionclient = $pio->predictionClient();
    $recommended_movies_raw = $pio_predictionclient->sendQuery(array('user' => $user_id, 'num' => 9));

    $movie_ids = array_map(function($item){
      return $item['item'];
    }, $recommended_movies_raw['itemScores']);   

    $es_client = new \Elasticsearch\Client();

    $search_params['index'] = 'movierecommendation_app';
    $search_params['type']  = 'movie';
    $search_params['body']['query']['bool']['must']['terms']['_id'] = $movie_ids;

    $es_response = $es_client->search($search_params);
    $recommended_movies = $es_response['hits']['hits'];

  }catch(Exception $e){
      echo 'Caught exception: ', $e->getMessage(), "\n";
  }

  session(array('movies_viewed' => 0, 'user_id' => null));

  return view('recommended_movies', array('recommended_movies' => $recommended_movies));

} 
```

以下是推荐电影页面的 HTML:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title></title>
  <link rel="stylesheet" href="/assets/css/bootstrap.min.css">
  <link rel="stylesheet" href="/assets/css/style.css">
</head>
<body>
  <div id="wrapper">
    <div class="navbar navbar-default">
      <div class="navbar-header">
        <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-responsive-collapse">
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
        </button>
        <a class="navbar-brand" href="">Movie Recommender</a>
      </div>
      <div class="navbar-collapse collapse navbar-responsive-collapse">
        <ul class="nav navbar-nav">
          <li><a href="/">Home</a></li>
        </ul>
      </div>
    </div>

    <div class="container">

      <div class="row">
        <h1>Recommended Movies</h1>
        <div id="recommended-movies" class="col-md-12">
        <?php
        foreach($recommended_movies as $rm){
        ?>
        <div class="col-md-6">
          <img src="http://image.tmdb.org/t/p/w500<?php echo $rm['_source']['poster_path'] ?>" alt="<?php echo $rm['_source']['title'] ?>">
          <h4><?php echo $rm['_source']['title']; ?></h4>
          <div class="release-date">
          <?php echo $rm['_source']['release_date']; ?>
          </div>
          <div class="genre">
          <?php echo $rm['_source']['genre']; ?>
          </div>
          <div class="overview">      
          <?php
          echo $rm['_source']['overview'];
          ?>
          </div>
        </div>

        <?php 
        }
        ?>    
        </div>
      </div>

    </div>
  </div>

</body>
</html> 
```

我们在上面所做的是遍历`$recommended_movies`数组，然后回显相关字段的值:标题、发布日期、流派、概览和图像。

### 展开发动机

此时，我们已经准备好部署引擎了——我们需要为它分配应用程序。为此，导航到保存引擎的目录并打开`engine.json`文件。它应该是这样的:

```
{
  "id": "default",
  "description": "Default settings",
  "engineFactory": "wern.RecommendationEngine",
  "datasource": {
    "params" : {
      "appName": "INVALID_APP_NAME"
    }
  },
  "algorithms": [
    {
      "name": "als",
      "params": {
        "rank": 10,
        "numIterations": 20,
        "lambda": 0.01,
        "seed": 3
      }
    }
  ]
} 
```

我们需要改变两件事:`datasource.params`对象下的`appName`和同一对象上的`appId`。如果不确定 app ID 是什么，可以在终端中执行`pio app list`命令。它应该是这样的:

![pio app list](img/a7da1607e74a048f97ae92b1a637e6da.png)

只需复制 ID 列下的值。

更新后，您的`engine.json`文件应该如下所示:

```
{
  "id": "default",
  "description": "Default settings",
  "engineFactory": "wern.RecommendationEngine",
  "datasource": {
    "params" : {
      "appId": 1,
      "appName": "MovieRecommendationApp"
    }
  },
  "algorithms": [
    {
      "name": "als",
      "params": {
        "rank": 10,
        "numIterations": 20,
        "lambda": 0.01,
        "seed": 3
      }
    }
  ]
} 
```

接下来，我们需要通过在引擎的根目录中执行`pio build`命令来构建引擎。这将下载引擎需要的所有文件，并将它们保存到引擎中。这可能需要一段时间，具体取决于您的计算机速度和互联网连接。我建议添加`--verbose`选项，这样您就可以清楚地看到发生了什么。

完成后，它应该会显示类似如下的内容:

```
[INFO] [Console$] Your engine is ready for training. 
```

一旦你看到这一点，你就可以在浏览器中访问该应用程序，开始喜欢和不喜欢电影，直到它显示出指向电影推荐页面的链接。要训练数据，需要执行`pio train`命令。执行这个命令时，请确保您仍然在引擎目录中。

如果培训成功完成，它应该显示如下内容:

```
[INFO] [CoreWorkflow$] Training completed successfully. 
```

如果没有，您可能会得到如下内容:

```
[ERROR] [Executor] Exception in task 0.0 in stage 42.0 
```

如果是这种情况，您可以尝试更改您的`engine.json`文件的`algorithms`属性下的`numIterations`。对我来说，把它改成 10 是有效的。

```
"algorithms": [
  {
    "name": "als",
    "params": {
      "rank": 10,
      "numIterations": 10,
      "lambda": 0.01,
      "seed": 3
    }
  }
] 
```

该错误与输入数据和计算机中的可用内存有关:问题是 Apache Spark 需要大量内存来训练特定迭代次数的数据。需要的迭代次数越少，需要的内存就越少。如果您的系统没有所需的内存，就会出现这个错误。这就是减少迭代次数有效的原因。

接下来，您可以使用`pio deploy --port 8192`命令部署引擎。这将部署推荐引擎，并且可以在您计算机的端口 8192 上访问它。当你在浏览器中访问`http://IP:8192`时，它应该显示一些关于引擎和服务器的信息，其中 IP 是你本地机器的 IP(在[家园改进](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)的情况下是`192.168.10.10`)。您也可以返回浏览器并访问电影推荐页面。它现在应该包含一些电影推荐。

现在您可以将`pio train`和`pio deploy`命令添加到您的 [crontab](https://www.sitepoint.com/a-comprehensive-crash-course-into-cronjobs) 中，这样它每 5 分钟执行一次。通过这种方式，它不断在后台运行以训练新数据，并在准备就绪时进行部署。

```
*/5 * * * * cd /path/to/engine; pio train
*/5 * * * * cd /path/to/engine; pio deploy 
```

## 结论

在本教程中，您已经学习了如何使用 PredictionIO 为您的 PHP 应用程序提供机器学习功能。你可以查看这个项目的 [Github repo](https://github.com/anchetaWern/predictionio-movierecommendation) 来查看完整的源代码。

在这个系列中，我们仅仅触及了皮毛，您可以通过预测做更多的事情。如果你想了解更多，我建议你查阅官方文件。

如果你知道任何预测的替代方法或者一些有趣的用例，请在评论中告诉我们！

## 分享这篇文章