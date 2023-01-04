# 如何用 CouchDB 创建口袋妖怪产卵位置记录器

> 原文：<https://www.sitepoint.com/how-to-create-a-pokemon-spawn-locations-recorder-with-couchdb/>

在之前的一篇文章中，已经向您介绍了 CouchDB 。这一次，你将创建一个成熟的应用程序，在这里你可以应用你所学到的东西。在本教程的最后，您还将学习如何保护您的数据库。

## 项目概述

你要建立一个口袋妖怪产卵位置记录器。

这将允许用户保存他们在 Pokemon Go 上遇到的怪物的位置。谷歌地图将被用来搜索位置和一个标记放置在精确的位置。一旦用户对位置感到满意，就可以与标记进行交互，这时它会显示一个模态框，允许用户输入口袋妖怪的名称并保存位置。当下一个用户出现并搜索同一位置时，之前用户添加的值将作为标记绘制在地图中。这是该应用程序的外观:

![pokespawn screen](img/106bf3cc7d3f474fd952c4c10f6d5eb6.png)

该项目的完整源代码可在 [Github](https://github.com/sitepoint-editors/pokespawn) 上获得。

## 设置开发环境

如果你没有一个好的、隔离的开发环境，建议你使用 [Homestead 改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。

这个盒子没有安装 CouchDB，所以你需要手动安装；但不仅仅是普通的 CouchDB。该应用程序需要处理地理数据(纬度和经度):您将为 CouchDB 提供来自 Google Maps 的边界框信息。边界框表示地图中当前显示的区域，用户之前添加到该区域的所有坐标也将显示在地图上。默认情况下，CouchDB 不能做到这一点，这就是为什么你需要安装一个名为[geo touch](https://github.com/couchbase/geocouch)的插件，以便给 CouchDB 一些空间超能力。

最简单的方法是使用[geo touch docker 容器](https://github.com/elecnix/docker-geocouch)。您也可以尝试手动安装 GeoCouch，但这需要您从源代码安装 CouchDB 并手动配置。我真的不推荐这种方法，除非你有 [unix 胡子](https://www.google.com/search?site=&tbm=isch&source=hp&biw=1600&bih=765&q=unix+beard&oq=unix+beard)。

继续将 Docker 安装到您正在使用的虚拟机中，并在完成后返回这里。

### 安装 GeoCouch

首先，克隆 [repo](https://github.com/elecnix/docker-geocouch) 并在创建的目录中导航。

```
git clone git@github.com:elecnix/docker-geocouch.git
cd docker-geocouch 
```

接下来，打开`Dockerfile`，用下面的代码替换获取 CouchDB 的脚本:

```
# Get the CouchDB source
RUN cd /opt; wget http://www-eu.apache.org/dist/couchdb/source/${COUCH_VERSION}/a$
    tar xzf /opt/apache-couchdb-${COUCH_VERSION}.tar.gz 
```

您需要这样做，因为当前正在使用的下载 URL[已经失败了](https://github.com/elecnix/docker-geocouch/pull/4)。

构建 docker 映像:

```
docker build -t elecnix/docker-geocouch:1.6.1 . 
```

这需要一段时间，取决于你的网络连接，所以去吃点零食吧。完成后，创建容器并启动它:

```
docker create -ti -p 5984:5984 elecnix/docker-geocouch:1.6.1
docker start <container id> 
```

启动后，您可以通过执行以下命令来测试它是否正在运行:

```
curl localhost:5984 
```

在虚拟机外部，如果您正确转发端口，将会:

```
curl 192.168.33.10:5984 
```

它应该返回以下内容:

```
{"couchdb":"Welcome","uuid":"2f0b5e00e9ce08996ace6e66ffc1dfa3","version":"1.6.1","vendor":{"version":"1.6.1","name":"The Apache Software Foundation"}} 
```

请注意，我将在整篇文章中不断引用`192.168.33.10`。这是分配给 Scotchbox 的 IP，就是我用的流浪盒子。如果你用的是[家园改良版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)，IP 就是`192.168.10.10`。您可以使用此 IP 访问应用程序。如果你完全使用其他东西，根据需要进行调整。

## 设置项目

您将使用 Slim 框架来加速应用程序的开发。使用 [Composer](https://getcomposer.org/) 创建一个新项目:

```
php composer create-project slim/slim-skeleton pokespawn 
```

`pokespawn`是项目的名称，所以 Composer 安装完成后，请继续导航到该目录。然后，安装以下额外的软件包:

```
composer require danrovito/pokephp guzzlehttp/guzzle gregwar/image vlucas/phpdotenv 
```

以下是对每一种方法的简要概述:

*   `danrovito/pokephp`–方便与口袋妖怪 API 对话。
*   `guzzlehttp/guzzle`–用于向 CouchDB 服务器发出请求。
*   `gregwar/image`–用于调整口袋妖怪 API 返回的口袋妖怪精灵的大小。
*   `vlucas/phpdotenv`–用于存储配置值。

### 设置数据库

从浏览器访问[蒲团](https://www.sitepoint.com/a-pokemon-crash-course-on-couchdb/)并创建一个名为`pokespawn`的新数据库。创建完成后，进入数据库并创建一个新视图。您可以通过点击**视图**下拉菜单并选择**临时视图**来实现。在**地图功能**的文本区域内添加以下内容:

```
function(doc){
  if(doc.doc_type == 'pokemon'){
    emit(doc.name, null);
  }
} 
```

![create new view](img/1a41ca9086154be446bd07fa8bdbdbbc.png)

完成后，点击**另存为**按钮，添加`pokemon`作为设计文档的名称，添加`by_name`作为视图名称。按下**保存**保存视图。稍后，您将使用该视图根据用户输入的内容来建议口袋妖怪的名称。

![save view](img/25ecba2d6db72e17ca40e29d8d2c59bc.png)

接下来，创建一个用于响应空间搜索的设计文档。您可以通过在**视图**下拉菜单中选择**设计文档**，然后点击**新文档**来完成。一旦进入创建设计文档的页面，点击**添加字段**按钮，添加`spatial`作为字段名称，并添加以下内容作为值:

```
{
   "points": "function(doc) {\n    if (doc.loc) {\n        emit([{\n            type: \"Point\",\n            coordinates: [doc.loc[0], doc.loc[1]]\n        }], [doc.name, doc.sprite]);\n    }};"
} 
```

该设计文档利用了 GeoCouch 提供的空间功能。它做的第一件事是检查文档中是否有一个`loc`字段。`loc`字段是一个包含特定位置坐标的数组，第一项包含纬度，第二项包含经度。如果文档满足这个标准，它就像普通视图一样使用`emit()`功能。`key`是一个 [GeoJSON](http://geojson.org/) 几何体，值是一个包含口袋妖怪和精灵名称的数组。

当您向设计文档发出请求时，您需要指定具有 JSON 数组格式的`start_range`和`end_range`。每一项可以是一个数字，也可以是一个`null`。如果您想要一个[开放范围](https://github.com/couchbase/geocouch/wiki/Spatial-Views-API#open-ranges)，则使用`null`。下面是一个请求示例:

```
curl -X GET --globoff 'http://192.168.33.10:5984/pokespawn/_design/location/_spatial/points?start_range=[-33.87049924568689,151.2149563379288]&end_range=[33.86709181198735,151.22298150730137]' 
```

它的输出:

```
{
  "update_seq": 289,
  "rows":[{
     "id":"c8cc500c68f679a6949a7ff981005729",
     "key":[
        [
           -33.869107336588,
           -33.869107336588
        ],
        [
           151.21772705984,
           151.21772705984
        ]
     ],
     "bbox":[
        -33.869107336588,
        151.21772705984,
        -33.869107336588,
        151.21772705984
     ],
     "geometry":{
        "type":"Point",
        "coordinates":[
           -33.869107336588,
           151.21772705984
        ]
     },
     "value":[
        "snorlax",
        "143.png"
     ]
  }]
} 
```

如果你想了解更多关于地理触摸的具体操作，请务必阅读[文档](https://github.com/couchbase/geocouch/blob/master/gc-couchdb/README.md)或[维基](https://github.com/couchbase/geocouch/wiki/Spatial-Views-API)。

## 创建项目

现在您已经准备好编写一些代码了。首先，您将查看后端代码，然后继续查看前端代码。

### 戳进口商

该应用程序需要一些口袋妖怪数据已经在数据库中，然后才能使用，因此需要一个只在本地执行的脚本。在项目目录的根目录下创建一个`poke-importer.php`文件，并添加以下内容:

```
<?php
require 'vendor/autoload.php';

set_time_limit(0);

use PokePHP\PokeApi;
use Gregwar\Image\Image;

$api = new PokeApi;

$client = new GuzzleHttp\Client(['base_uri' => 'http://192.168.33.10:5984']); //create a client for talking to CouchDB

$pokemons = $api->pokedex(2); //make a request to the API
$pokemon_data = json_decode($pokemons); //convert the json response to array

foreach ($pokemon_data->pokemon_entries as $row) {
    $pokemon = [
        'id' => $row->entry_number,
        'name' => $row->pokemon_species->name,
        'sprite' => "{$row->entry_number}.png",
        'doc_type' => "pokemon"
    ];
    //get image from source, save it then resize.
    Image::open("https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/{$row->entry_number}.png")
         ->resize(50, 50)
         ->save('public/img/' . $row->entry_number . '.png');
    //save the pokemon data to the database
    $client->request('POST', "/pokespawn", [
        'headers' => [
                'Content-Type' => 'application/json'
        ],
        'body' => json_encode($pokemon)
    ]);

    echo $row->pokemon_species->name . "\n";
}
echo "done!"; 
```

这个脚本向[口袋妖怪 API](http://pokeapi.co/) 的 Pokedex 端点发出请求。此端点需要您希望它返回的 Pokedex 版本的 ID。由于 Pokemon Go 目前只允许玩家从第一代开始抓 Pokemon，所以供应`2`作为 ID。这将返回原口袋妖怪游戏关东地区的所有口袋妖怪。然后遍历数据，提取所有必要的信息，保存 sprite，并使用提取的数据创建一个新文档。

### 路线

打开`src/routes.php`文件，添加以下路线:

```
<?php
$app->get('/', 'HomeController:index');
$app->get('/search', 'HomeController:search');
$app->post('/save-location', 'HomeController:saveLocation');
$app->post('/fetch', 'HomeController:fetch'); 
```

每条路线都将响应可以在整个应用程序中执行的操作。根路由返回主页，`search`路由返回口袋妖怪名称建议，`save-location`路由保存位置，`fetch`路由返回特定位置的口袋妖怪。

### 家庭控制器

在`src`目录下，创建一个`app/Controllers`文件夹，并在里面创建一个`HomeController.php`文件。这将执行每个路由所需的所有操作。代码如下:

```
<?php
namespace App\Controllers;

class HomeController
{
  protected $renderer;

  public function __construct($renderer)
  {
    $this->renderer = $renderer; //the twig renderer
    $this->db = new \App\Utils\DB; //custom class for talking to couchdb
  }

  public function index($request, $response, $args)
  {
    //render the home page
    return $this->renderer->render($response, 'index.html', $args);
  }

  public function search()
  {
    $name = $_GET['name']; //name of the pokemon being searched
    return $this->db->searchPokemon($name); //returns an array of suggestions based on the user input
  }

  public function saveLocation()
  {
    $id = $_POST['pokemon_id']; //the ID assigned by CouchDB to the Pokemon
    return $this->db->savePokemonLocation($id, $_POST['pokemon_lat'], $_POST['pokemon_lng']); //saves the pokemon location to CouchDB and returns the data needed to plot the pokemon in the map
  }

  public function fetch()
  {
    return json_encode($this->db->fetchPokemons($_POST['north_east'], $_POST['south_west'])); //returns the pokemon's within the bounding box of Google map.
  }
} 
```

家庭控制器使用通过构造函数传入的`$renderer`来呈现应用程序的主页。它还使用了您将很快创建的`DB`类。

### 与 CouchDB 交谈

在`app`目录下创建一个`Utils/DB.php`文件。打开文件并创建一个类:

```
<?php
namespace App\Utils;

class DB
{

} 
```

在类内部，创建一个新的 Guzzle 客户端。您使用 Guzzle 而不是一些用于 CouchDB 的 PHP 客户端，因为您可以用它做任何您想做的事情。

```
private $client;

public function __construct()
{
    $this->client = new \GuzzleHttp\Client([
        'base_uri' => getenv('BASE_URI')
    ]);
} 
```

该配置来自项目根目录下的`.env`文件。这包含 CouchDB 的基本 URL。

```
BASE_URI="http://192.168.33.10:5984" 
```

`searchPokemon`负责返回自动建议功能使用的数据。因为 CouchDB 实际上并不支持您在 SQL 中习惯的`LIKE`条件，所以您使用了一点小技巧来模仿它。这里的技巧是使用`start_key`和`end_key`，而不是只返回精确匹配的`key`。`fff0`是分配在基本多语言平面最末端的特殊 unicode 字符之一。这使得它很适合追加到被搜索的实际字符串的末尾，由于它的值很高，使得其余的字符成为可选的。请注意，这种方法只适用于短词，因此对于搜索口袋妖怪的名字来说绰绰有余。

```
public function searchPokemon($name)
{
  $unicode_char = '\ufff0';
  $data = [
    'include_docs' => 'true',
    'start_key' => '"' . $name . '"',
    'end_key' => '"' . $name . json_decode('"' . $unicode_char .'"') . '"'
  ];
  //make a request to the view you created earlier
  $doc = $this->makeGetRequest('/pokespawn/_design/pokemon/_view/by_name', $data);
  if (count($doc->rows) > 0) {
    $data = [];
    foreach ($doc->rows as $row) {
      $data[] = [
        $row->key,
        $row->id
      ];
    }
    return json_encode($data);
  }
  $result = ['no_result' => true];
  return json_encode($result);
} 
```

`makeGetRequest`用于执行对 CouchDB 的读请求，而`makePostRequest`用于写请求。

```
public function makeGetRequest($endpoint, $data = [])
{
  if (!empty($data)) {
    //make a GET request to the endpoint specified, with the $data passed in as a query parameter
    $response = $this->client->request('GET', $endpoint, [
      'query' => $data
    ]);
  } else {
    $response = $this->client->request('GET', $endpoint);
  }
  return $this->handleResponse($response);
}

private function makePostRequest($endpoint, $data)
{
  //make a POST request to the endpoint specified, passing in the $data for the request body
  $response = $this->client->request('POST', $endpoint, [
    'headers' => [
        'Content-Type' => 'application/json'
    ],
    'body' => json_encode($data)
  ]);
  return $this->handleResponse($response);
} 
```

`savePokemonLocation`保存谷歌地图标记当前指向的坐标，以及`name`和`sprite`。还添加了一个`doc_type`字段，以便于检索所有与位置相关的文档。

```
public function savePokemonLocation($id, $lat, $lng)
{
  $pokemon = $this->makeGetRequest("/pokespawn/{$id}"); //get pokemon details based on ID

  //check if supplied data are valid
  if (!empty($pokemon->name) && $this->isValidCoordinates($lat, $lng)) {
    $lat = (double) $lat;
    $lng = (double) $lng;
    //construct the data to be saved to the database
    $data = [
      'name' => $pokemon->name,
      'sprite' => $pokemon->sprite,
      'loc' => [$lat, $lng],
      'doc_type' => 'pokemon_location'
    ];

    $this->makePostRequest('/pokespawn', $data); //save the location data
    $pokemon_data = [
      'type' => 'ok',
      'lat' => $lat,
      'lng' => $lng,
      'name' => $pokemon->name,
      'sprite' => $pokemon->sprite
    ];
    return json_encode($pokemon_data); //return the data needed by the pokemon marker
  }
  return json_encode(['type' => 'fail']); //invalid data
} 
```

`isValidCoordinates`检查纬度和经度值的格式是否有效。

```
private function isValidCoordinates($lat = '', $lng = '')
{
  $coords_pattern = '/^[+\-]?[0-9]{1,3}\.[0-9]{3,}\z/';
  if (preg_match($coords_pattern, $lat) && preg_match($coords_pattern, $lng)) {
    return true;
  }
  return false;
} 
```

`fetchPokemons`是向您之前创建的空间搜索设计文档发出请求的函数。这里，您指定西南坐标作为`start_range`的值，东北坐标作为`end_range`的值。响应也仅限于前 100 行，以防止请求太多数据。前面，您还看到了 CouchDB 返回的一些数据并不是真正需要的。提取然后只返回前端需要的数据是有用的。我选择把它作为一个优化留到下一天。

```
public function fetchPokemons($north_east, $south_west)
{
  $north_east = array_map('doubleval', $north_east); //convert all array items to double
  $south_west = array_map('doubleval', $south_west);
  $data = [
    'start_range' => json_encode($south_west),
    'end_range' => json_encode($north_east),
    'limit' => 100
  ];
  $pokemons = $this->makeGetRequest('/pokespawn/_design/location/_spatial/points', $data); //fetch all pokemon's that are in the current area
  return $pokemons;
} 
```

`handleResponse`将 CouchDB 返回的 JSON 字符串转换成数组。

```
private function handleResponse($response)
{
  $doc = json_decode($response->getBody()->getContents());
  return $doc;
} 
```

打开根目录下的`composer.json`，在`require`属性的正下方添加以下内容，然后执行`composer dump-autoload`。这允许您自动加载`src/app`目录中的所有文件，并使其在`App`名称空间中可用:

```
"autoload": {
    "psr-4": {
        "App\\": "src/app"
    }
} 
```

最后，将家庭控制器注入容器。您可以通过打开`src/dependencies.php`文件并在底部添加以下内容来实现:

```
$container['HomeController'] = function ($c) {
    return new App\Controllers\HomeController($c->renderer);
}; 
```

这允许您将树枝渲染器传递到家庭控制器，并使`HomeController`可从路由器访问。

### 主页模板

现在，您已经准备好进行前端了。首先，在项目目录的根目录下创建一个`templates/index.html`文件，并添加以下内容:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>PokéSpawn</title>
    <link rel="stylesheet" href="lib/picnic/picnic.min.css">
    <link rel="stylesheet" href="lib/remodal/dist/remodal.css">
    <link rel="stylesheet" href="lib/remodal/dist/remodal-default-theme.css">
    <link rel="stylesheet" href="lib/javascript-auto-complete/auto-complete.css">
    <link rel="stylesheet" href="css/style.css">
    <link rel="icon" href="favicon.ico"><!-- by Maicol Torti https://www.iconfinder.com/Maicol-Torti -->
</head>
<body>
  <div id="header">
    <div id="title">
      <img src="img/logo.png" alt="logo" class="header-item" />
      <h1 class="header-item">PokéSpawn</h1>
    </div>
    <input type="text" id="place" class="controls" placeholder="Where are you?"><!-- text field for typing the location -->
  </div>

  <div id="map"></div>
  <!-- modal for saving pokemon location -->
  <div id="add-pokemon" class="remodal" data-remodal-id="modal">
    <h3>Plot Pokémon Location</h3>
    <form method="POST" id="add-pokemon-form">
      <div>
        <input type="hidden" name="pokemon_id" id="pokemon_id"><!-- id of the pokemon in CouchDB-->
        <input type="hidden" name="pokemon_lat" id="pokemon_lat"><!--latitude of the red marker -->
        <input type="hidden" name="pokemon_lng" id="pokemon_lng"><!--longitude of the red marker -->
        <input type="text" name="pokemon_name" id="pokemon_name" placeholder="Pokémon name"><!--name of the pokemon whose location is being added -->
      </div>
      <div>
        <button type="button" id="save-location">Save Location</button><!-- trigger the submission of location to CouchDB -->
      </div>
    </form>
  </div>

  <script src="lib/zepto.js/dist/zepto.min.js"></script><!-- event listening, ajax -->
  <script src="lib/remodal/dist/remodal.min.js"></script><!-- for modal box -->
  <script src="lib/javascript-auto-complete/auto-complete.min.js"></script><!-- for autocomplete text field -->
  <script src="js/main.js"></script>

  <script src="https://maps.googleapis.com/maps/api/js?key=YOUR_GOOGLEMAP_APIKEY&callback=initMap&libraries=places" defer></script><!-- for showing a map-->
</body>
</html> 
```

在`<head>`中是应用程序使用的各种库的样式，以及应用程序的样式。在`<body>`中是用于搜索位置的文本字段、地图容器和用于保存新位置的模式。下面是应用程序中使用的脚本。别忘了用你自己的 API 键替换谷歌地图脚本中的`YOUR_GOOGLEMAP_APIKEY`。

### Java Script 语言

对于主 JavaScript 文件(`public/js/main.js`)，首先创建变量来存储整个文件中需要的值。

```
var modal = $('#add-pokemon').remodal(); //initialize modal
var map; //the google map

var markers = []; //an array for storing all the pokemon markers currently plotted in the map 
```

接下来，创建初始化地图的函数。指定了一个`min_zoomlevel`来防止用户缩小，直到他们可以看到整个世界地图。您已经为 CouchDB 可以返回的结果添加了一个限制，但这也是一个很好的补充，可以防止用户期望他们可以从整个世界中选择数据。

```
function initMap() {
  var min_zoomlevel = 18;
  map = new google.maps.Map(document.getElementById('map'), {
    center: {lat: -33.8688, lng: 151.2195}, //set
    disableDefaultUI: true, //hide default UI controls
    zoom: min_zoomlevel, //set default zoom level
    mapTypeId: 'roadmap' //set type of map
  });

  //continue here...
} 
```

为用户想要添加的精确定位位置创建标记。然后，添加一个事件侦听器，用于在按下标记时打开添加位置的模式:

```
marker = new google.maps.Marker({
  map: map,
  position: map.getCenter(),
  draggable: true
});

marker.addListener('click', function(){
  var position = marker.getPosition();
  $('#pokemon_lat').val(position.lat());
  $('#pokemon_lng').val(position.lng());
  modal.open();
}); 
```

初始化搜索框:

```
var header = document.getElementById('header');
var input = document.getElementById('place');
var searchBox = new google.maps.places.SearchBox(input); //create a google map search box
map.controls[google.maps.ControlPosition.TOP_LEFT].push(header); //position the header at the top left side of the screen 
```

添加各种地图监听器:

```
map.addListener('bounds_changed', function() { //executes when user drags the map
  searchBox.setBounds(map.getBounds()); //make places inside the current area a priority when searching
});

map.addListener('zoom_changed', function() { //executes when user zooms in or out of the map
  //immediately set the zoom to the minimum zoom level if the current zoom goes over the minimum
  if (map.getZoom() < min_zoomlevel) map.setZoom(min_zoomlevel);
});

map.addListener('dragend', function() { //executes the moment after the map has been dragged
  //loop through all the pokemon markers and remove them from the map
  markers.forEach(function(marker) {
    marker.setMap(null);
  });
  markers = [];

  marker.setPosition(map.getCenter()); //always place the marker at the center of the map
  fetchPokemon(); //fetch some pokemon in the current viewable area
}); 
```

当搜索框中的位置发生变化时，添加一个事件侦听器。

```
searchBox.addListener('places_changed', function() { //executes when the place in the searchbox changes
  var places = searchBox.getPlaces();

  if (places.length == 0) {
    return;
  }

  var bounds = new google.maps.LatLngBounds();
  var place = places[0]; //only get the first place
  if (!place.geometry) {
    return;
  }

  marker.setPosition(place.geometry.location); //put the marker at the location being searched
  if (place.geometry.viewport) {
    // only geocodes have viewport
    bounds.union(place.geometry.viewport);
  } else {
    bounds.extend(place.geometry.location);
  }

  map.fitBounds(bounds); //adjust the current map bounds to that of the place being searched
  fetchPokemon(); //fetch some Pokemon in the current viewable area

}); 
```

`fetchPokemon`函数负责获取之前绘制在地图当前可视区域的口袋妖怪。

```
function fetchPokemon(){

  //get the northeast and southwest coordinates of the viewable area of the map
  var bounds = map.getBounds();
  var north_east = [bounds.getNorthEast().lat(), bounds.getNorthEast().lng()];
  var south_west = [bounds.getSouthWest().lat(), bounds.getSouthWest().lng()];

  $.post(
    '/fetch',
    {
      north_east: north_east,
      south_west: south_west
    },
    function(response){
      var response = JSON.parse(response);
      response.rows.forEach(function(row){ //loop through all the results returned
        var position = new google.maps.LatLng(row.geometry.coordinates[0], row.geometry.coordinates[1]); //create a new google map position
        //create a new marker using the position created above
        var poke_marker = new google.maps.Marker({
          map: map,
          title: row.value[0], //name of the pokemon
          position: position,
          icon: 'img/' + row.value[1] //pokemon image that was saved locally
        });

        //create an infowindow for the marker
        var infowindow = new google.maps.InfoWindow({
          content: "<strong>" + row.value[0] + "</strong>"
        });
        //when clicked it will show the name of the pokemon
        poke_marker.addListener('click', function() {
          infowindow.open(map, poke_marker);
        });
        markers.push(poke_marker);
      });
    }
  );
} 
```

这是为输入口袋妖怪名字的文本字段添加自动建议功能的代码。指定了一个`renderItem`函数来定制用于呈现每个建议的 HTML。这允许您添加口袋妖怪的 ID 作为数据属性，一旦选择了建议，您就可以使用它来设置`pokemon_id`字段的值。

```
new autoComplete({
  selector: '#pokemon_name', //the text field to add the auto-complete
  source: function(term, response){
    //use the results returned by the search route as a data source
    $.getJSON('/search?name=' + term, function(data){
      response(data);
    });
  },
  renderItem: function (item, search){ //the code for rendering each suggestions.
      search = search.replace(/[-\/\\^$*+?.()|[\]{}]/g, '\\$&');
      var re = new RegExp("(" + search.split(' ').join('|') + ")", "gi");
      return '<div class="autocomplete-suggestion" data-id="' + item[1] + '" data-val="' + item[0] + '">' + item[0].replace(re, "<b>$1</b>")+'</div>';
  },
  onSelect: function(e, term, item){ //executed when a suggestion is selected
    $('#pokemon_id').val(item.getAttribute('data-id'));
  }
}); 
```

当按下**保存位置**按钮时，向服务器请求将口袋妖怪位置添加到 CouchDB。

```
$('#save-location').click(function(e){
  $.post('/save-location', $('#add-pokemon-form').serialize(), function(response){
    var data = JSON.parse(response);
    if(data.type == 'ok'){
      var position = new google.maps.LatLng(data.lat, data.lng); //create a location
      //create a new marker and use the location
      var poke_marker = new google.maps.Marker({
        map: map,
        title: data.name, //name of the pokemon
        position: position,
        icon: 'img/' + data.sprite //pokemon image
      });
      //create an infowindow for showing the name of the pokemon
      var infowindow = new google.maps.InfoWindow({
        content: "<strong>" + data.name + "</strong>"
      });
      //show name of pokemon when marker is clicked
      poke_marker.addListener('click', function() {
        infowindow.open(map, poke_marker);
      });

      markers.push(poke_marker);
    }
    modal.close();
    $('#pokemon_id, #pokemon_lat, #pokemon_lng, #pokemon_name').val(''); //reset the form
  });

});

$('#add-pokemon-form').submit(function(e){
  e.preventDefault(); //prevent the form from being submited on enter
}) 
```

### 风格

创建一个`public/css/styles.css`文件并添加以下样式:

```
html, body {
    height: 100%;
    margin: 0;
    padding: 0;
}

#header {
    text-align: center;
}

#title {
    float: left;
    padding: 5px;
    color: #f5716a;
}

.header-item {
    padding-top: 10px;
}

h1.header-item {
    font-size: 14px;
    margin: 0;
    padding: 0;
}

#map {
    height: 100%;
}

.controls {
    margin-top: 10px;
    border: 1px solid transparent;
    border-radius: 2px 0 0 2px;
    box-sizing: border-box;
    -moz-box-sizing: border-box;
    height: 32px;
    outline: none;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.3);
}

#place {
    background-color: #fff;
    margin-left: 12px;
    padding: 0 11px 0 13px;
    text-overflow: ellipsis;
    width: 300px;
    margin-top: 20px;
}

#place:focus {
    border-color: #4d90fe;
}

#type-selector {
    color: #fff;
    background-color: #4d90fe;
    padding: 5px 11px 0px 11px;
}

#type-selector label {
    font-family: Roboto;
    font-size: 13px;
    font-weight: 300;
}

#target {
    width: 345px;
}

.remodal-wrapper {
    z-index: 100;
}

.remodal-overlay {
    z-index: 100;
} 
```

## 保护 CouchDB

默认情况下，CouchDB 对所有人开放。这意味着一旦你把它暴露在互联网上，任何人都可以破坏你的数据库。任何人都可以通过简单地使用 [Curl](https://curl.haxx.se/) 、 [Postman](https://www.getpostman.com/) 或任何其他用于发出 HTTP 请求的工具来进行任何数据库操作。事实上，这种暂时的状态甚至有一个名字:行政党。在之前的教程中，甚至在之前创建新的数据库、视图和设计文档时，您已经看到了这一点。所有这些操作只能由服务器管理员执行，但是您已经在没有登录或其他任何操作的情况下完成了这些操作。还不服气？尝试在您的本地机器上执行:

```
curl -X PUT http://192.168.33.10:5984/my_newdatabase 
```

如果您的 CouchDB 安装中还没有服务器管理员，您将得到以下响应:

```
{"ok":true} 
```

呀，对吧？好消息是有一个简单的解决方法。你所要做的就是创建一个服务器管理员。您可以使用以下命令来完成此操作:

```
curl -X PUT http://192.168.33.10:5984/_config/admins/kami -d '"mysupersecurepassword"' 
```

上面的命令创建了一个名为“kami”的新服务器管理员，密码为“mysupersecurepassword”。

默认情况下，CouchDB 没有任何服务器管理员，所以一旦您创建了一个，管理团队就结束了。请注意，服务器管理员拥有上帝般的权力，所以您最好只创建一两个。然后创建一些只能执行 CRUD 操作的数据库管理员。您可以通过执行以下命令来实现这一点:

```
curl -HContent-Type:application/json -vXPUT http://kami:mysupersecurepassword@192.168.33.10:5984/_users/org.couchdb.user:plebian --data-binary '{"_id": "org.couchdb.user:plebian","name": "plebian","roles": [],"type": "user","password": "mypass"}' 
```

如果成功，它将返回类似以下内容的响应:

```
*   Trying 192.168.33.10...
* Connected to 192.168.33.10 (192.168.33.10) port 5984 (#0)
* Server auth using Basic with user 'root'
> PUT /_users/org.couchdb.user:plebian HTTP/1.1
> Host: 192.168.33.10:5984
> Authorization: Basic cm9vdDpteXN1cGVyc2VjdXJlcGFzc3dvcmQ=
> User-Agent: curl/7.47.0
> Accept: */*
> Content-Type:application/json
> Content-Length: 101
>
* upload completely sent off: 101 out of 101 bytes
< HTTP/1.1 201 Created
< Server: CouchDB/1.6.1 (Erlang OTP/R16B03)
< Location: http://192.168.33.10:5984/_users/org.couchdb.user:plebian
< ETag: "1-9c4abdc905ecdc9f0f56921d7de915b9"
< Date: Thu, 18 Aug 2016 07:57:20 GMT
< Content-Type: text/plain; charset=utf-8
< Content-Length: 87
< Cache-Control: must-revalidate
<
{"ok":true,"id":"org.couchdb.user:plebian","rev":"1-9c4abdc905ecdc9f0f56921d7de915b9"}
* Connection #0 to host 192.168.33.10 left intact 
```

现在，您可以使用不同的数据库名称尝试前面的相同命令:

```
curl -X PUT http://192.168.33.10:5984/my_awesomedatabase 
```

CouchDB 会对你大喊:

```
{"error":"unauthorized","reason":"You are not a server admin."} 
```

为此，您现在必须在 URL 中提供您的用户名和密码，如下所示:

```
curl -X PUT http://{your_username}:{your_password}@192.168.33.10:5984/my_awesomedatabase 
```

好吧，就这样？嗯，不完全是，因为您所做的唯一事情是限制只能由服务器管理员完成的数据库操作。这包括创建新数据库、删除数据库、管理用户、对所有数据库(包括系统表)的完全管理访问、对所有文档的 CRUD 操作。这使得未经验证的用户仍然能够在任何数据库上进行 CRUD 操作。你可以试着退出 Futon，选择任何你想乱搞的数据库，在里面做 CRUD 的东西。CouchDB 仍然会很乐意为您执行那些操作。

那么，如何修补剩余的漏洞呢？您可以通过创建一个设计文档来做到这一点，该文档将检查试图执行写操作(插入或更新)的用户的用户名是否与允许执行该操作的用户的名称相同。在 Futon 中，使用服务器管理员或数据库管理员帐户登录，选择要使用的数据库，并创建一个新的设计文档。将 ID 设置为`_design/blockAnonymousWrites`，添加一个名为`validate_doc_update`的字段，并将值设置为:

```
function(new_doc, old_doc, userCtx){
  if(userCtx.name != 'kami'){
    throw({forbidden: "Not Authorized"});
  }
} 
```

文档的新版本、现有文档和用户上下文作为参数传递给这个函数。您唯一需要检查的是`userCtx`,它包含数据库的名称、执行操作的用户的名称以及分配给用户的一组角色。

A `secObj`也作为第四个参数被传递，但是您实际上并不需要处理它；所以省略了。基本上，`secObj`描述了在数据库上设置了什么管理特权。

一旦添加了值，保存设计文档，注销，尝试创建一个新文档或更新一个现有文档，然后看 CouchDB 向您抱怨。

![block anonymous writes](img/985eaf73d423b4d6abe382931a2854ce.png)

因为您只是检查用户名，所以您可能认为攻击者可以简单地猜测用户名，并为密码提供任何值，这样就可以了。不完全是，因为 CouchDB 在设计文档被执行之前首先检查用户名和密码是否正确。

或者，如果在一个数据库中有许多用户，也可以检查角色。下面的函数会向任何没有“口袋妖怪 _ 主人”角色的用户抛出一个错误。

```
function(new_doc, old_doc, userCtx) {
  if(userCtx.roles.indexOf('pokemon_master') == -1){
    throw({forbidden: "Not Authorized"});
  }
} 
```

如果您想了解更多关于如何保护 CouchDB 的信息，请务必查看以下资源:

*   [CouchDB 权威指南-安全性](http://guide.couchdb.org/draft/security.html)
*   CouchDB 认证和安全的权威指南
*   [安全功能概述](http://wiki.apache.org/couchdb/Security_Features_Overview)
*   [文档更新验证](https://wiki.apache.org/couchdb/Document_Update_Validation)

## 保护应用程序

最后，让我们更新应用程序，以使用您应用到数据库的安全措施。首先更新`.env`文件:仅用 IP 地址和端口更改`BASE_URI`，然后添加您已经创建的 CouchDB 用户的用户名和密码。

```
BASE_URI="192.168.33.10:5984"
COUCH_USER="plebian"
COUCH_PASS="mypass" 
```

然后，更新`DB`类的构造函数以使用新的细节:

```
public function __construct()
{
  $this->client = new \GuzzleHttp\Client([
    'base_uri' => 'http://' . getenv('COUCH_USER') . ':' . getenv('COUCH_PASS') . '@' . getenv('BASE_URI')
    ]);
} 
```

## 结论

就是这样！在本教程中，您学习了如何使用 CouchDB 创建口袋妖怪产卵位置记录器应用程序。在 GeoCouch 插件的帮助下，您能够执行空间查询，并学习了如何保护 CouchDB 数据库。

你在你的项目中使用 CouchDB 吗？为什么有什么建议/功能可以添加到我们这个小项目中吗？请在评论中告诉我们！

## 分享这篇文章