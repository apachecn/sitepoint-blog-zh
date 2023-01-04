# 分形:实用漫游

> 原文：<https://www.sitepoint.com/fractal-practical-walkthrough/>

如果您曾经开发过 API，您可能会遇到数据库模式更改的问题。如果您碰巧没有一个好的实现，那么在更改一些列名时，您必须重新构建整个模型。在本文中，我将演示如何使用[分形](http://fractal.thephpleague.com/)作为模型和 JSON 输出之间的一层。这篇文章将向你展示这个包如何使 API 开发变得更容易。

## 分形作为一个包

Fractal 是一个用 PHP 编写的项目，也是非常包联盟的一部分。这些包都符合几个要求，比如 PHP-FIG 的使用和单元测试覆盖率。分形主要是由[菲尔·斯特金](http://philsturgeon.uk/)开发的，并且仍然定期得到改进。也可以配合[作曲](https://getcomposer.org/)使用。

## 设置环境

出于演示的目的，我现在将使用 Silex 和 Illuminate/Database(Laravel 的 ORM 组件)建立一个框架。如果你没有任何这方面的经验也没关系。我要做的事情非常简单，我会尽可能多的解释。如果有不清楚的地方，请不要犹豫，留下你的评论。

我现在将建立一个框架。注意，如果你不想遵循**的步骤，你可以下载文章最后的全部代码**。现在开始在根文件夹中创建一个新文件夹。我们将从创建我们的`composer.json`文件开始，包含我们需要的所有依赖项。在我们的例子中:Silex 和 Illuminate\Database。像这样创建一个`composer.json`文件:

```
{
"require": {
"silex/silex": "~1.2",
"illuminate/database": "*"
},
}
```

用`composer install`安装软件包。

### 数据库

我将以一个在线音乐数据库为例。该数据库将提供几首歌曲的信息:歌曲名称，艺术家姓名，艺术家网站，专辑名称，发行日期和音乐标签。开始时，这些都在一个表中。如果你想亲自尝试，从这篇文章的库下载文件`1.sql`，并在你的数据库上运行它。

### 代码

为了在 Illuminate\Database 中使用 Silex，我们需要一些代码。在`app folder`里面创建一个新文件`index.php`。我们将在这里启动 Silex，连接到数据库并定义路由:

```
<?php
require("../vendor/autoload.php");

$app = new Silex\Application();

$app['database'] = require("database.php");

$app->mount('/tracks', include 'controllers/tracks.php');

$app->run();
```

在第一行，我们需要 composer 的自动加载文件。然后我们创建一个新的 Silex 应用程序并加载到`Illuminate/Database`中。然后我们为`/tracks`创建一个控制器，这样所有以`/tracks`开头的 URL 都将由`controllers/tracks.php`处理。`database.php`文件看起来是这样的，别忘了更改连接设置:

```
<?php
use Illuminate\Database\Capsule\Manager as Capsule;

$capsule = new Capsule;

$capsule->addConnection([
'driver' => 'mysql',
'host' => 'localhost',
'database' => 'musicstore',
'username' => 'root',
'password' => '',
'charset' => 'utf8',
'collation' => 'utf8_unicode_ci',
'prefix' => '',
]);

use Illuminate\Events\Dispatcher;
use Illuminate\Container\Container;
$capsule->setEventDispatcher(new Dispatcher(new Container));

$capsule->setAsGlobal();

$capsule->bootEloquent();

return $capsule;
```

## 第一个 API 版本

最初，数据库只包含一个表(导入`1.sql`)。此表包含有关曲目本身(标题、音乐标签)、专辑(标题和发行日期)和艺术家(名称和网站)的信息。我们的 API 将提供两件事:当导航到`/tracks`时，它将输出一个 json 列表，其中包含曲目 id、标题和艺术家。当导航到`/tracks/$id`时，它会将所有的轨迹信息输出为一个 json 对象。

### 曲目列表

我们从曲目列表开始。把这段代码放入`/controllers/tracks.php`:

```
<?php

$tracks = $app['controllers_factory'];

$tracks->get('/', function() use ($app)
{
$tracklist = Track::getTrackList();
$output = array("data" => $tracklist);
return json_encode($output);
});

return $tracks;
```

而这个型号的代码，`/models/Track.php`:

```
public static function getTrackList()
{
return Track::select('id','name','artist_name')->get();
}
```

它使用[Illuminate \ Database query builder](http://laravel.com/docs/queries)获取 id、名称和文章的列表，然后输出它们。输出将具有以下格式:

```
{
data: [
{
id: 1,
name: "Song 1",
artist_name: "Artist 1"
},
{
id: 2,
name: "Song 2",
artist_name: "Artist 2"
}
]
}
```

这按预期工作，但是如果表模式改变，JSON 输出也会改变。我们现在将使用一个[分形转换器](http://fractal.thephpleague.com/transformers/)来强制输出一个特定的结构。这个 Transformer 是一个扩展了`Fractal\TransformerAbstract`的类。在你的应用文件夹中创建一个名为`transformers`的变形金刚新文件夹。第一个变压器是`TracklistTransformer.php`，我们将单独为`/tracks`使用，它看起来像这样:

```
<?php
namespace Musicstore;

class TracklistTransformer extends \League\Fractal\TransformerAbstract
{
public function transform(Track $track)
{
return [
'id' => (int) $track->id,
'title' => $track->title,
'artist' => $track->artist_name
];
}
}
```

每个转换器都需要一个`transform()`方法，其参数是您想要转换的对象，在我们的例子中是类`Track`的一个实例。在 return 语句中，该结构被定义为关联数组。

我们现在将应用转换，并通过使用一个[串行化器](http://fractal.thephpleague.com/serializers/)来创建输出数组。我们将使用`DataArraySerializer`，因为这将把我们的数据放在 JSON 对象的`data-key`下。这允许您在响应中包含其他信息，如状态代码或错误消息。为了使用这个序列化程序，我们需要一个 manager 类的实例。为此，我将使用 DI 容器，这样我们就不必每次都重写样板代码。新的`/controllers/tracks.php`文件如下所示:

```
<?php

$tracks = $app['controllers_factory'];

$tracks->get('/', function() use ($app)
{
$tracks = Track::getTrackList();
$tracklist = new \League\Fractal\Resource\Collection($tracks, new TracklistTransformer);
$output = $app['serializer']->createData($tracklist)->toArray();
return json_encode($output);
});

return $tracks;
```

我们使用`$tracklist = new \League\Fractal\Resource\Collection($tracks, new TracklistTransformer);`来应用转换。在这条路线中，我们使用了一个曲目集合:我们的`$tracks`变量是类[Illuminate \ Database \口才\Collection](https://github.com/illuminate/database/blob/master/Eloquent/Collection.php) 的一个对象。这个类与分形及其变形器兼容。对于另一条路线，我们将在单个表项目上使用转换器。但稍后会详细介绍。

为了使用`$app['serializer']`，将该代码放在`index.php`中，就在`app['database']`语句的下面:

```
$app['serializer'] = $app->share(function()
{
$manager = new \League\Fractal\Manager();
$manager->setSerializer(new League\Fractal\Serializer\DataArraySerializer());
return $manager;
});
```

现在您有了一个工作的曲目列表。

### 跟踪信息

我现在将快速演示如何显示特定轨迹的信息，然后使用分形对数据模式进行编辑。打开`controllers/tracks.php`并在 return 语句上方添加以下代码:

```
<?php
$tracks->get('/{id}', function($id) use ($app)
{
return Track::find($id);
});
```

返回一个类 Track 的对象，其 id 与 URL 匹配。这同样有效，但不是一个好方法，所以我们将实现分形。创建一个新的变压器`transformers/TrackTransformer.php`:

```
<?php
namespace Musicstore;

class TrackTransformer extends \League\Fractal\TransformerAbstract
{
public function transform(Track $track)
{
return [
'id' => (int) $track->id,
'title' => $track->title,
'artist_name' => $track->artist_name,
'artist_website' => $track->artist_website,
'album_name' => $track->album_name,
'album_release' => $track->album_release,
'album_label' => $track->album_label
];
}
}
```

在您的控制器中编辑到此的路线:

```
$tracks->get('/{id}', function($id) use ($app)
{
$track = Track::find($id);
$track = new \League\Fractal\Resource\Item($track, new TrackTransformer);
$output = $app['serializer']->createData($track)->toArray();
return json_encode($output);
});
```

正如您所看到的，它与前面的路线非常相似，只是在这种情况下，我们处理的不是一个`Collection`而是一个`Item`。如果您使用的是`1.sql`，您可以导航到`localhost/app/tracks/1`并查看一些样本数据。

## 更新的 API 版本

我们的音乐商店想要扩展并提供更多关于艺术家和专辑的信息。在新的数据库模式中，艺术家和专辑有单独的表。您可以下载`2.sql`来使用新的模式。这些表格如下所示:

曲目:

*   身份证明（identification）
*   标题
*   艺术的
*   相册 id

艺术家:

*   身份证明（identification）
*   名字
*   网站

相册:

*   身份证明（identification）
*   名字
*   释放；排放；发布
*   标签

在 tracks 表中，artist 和 album 列是整数，对应于各自表中的行。我们将使用变压器来保持输出结构不变。为了方便起见，我们将使用`Illuminate\Database`的一些特性。进入`models/Track.php`并添加另外两个方法:

```
public function artist()
{
return $this->belongsTo('\Musicstore\Artist');
}

public function album()
{
return $this->belongsTo('\Musicstore\Album');
}
```

如果我们有一个`Track`对象，我们可以通过使用`$track->album`和`$track->artist`来访问它的`Album`和`Artist`。首先，我们需要制作这些模型，因此创建两个文件`models/Artist.php`和`models/Album.php`:

```
// models/Artist.php
<?php
namespace Musicstore;
class Artist extends \Illuminate\Database\Eloquent\Model
{
public function tracks()
{
return $this->hasMany('\Musicstore\Track');
}
}

// models/Album.php
<?php
namespace Musicstore;
class Album extends \Illuminate\Database\Eloquent\Model
{
public function tracks()
{
return $this->hasMany('\Musicstore\Track');
}
}
```

我们现在要做的最后一件事是编辑我们的变形金刚:

```
// transformers/TrackTransformer.php
return [
'id' => (int) $track->id,
'title' => $track->title,
'artist_name' => $track->artist->name,
'artist_website' => $track->artist->website,
'album_name' => $track->album->name,
'album_release' => $track->album->release,
'album_label' => $track->album->label
];

// transformers/TracklistTransformer.php
return [
'id' => (int) $track->id,
'title' => $track->title,
'artist' => $track->artist->name
];
```

如果你测试了所有的东西，你会发现它是有效的。

## 结论

正如我所演示的，通过在输出之前添加分形，您可以轻松地执行数据库更改，而不会被最终用户注意到。您可以在 [my repository](https://github.com/AlexCogn/Musicstore) 下载完整的示例代码。如果有不清楚的地方，请不要犹豫留下评论。

## 分享这篇文章