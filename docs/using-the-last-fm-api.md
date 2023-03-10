# 使用 PHP Last.fm API

> 原文：<https://www.sitepoint.com/using-the-last-fm-api/>

如果你和我一样，边编码边听音乐，你喜欢记录你听过的歌曲，那么你可能听说过 Last.fm。

Last.fm 是一个以音乐推荐引擎为核心的社交网站。它跟踪用户的音乐品味，并为艺术家提供活动、维基和目录。所有的数据都是通过安装在每个用户的音乐播放器(Spotify、Winamp、iTunes 等)上的 Audioscrobbler 数据库插件收集的。).最重要的是，Last.fm 有一个丰富且令人印象深刻的 API，开发者可以用它来构建移动和 web 混搭。

如果你正在为一个乐队或艺术家建立一个网站，并且你想使用 Last.fm 使它更加社会化，那么使用他们的 API 是必须的。通过这种方式，艺术家和粉丝可以相互联系，粉丝可以及时了解音乐会和新专辑。

在本文中，我将向您展示如何查询最后一个 FM API 来获取信息，从而为 [Coldplay](http://en.wikipedia.org/wiki/Coldplay "Coldplay - Wikipedia, the free encyclopedia") 建立一个粉丝网站。我们首先获取乐队的基本信息，然后获取他们最受欢迎的歌曲、他们专辑的内容以及乐队将在哪里演出的活动列表，以便粉丝获得门票。

## 入门指南

为了获得一个 API 帐户，您需要首先拥有一个 Last.fm 用户帐户。使用您的用户帐户登录，然后前往[www.last.fm/api/account](http://www.last.fm/api/account "www.last.fm/api/account")获取一个 API 帐户。当你申请一个 API 账户时，你有 4 种选择:商业使用、商业推广使用、定制使用和非商业使用。指定您的应用程序的名称、描述和网站，您将获得一个 API 密钥和秘密。

与所有主要的 API 服务一样，对于不同的编程语言，包括 PHP，有许多不同的库。我将使用 Felix Bruns 创建并托管在 GitHub 上的 [PHP Last.fm API 库](https://github.com/fxb/php-last.fm-api "fxb/php-last.fm-api")。克隆项目或下载 ZIP 文件，并将其解压缩到您的工作目录中。

## 发出请求

Last.fm API 是基于 REST 的，使用 XML 格式的响应，但是这个细节是由 PHP Last.fm API 库从我们这里抽象出来的。我们可以通过方法与 Last.fm 接口。

Last.fm 艺术家资源返回关于艺术家、艺术家正在表演的事件以及艺术家最受欢迎的曲目的信息。首先，让我们使用库的`Artist::search()`方法搜索一位艺术家。

```
<?php
require __DIR__ . "/src/lastfm.api.php";
require __DIR__ . "/config.php";

// set api key
CallerFactory::getDefaultCaller()->setApiKey(LAST_FM_API_KEY);

// search for the Coldplay band
$artistName = "Coldplay";
$limit = 1;
$results = Artist::search($artistName, $limit);

echo "<ul>";
while ($artist = $results->current()) {
    echo "<li><div>";
    echo "Artist URL: " . $artist->getUrl() . "<br>";
    echo '<img src="' . $artist->getImage(4) . '">';
    echo "</div></li>";

    $artist = $results->next();
}
echo "</ul>";
```

`Artist::getEvents()`返回艺术家正在播放的事件列表。

```
<?php
// search for the Coldplay band events
$artistName= "ColdPlay";
$events = Artist::getEvents($artistName);
if (!empty($events)) {
    echo "<ul>";
    foreach ($events as $key => $evt) {
        echo "<li><div>";
        echo "Event Number: " . ($key + 1) . "<br>";
        echo "Event Name: " . $evt->getTitle() . "<br>";
        echo "Artists: " . implode(", ", $evt->getArtists()) . "<br>";
        echo "Venue: " . $evt->getVenue()->getName() . "<br>";
        echo "Location: " . 
            $evt->getVenue()->getLocation()->getStreet() . " " .
            $evt->getVenue()->getLocation()->getCity() . " " .
            $evt->getVenue()->getLocation()->getCountry() . "<br>";
        echo "Description: " . $evt->getDescription() . "<br>";
        echo "Event URL: " . $evt->getUrl() . "<br>";
        echo "</div></li>";
    }
    echo "</ul>";
}
```

`Artist::getTopTracks()`获取某个艺术家发布的流行歌曲。

```
<?php
// get top tracks for Coldplay
$artistName= "Coldplay";
$tracks = Artist::getTopTracks($artist_name);
echo "<ul>";
foreach($tracks as $key => $track) {
    echo "<li><div>";
    echo "Track Number: " . ($key + 1) . "<br>";
    echo "Title: " . $track->getName() . "<br>";
    echo "Played: " . $track->getPlayCount() . " time(s)<br>";
    echo "Duration: " . $track->getDuration() . " seconds<br>";
    echo "Track URL: " . $track->getUrl() . "<br>";
    echo "</div></li>";
}
echo "</ul>";
```

相册资源返回艺术家的相册信息。检索专辑的完整曲目列表。

```
<?php
// get top tracks for Coldplay
$artistName= "Coldplay";
$albumName = "Mylo Xyloto";
$album = Album::getInfo($artistName, $albumName);
echo "<div>";
echo "Number of Plays: " . $album->getPlayCount() . " time(s)<br>";
echo 'Cover: <img src="' . $album->getImage(4) . '"><br>";
echo "Album URL: " . $album->getUrl() . "<br>";
echo "</div>";
```

## 验证您的混搭

音乐是社会交往的一大部分；我喜欢看我的朋友在听什么，这样我就有可能发现我以前从未听过的艺术家，并成为他们的粉丝。我们可以从 API 中获取用户信息，并在我们自己的混搭中使用它。

有点奇怪的是，Last.fm 没有像其他主要社交网站那样使用 OAuth。相反，它们使用类似的方法，但是使用单个令牌而不是访问令牌，并使用 Oauth 使用的访问令牌秘密。可以在[www.last.fm/api/webauth](http://www.last.fm/api/webauth "www.last.fm/api/webauth")找到流程的描述。但是，这主要是我们的库抽象出来的实现细节。

第一步是请求检索用户信息的权限。一旦 mashup 通过身份验证，Last.fm 将返回一个回调 URL，之后我们可以检索所需的信息。

```
<?php
require __DIR__ . "/src/lastfm.api.php";
require __DIR__ . "/config.php";

CallerFactory::getDefaultCaller()->setApiKey(LAST_FM_API_KEY);

// create the callback url
$callback = "http://" . $_SERVER["HTTP_HOST"] . "/callback.php";
$url = "http://www.last.fm/api/auth/?api_key=" . LAST_FM_API_KEY . "&cb=" . $callback;
echo '<a href="' . $url . '">Authenticate with Last.fm</a>';
```

用户将点击该链接并被带到 Last.fm 认证页面。用户通过身份验证后，她将被重定向回站点，我们将拥有发出请求所需的令牌。

```
<?php
// get last top 10 track listen by user "gafitescu"
$user = "gafitescu";
$tracks = User::getRecentTracks($user, 10);
echo "<ul>";
foreach($tracks as $key => $track) {
    echo "<li><div>";
    echo "Track Number: " . ($key + 1) . "<br>";
    echo "Title: " . $track->getName() . "<br>";
    echo "Artist: " . $track->getArtist() . "<br>";
    echo "Album: " . $track->getAlbum() . "<br>";
    echo "Track URL: " . $track->getUrl() . "<br>";
    echo "</div></li>";
}
echo "</ul>";

// get user's friends
$friends = User::getFriends($user);
echo "<ul>";
foreach ($friends as $friend) {
    echo "<li><div>";
    echo "Friend: " . $friend->getName() . "<br>";
    echo "Country: " . $friend->getCountry() . "<br>";
    echo "Age: " . $friend->getAge() . "<br>";
    echo "</div></li>";
}
echo "</ul>";
```

## 结论

在一个一切都变得越来越互联的世界里，现在开放图形正在成为标准，将粉丝与他们最喜欢的艺术家联系起来非常重要，Last FM 通过其 API 做得很好。开放让你可以增强你的社区，提高你的产品/想法的知名度，最好的方法就是拥有一个公共 API。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章