# 如何用 Laravel Scout 和 Algolia 建立一个歌词网站

> 原文：<https://www.sitepoint.com/build-lyrics-website-laravel-scout-algolia/>

在本教程中，我们将使用 Laravel Scout 和 [Algolia](http://algolia.com) 创建一个最小的歌词网站。为此，我们需要一个 CRUD 应用程序来处理我们的数据，并需要一个搜索界面来让用户搜索内容。然而，与其从头开始创建完整的应用程序，不如让我们只安装我们为本教程创建的[成品 CRUD](https://github.com/lavary/lyrics-crud) 应用程序。

Algolia 是一个托管的搜索引擎 API，为我们提供了创建具有强大搜索功能的产品所需的所有工具和资源。

![Search stock vector image](img/12f4c9a10fe6f342e440f359179ff55f.png)

在本教程结束时，我们将有一个这样的产品:

![Lyrics_Website_With_Algolia_Scout](img/64e76845da3c022685545c7bfd079745.png)

![Lyrics Details Page](img/c78a9e773a5bed0f2616c20808ec1989.png)

## 引导应用程序

我假设您已经启动并运行了您的开发环境。但是如果你需要一个好的开发环境马上投入行动，那就用[家园改良](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。

*陌生到流浪？阅读[本](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/)。想要更深入的了解？购买我们关于 PHP 环境的[书！](http://bit.ly/phpenv-sp)*

如果您已经有一个类似的应用程序，或者您有足够的经验来相对快速地构建一个应用程序，请随意跳过这一部分。

### CRUD 应用

下载预构建的 CRUD 项目最方便的方法是克隆它:

```
git clone git@github.com:lavary/lyrics-crud.git coolyrics
cd coolyrics
composer install 
```

### 设置数据库

现在，让我们创建一个 MySQL 数据库。以下设置适用于上面提到的[家园改良](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)环境。根据需要进行更改。

```
mysql -h localhost -u homestead -psecret
mysql> CREATE DATABASE lyrics 
```

创建数据库后，我们制作了一个`.env.example`(位于我们项目的根目录中)的副本，并将其命名为`.env`。这是我们放置数据库凭证的地方:

```
#...

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=lyrics
DB_USERNAME=root
DB_PASSWORD=password

# ... 
```

还是那句话，根据需要应用到自己的喜好上。

现在，我们运行`migration`命令来创建表格:

```
php artisan migrate 
```

### 用样本数据填充数据库

歌词网站包含关于音乐家和他们的作品的信息，当然还有他们歌曲的歌词。为了创建一个最简单的数据结构，我们只为这个项目创建了两个有说服力的模型，即`Artist`和`Song`。`Artist`模型与`Song`模型有一对多的关系。这意味着每个艺术家可以有许多歌曲，每首歌曲属于我们的数据库中的艺术家。

在进入下一部分之前，您可以将一些记录插入到数据库中，从您最喜欢的艺术家开始，然后为每个艺术家添加一些歌曲。

这是我们目前掌握的情况:

![Lyrics Songs list](img/fe1cad464487a6cceff4f1cddc39474c.png)

您还可以使用项目文件中包含的 [SQL](https://raw.githubusercontent.com/lavary/lyrics-crud/master/storage/app/lyrics.sql) 文件，并通过以下命令将其转储到您的数据库中:

```
mysql -h localhost -u {USERNAME} -p{PASSWORD} lyrics  < /path/to/the/sql/file 
```

您也可以使用您最喜欢的 MySQL 管理应用程序导入文件，如 Sequel Pro、MySQL Workbench 或 PHPMyAdmin。

## 安装 Scout

让我们继续安装 Scout:

```
composer require laravel/scout 
```

然后，我们将服务提供者添加到`config/app.php`文件中的`$providers`:

```
Laravel\Scout\ScoutServiceProvider::class, 
```

现在我们需要使用`publish:config` artisan 命令生成 Scout 的配置文件:

```
php artisan vendor:publish --provider="Laravel\Scout\ScoutServiceProvider" 
```

因此，在`config`目录中会生成一个名为`scout.php`的配置文件。我们稍后将编辑该文件。

为了使数据模型可搜索，我们需要在各自的模型类中使用和`Laravel\Scout\Searchable`特征。这就是我们案例中的`Song`模型:

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Laravel\Scout\Searchable;

class Song extends Model
{
    use Searchable;

    protected $fillable = ['title', 'album', 'lyrics', 'youtube_link'];

    public function artist()
    {
        return $this->belongsTo('App\Artist');
    }
} 
```

## 设置 Algolia

按照计划，我们将使用 [Algolia](http://algolia.com) 作为我们的搜索引擎 API。

首先，让我们[创建一个账户](https://www.algolia.com/users/sign_up)来获得我们的*应用 ID* 。Scout 需要**应用 ID** 和**管理 API 密钥**才能操作。注册完成后，我们可以在左侧菜单的 **API Keys** 下找到我们的凭证。

![Algolia API Keys](img/a07926938defec45c59fa5bdf992d7ec.png)

现在，我们打开`config/scout.php`配置文件，并将我们的凭证放在那里:

```
<?php
'algolia' => [
        'id' => env('ALGOLIA_APP_ID', ''),
        'secret' => env('ALGOLIA_SECRET', ''),
    ], 
```

将键保存在`.env`中并使用`env()`或`getenv()`函数将它们加载到`scout.php`中是一个很好的做法。

要使用 Algolia 的 API，我们需要安装 Algolia 的 PHP SDK，它也可以作为 Composer 包获得:

```
composer require algolia/algoliasearch-client-php 
```

## 索引我们的数据

此时，我们需要在 Algolia 上创建索引。索引中的每条记录都是一个无模式的 JSON 对象(每条记录代表我们数据库中的一条记录)，它具有一组属性，可用于搜索、显示、排序和过滤数据。

我们只需要索引上述操作所需的数据，而不是索引整个记录。这有助于保持我们的索引整洁和优化。

除此之外，索引不是关系数据库，这意味着当搜索索引时，我们不能使用复杂的`where`子句或 SQL 连接。为了解决这个限制，我们应该为索引记录定义一个定制的结构。另一方面，我们应该连接所有需要的表，在索引之前准备一个定制的 JSON 对象。

为此，我们覆盖了`Song`模型中的`toSearchableArray()`方法(该方法通过`Laravel\Scout\Searchable`特征添加到类中)。默认情况下，`toSearchableArray()`方法返回`$this->toArray()`输出作为我们的索引对象(当向 Algolia 发送索引记录时)，而我们需要额外的数据，如**艺术家的名字**、**流派**和**图像 URL** ，它们位于另一个表`artists`中。

我们是这样做的:

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Laravel\Scout\Searchable;

class Song extends Model
{
    use Searchable;

    protected $fillable = ['title', 'album', 'lyrics', 'youtube_link'];

    public function toSearchableArray()
    {
        $genres = array_map(function($item) {
            return trim($item);
        }, explode(',', $this->artist->genres));

        return array_merge( $this->toArray(), ['artist' => $this->artist->name, 'photo' => $this->artist->photo, 'genres' => $genres]);
    }

    public function artist()
    {
        return $this->belongsTo('App\Artist');
    }
} 
```

由于`genres`字段在我们的数据库中可能包含一个逗号分隔的值(在我们的 CRUD 应用程序中有一个简单的文本字段)，我们使用`explode`来分隔流派。然后，我们迭代结果，去掉每个部分前后不需要的空格——使用`map()`函数:

```
<?php

// ...

$genres = array_map(function($item) {
            return trim($item);
        }, explode(',', $this->artist->genres));

// ... 
```

最后，我们将`$this->toArray()`的输出与我们想要的属性合并，返回最终的数组。

```
<?php

// ...

return array_merge( $this->toArray(), ['artist' => $this->artist->name, 'photo' => $this->artist->photo, 'genres' => $genres]);

// ... 
```

你可能想知道，为什么我们需要索引一个随机生成的字符串，比如图像 URL。我们很快就会谈到这一点。

对于数据库中的现有记录，我们可以使用`scout:import` artisan 命令一次性导入索引，如下所示:

```
php artisan scout:import "App\Song" 
```

当索引过程完成时，我们可以通过进入**索引**并从下拉菜单中选择我们的索引名称来查看 Algolia 上的索引:

![Algolia Index Dropdown](img/624e5f9ed378ed5e5aa4c26c9820f281.png)

索引不是一次性的任务。初始导入后，索引需要与数据库保持同步。既然我们用了 Scout，一切都已经搞定了。从现在开始，任何时候在数据库中插入、更新或删除记录，相应的索引记录都会受到相应的影响——这要感谢 Laravel 的模型观察者。

要了解 Scout 如何管理索引过程，请看一下[文档](https://laravel.com/docs/5.4/scout)。

## 配置 Algolia

接下来要做的事情是配置我们的 Algolia 索引以实现最佳操作。这些设置既可以从仪表板上修改，也可以使用 Algolia 的 API 以编程方式修改。

最重要的配置选项是*可搜索属性*和*自定义排名属性*。

“可搜索属性”设置定义了用于搜索的记录属性。这些属性的顺序也很重要，因为位于顶部的属性被认为更重要，并且**控制着**排名。

![Algolia Searchable Attributes](img/eb9b685b7edc3ab901c31ae299d063e3.png)

“自定义排名属性”选项表示每条记录的受欢迎程度，这也会影响排名。它们可以是任何东西，从点赞数、浏览量、下载量到评论数。也就是说，我们需要在索引中包含这些信息。

![Algolia Custom Ranking](img/c6e3427fd661fad5e1d484ea4eabe2eb.png)

Algolia 配备了大量配置选项。浏览每个设置超出了本教程的范围。要了解更多关于配置选项的信息，你应该看看 [Algolia 关于配置和相关性的 FAQ](https://www.algolia.com/doc/faq/index-configuration/) 。

## 网站

最后要做的是在我们的歌词网站内实现搜索接口。Algolia 的好处是，它没有将我们限制在任何接口实现上。

传统的搜索实现倾向于将搜索逻辑放在后端。为了实现这一点，我们可以使用 concertive 来搜索记录，使用`search()`方法(由`Searchable`特征提供)。这种方法有点慢，因为搜索请求要经过不同层次的抽象。可以这样做:

```
<?php

// ...
use Illuminate\Http\Request;

Route::get('/search', function (Request $request) {
    return App\Song::search($request->search)->get();
});

// ... 
```

另一种方式是直接从用户的浏览器向 Algolia 的搜索 API 发出搜索请求，为我们的用户提供“随你找”的体验。这种方法比前者快得多，因为不涉及接口。这意味着**我们希望在搜索结果中出现的每个属性****都应该出现在索引本身中**。

在本教程中，我们将采用第二种方法。我们使用 Scout 进行索引，并保持我们的索引与我们的数据库同步，然后我们使用 Algolia 的 API 进行搜索。

通过采用第二种方法，我们将有许多选项来显示结果。我们可以使用 AngularJS，Vue.js，或者 Algolia 的两个流行库，即 [Autocomplete](https://www.algolia.com/doc/guides/search/auto-complete/) 和 [Instantsearch.js](https://community.algolia.com/instantsearch.js/) 。

对于这个项目，我们将使用`Instantsearch.js`，这是一个基于 React 的 UI 小部件库，使创建搜索界面变得轻而易举。

我们的歌词网站由两个页面组成，一个是主搜索页面，另一个是显示歌曲细节的页面。

首先，让我们为这两个页面创建路线和控制器。然后我们将创建视图。

**文件:** `routes/web.php`

```
<?php

//...

Route::get('/',          'LyricsController@search');
Route::get('song/{id}',  'LyricsController@lyric');

// ... 
```

控制器:

```
php artisan make:controller LyricsController 
```

**文件:** `app/Http/Controllers/LyricsController.php`

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Song;

class LyricsController extends Controller
{
    public function search()
    {
        return view('search');
    }

    public function song(Request $request, $id)
    {
        $song = Song::find($id);

        return view('lyrics', compact('song'));
    }
} 
```

`song()`和`search()`方法呈现我们的网站页面。

首先，让我们为页面创建一个主布局模板。在`resources/views/layouts`中，创建一个名为`basic.blade.php`的文件，内容如下:

**文件:** `resources/views/layouts/basic.blade.php`

```
<!DOCTYPE html>
<html lang="{{ config('app.locale') }}">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <title>{{ config('app.name', 'Coolyrics') }}</title>

    <!-- Latest compiled and minified CSS -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">

    <!-- Styles -->
    <link rel="stylesheet" type="text/css" href="{{ asset('css/styles.css') }}">

</head>
<body>

@yield('content')

@section('javascript')
@show

</body>
</html> 
```

CSS 和 Javascript 文件有单独的刀片部分，这使我们能够更好地控制全局和特定于页面的资产，以及子模板的内部 JavaScript 代码。

页面中还加载了一个 CSS 文件——使网站更加个性化。如果你不喜欢它的外观和感觉，请随意更换样式。

现在，我们在`public/css`下创建一个名为`styles.css`的文件，内容如下:

**文件:** `public/css/styles.css`

```
html, body {
  height: 100%;
}

h2 {
  color: #888;
  font-size: 30pt;
}

#header {
  background: #34495e;
  color: #f1c40f;
  height: 80px;
}

#header .input-group {
  margin-top: 20px;
}

#header h1 { 
  margin-left: 50px;
  font-size: 20pt;
  font-weight: bold;
}

#header h1 sup {
  font-size: 8pt;
  font-weight: normal;
  display: inline-block;
}

.dropdown-menu .ais-menu {
  padding: 5px 5px;
}

.dropdown-menu .ais-menu--item {
  border-bottom: #ccc 1px dotted;
  padding: 5px 25px 5px 15px;
}

.dropdown-menu .ais-menu--item:last-child {
  border: 0;
}

.container {
  padding: 35px;
}

.container-fluid {
  padding: 40px;
}

#hits-container {
}

.ais-hits__empty {
  height: 100%;
  font-size: 15pt;
  font-weight: bold;
  color: #888;
}

.ais-hits__empty p {
  font-size: 12pt;
  font-weight: normal;
  color: #999;
  padding-top: 5px;
}

.song {
  border-bottom: #ccc 1px dotted;
  padding: 20px;
}

.song a.song-link {
  color: #000;
  font-size: 15pt;
  display: block;
}

.song a.song-link em {
  font-style: normal;
  color: #000;
  font-weight: bold;
}

.song span {
  color: #888;
  display: block;
}

.song span.song-artist {
  margin: 3px 0;
}

.song-youtube-link {
  margin-top: 10px;
}

#pagination-container {
  text-align: center;
  font-size: 11pt;
  margin: 20px 0;
}

#login-link {
  font-weight: bold;
  text-transform: uppercase;
  line-height: 80px;
}

#login-link a {
  color: #fff;  
}

/* 
  to control the size of the thumbnail adjust the .thumbnail width and height
*/
.band-thumbnail { 
  width: 90px;
  height: 90px;
  background-position: center;
  background-size: cover;
  margin-right: 30px;
  border-radius: 3px;
}

#lyrics-container {

}

#lyrics-container p {
  font-size: 13pt ;
} 

h1 { font-size: 30pt;  }

#lyrics-container h1 span {
  color: #000;
}

#lyrics-header {
  background: #f5f5f5;
  padding: 60px 0;
}

#lyrics-album-details {
  font-size: 11pt;
  font-weight: normal;
  display: block;
  color: #888;
  margin-bottom: 20px;
}

#lyrics-album-details .glyphicon {
  color: #ccc;
  margin-right: 5px;
}

#lyrics-youtube-link {
  position: absolute;
  left: 0; bottom: 0;
  background: #cc181e;
  width: 100%;
  height: 30px;
  line-height: 30px;
}

#lyrics-youtube-link a {
  color: #fff;
}

#lyrics-thumbnail {
  position: relative;
  width: 370px;
  height: 220px;
  margin: 0 auto;
}

#post-meta {
  margin: 15px 0;
}

#post-meta span {
  color: #ccc;
  font-size: 10pt;
  font-weight: normal;
  margin: 5px 0;
}

#lyrics-content {
  background-color: #f1f1f1;
  padding: 40px 0;
}

.btn-xs .glyphicon {
  color: #888;
  font-size: 8pt;
} 
```

### 歌词页

现在，我们在`resources/views`中创建一个名为`song.blade.php`的文件，内容如下:

**文件:** `resources/views/song.blade.php`

```
@extends('layouts.basic')

<div id="lyrics-container" class="text-center">  
    <div id="lyrics-header">
        <h1>{{$song->title}}</h1>
        <span id="lyrics-album-details">
            {{$song->artist->name}} - {{$song->album}} Album
        </span>  
        <div id="lyrics-thumbnail">
            <img src="{{Storage::url('artists/' . $song->artist->photo)}}">
            <div id="lyrics-youtube-link">
                <a href="{{$song->youtube_link}}"><i class="glyphicon glyphicon-play"></i> Watch on Youtube</a>
            </div>
        </div><!--/#lyrics-thumbnail-->
    </div><!--/#lyrics-header-->
    <div id="lyrics-content">
        <p><strong>"{{$song->title}}"</strong></p>
        <p>{!! nl2br($song->lyrics) !!}</p>
    </div><!--/#lyrics-content-->
</div> 
```

这个页面由 Laravel 在后台渲染和控制。

在这个模板中，我们扩展了布局并回显了歌词的属性。它应该是这样的:

![Lyrics Details Page](img/c78a9e773a5bed0f2616c20808ec1989.png)

### 搜索界面

现在我们到了主要部分，到目前为止我们已经为它创建了一切，搜索界面。

为了创建搜索界面，我们需要组合 Instantsearch.js 的几个小部件，并配置每个小部件以满足我们的需求。

我们的搜索界面将由三个部件组成:

*   一个搜索字段(**搜索框**小部件)
*   显示结果的部分(**点击**小部件)
*   分页(**分页**小部件)

所有的小部件都是开箱即用的，我们不需要额外的开发。也就是说，每当用户在搜索框中输入一个值，所有的小部件(搜索框、点击、分页等。)会做出相应的回应。

在`resources/views`目录下创建一个名为`search.blade.php`的新文件，内容如下:

**文件:** `resources/views/search.blade.php`

```
@extends('layouts.basic')

@section('content')

<div id="header">
    <div class="col-md-2"><h1>Coolyrics</h1></div>
    <div class="col-md-6">

          <div id="search-box"></div> 

    </div>
</div>

<div class="container-fluid">
    <div id="hits-container"></div>
    <div id="pagination-container"></div>
</div>

@endsection

@section('scripts')  
<!-- Scripts -->
@parent    
<script language="javascript" src="https://cdn.jsdelivr.net/jquery/3.1.1/jquery.min.js"></script>
<script src="https://cdn.jsdelivr.net/instantsearch.js/1/instantsearch.min.js"></script>
@endsection 

@section('javascript')
@endsection 
```

在`content`部分，我们有一个 id 为`search-box`的容器，我们将在其中放置我们的`searchBox`小部件。我们还有用于`hits`和`pagination`小部件的容器。

在`scripts`部分，首先，我们使用`@parent`指令加载主布局的脚本。然后，我们添加特定于该模板的脚本，即`jQuery`和`Instantsearch.js`。

我们还有一个用于模板内部 Javascript 代码的部分(`javascript`)。这是我们实例化 Instantsearch.js 并添加小部件的地方。

### 设置即时搜索。射流研究…

我们可以使用鲍尔或 NPM 来安装 Instantsearch.js，或者像这样简单地使用 CDN:

**文件:** `resources/views/search.blade.php`

```
@section('css')
<link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/instantsearch.js/1/instantsearch.min.css" />
@endsection 
```

和 Javascript 文件:

**文件:** `resources/views/search.blade.php`

```
@section('scripts')
@parent    
<script language="javascript" src="https://cdn.jsdelivr.net/jquery/3.1.1/jquery.min.js"></script>
<script src="https://cdn.jsdelivr.net/instantsearch.js/1/instantsearch.min.js"></script>
@endsection 
```

### 初始化

现在，我们需要用给定的`Application Id`和`API Key`创建一个搜索对象。在`resources/views/search.blade.php`文件中，在`javascript`部分添加以下 JavaScript 代码。

**文件:** `resources/views/search.blade.php`

```
...

section('javascript')
var search = instantsearch({
    // You should put your keys here:
    appId: 'XXXX',
    apiKey: 'XXX',
    indexName: 'songs'
  });
@endsection

... 
```

在上面的代码中，我们需要提供三个值来实例化搜索对象:`appId`、`ApiKey`和`indexName`。

因为我们在 Javascript 环境中进行初始化，所以每个人都可以看到这些键。知道了这一点，我们**必须**使用我们的**仅搜索 API 键**，它仅限于搜索操作。你可以在你的 Algolia 档案中的 **API 键**下找到它。

`indexName`属性是我们想要搜索的索引的名称，在我们的例子中是`songs`。

### 添加搜索框

searchBox 小部件创建一个智能文本字段来输入搜索关键字。

要添加小部件，我们调用`search`对象(我们刚刚创建的)上的`addWidget`方法，向它传递一个`searchBox`的实例:

**文件:** `resources/views/search.blade.php`

```
...

search.addWidget(
    instantsearch.widgets.searchBox({
      container: '#search-box',
      placeholder: 'Search by artist, song, or lyrics',
      wrapInput: false,
      cssClasses: {
        input: 'form-control'
      }
    })
  );

@endsection

... 
```

正如您在上面的代码中看到的，我们在实例化小部件时传递了一个配置对象。该对象用于调整小部件的行为。

`container`是我们的`searchBox`小部件所在的位置。`placeholder`是文本字段的普通 HTML 占位符。

如果`wrapInput`被设置为`true`，那么文本字段本身被另一个具有名为`ais-search-box`的类的`<div>`元素包装。

最后，`cssClasses`选项指定了要添加到小部件中的附加类。在上面的设置中，我们只是向文本字段本身添加了一个类。我们也可以向包装元素添加类。

要了解`searchBox`的其他可用选项，你应该看看[文档](https://community.algolia.com/instantsearch.js/documentation/#searchbox)。

### 添加点击小部件

hits 小部件根据一组已定义的模板显示搜索结果。

在`searchBox`的代码后添加以下代码:

**文件:** `resources/views/search.blade.php`

```
...

search.addWidget(
    instantsearch.widgets.hits({
      container: '#hits-container',
      templates: {
        item: $('#hits-temp').html(),
        empty: 'No lyrics was found! <p>We will be adding more lyrics to the database.</p>',
        header: '<h2>Lyrics</h2>'
      }
    })
 );

@endsection

... 
```

同样，我们有一个设置对象来调整小部件的行为。`templates`选项定义了用于呈现点击(结果)部分不同部分的模板。在上面的代码中，我们为*项*定义了一个模板，它是为结果集中的每一项呈现的。我们还为搜索没有结果的任何时候定义了一个模板。还有另一个用于 hits *标题*部分的模板。

这些模板可以是一个 [Mustache 模板](https://github.com/janl/mustache.js/)或者只是一个字符串，由一个匿名的 Javascript 函数返回。

在我们的例子中，`item`模板是存储在`<script>`标签中的小胡子模板，我们用 jQuery 获取:

```
// ...
item: $('#hits-temp').html(),
// ... 
```

这是我们的胡子模板。您可以将它放在`search.blade.php`模板中的任何位置:

**文件:** `resources/views/search.blade.php`

```
<script type="text/javascript" id="hits-temp">

    <div class="row song">
        <div class="pull-left band-thumbnail" style="background-image: url(/storage/artists/@{{photo}});"></div> 
        <a class="song-link" href="song/@{{objectID}}">@{{{_highlightResult.title.value}}}</a>
        <span class="song-artist">- <a href="?q=@{{artist}}&hPP=20&idx=songs&p=0&is_v=1">@{{artist}}</a> (@{{album}})</span>
        <span class="song-youtube-link"><a href="@{{youtube_link}}" class="btn btn-default btn-xs"><i class="glyphicon glyphicon-play"></i> Watch on Youtube</a></span>
    </div>

</script> 
```

在这个模板中，我们可以访问索引中每一行的属性，如`objectID`、`artist`、`title`、`photo`、`youtube_link`等。

为了防止 Blade 渲染 mustache 指令，我们在每个花括号集之前放置了一个`@`，让 Blade 知道这个表达式应该保持不变，稍后由 Instantsearch.js 处理。

此外，Algolia 为每个项目提供了一个名为`_highlightResult`的对象。该对象包含基于搜索关键字的每个属性的突出显示文本。这有助于显示搜索到的关键字匹配结果的哪一部分。

```
<a class="song-link" href="song/@{{objectID}}">@{{{_highlightResult.title.value}}}</a> 
```

请注意，我们将每个项目链接到其详细信息页面。

### 添加分页

分页小部件生成分页链接:

**文件:** `resources/views/search.blade.php`

```
...

search.addWidget(
    instantsearch.widgets.pagination({
      container: '#pagination-container'
    })
  );

... 
```

要查看这个小部件的所有可用选项，您应该查看一下[文档](https://community.algolia.com/instantsearch.js/documentation/#pagination)。

最后，我们启动`search`对象:

**文件:** `resources/views/search.blade.php`

```
// ...

 search.start();

// ... 
```

完整的代码现在应该是这样的:

**文件:** `resources/views/search.blade.php`

```
@extends('layouts.basic')

@section('content')

<div id="header">
    <div class="col-md-2"><h1>Coolyrics</h1></div>
    <div class="col-md-6">

          <div class="input-group" id="search-box">
             <div class="input-group-btn">
              <button type="button" class="btn btn-default dropdown-toggle" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
              <span>Genre</span>
              <span class="caret"></span></button>
              <ul id="genres" class="dropdown-menu">

              </ul>
            </div><!-- /btn-group -->
          </div>          

    </div>
    <div  id="login-link" class="col-md-4 text-right">
            <a href="{{route('login')}}">Login</a>
    </div>
</div>

<div class="container-fluid">
    <div id="hits-container"></div>
    <div id="pagination-container"></div>
</div>

@endsection

@section('scripts')

<!-- Scripts -->
@parent    
<script language="javascript" src="https://cdn.jsdelivr.net/jquery/3.1.1/jquery.min.js"></script>
<script src="https://cdn.jsdelivr.net/instantsearch.js/1/instantsearch.min.js"></script>
@endsection

@section('javascript')

<script type="text/javascript" id="hits-temp"> <div class="row song">
        <div class="pull-left band-thumbnail" style="background-image: url(/storage/artists/@{{photo}});"></div> 
        <a class="song-link" href="song/@{{objectID}}">@{{{_highlightResult.title.value}}}</a>
        <span class="song-artist">- <a href="?q=@{{artist}}&hPP=20&idx=songs&p=0&is_v=1">@{{artist}}</a> (@{{album}})</span>
        <span class="song-youtube-link"><a href="@{{youtube_link}}" class="btn btn-default btn-xs"><i class="glyphicon glyphicon-play"></i> Watch on Youtube</a></span>
    </div> </script>

<span></span>

<script> var search = instantsearch({
    appId: 'XI8PV16IK6',
    apiKey: '63c96991d445cb2de4fff316ac909c1a',
    indexName: 'songs',
    urlSync: true
  });

  search.addWidget(
    instantsearch.widgets.searchBox({
      container: '#search-box',
      placeholder: 'Search by artist, song, or lyrics',
      wrapInput: false,
      cssClasses: {
        input: 'form-control'
      }
    })
  );

  search.addWidget(
    instantsearch.widgets.hits({
      container: '#hits-container',
      templates: {
        item: $('#hits-temp').html(),
        empty: 'No lyrics was found! <p>We will be adding more lyrics to the database.</p>',
        header: '<h2>Lyrics</h2>'
      }
    })
  );

  search.addWidget(
    instantsearch.widgets.hits({
      container: '#hits-container',
      templates: {
        item: $('#hits-temp').html(),
        empty: 'No lyrics was found! <p>We will be adding more lyrics to the database.</p>',
        header: '<h2>Lyrics</h2>'
      }
    })
  );

  search.addWidget(
    instantsearch.widgets.menu({
      container: '#genres',
      attributeName: 'genres',
      limit: 10,
      templates: {
          header: '',
          footer: '',
          item: '<li><a href="@{{url}}">@{{name}}</></li>'
      }
    })
  );

  search.addWidget(
    instantsearch.widgets.pagination({
      container: '#pagination-container'
    })
  );

  search.start(); </script>

@endsection 
```

现在，如果我们重新加载页面，我们应该会看到一个很棒的搜索界面，开箱即用。没有额外的编码，没有麻烦。

![Lyrics Search interface](img/64e76845da3c022685545c7bfd079745.png)

请随意使用界面。搜索不同的术语，并打开链接，看看它看起来如何。你也可以在搜索的时候故意打几个错别字，看看回应。Algolia 的错别字容忍算法将自动检测您的用户正在搜索什么，并返回正确的结果。

好了，我想这就是 Scout 和 Algolia 的开始。你可以在 [Github](https://github.com/lavary/coolyrics) 上找到完整的代码，以防你想自己尝试一下。

要查看我们在本教程中构建的工作演示，请点击[此处](http://174.138.58.5)。

## 包扎

在 Laravel Scout 和 Algolia 的帮助下，我们创建了我们的极简歌词网站。

请注意，该项目仅用于教育目的，以最基本的形式实现，因此不应用于生产环境。请随意以任何方式修改代码。

为了更进一步，你可以进入你的 Algolia 档案，改变设置，看看结果。您还可以为每个术语添加同义词，以防您的数据库中没有该术语。

如果你对这个话题有任何疑问，或者我们错过了什么，请在下面的评论中告诉我们！

## 分享这篇文章