# 使用 500 像素 API 的热门照片、滤镜和用户配置文件

> 原文：<https://www.sitepoint.com/popular-photos-filters-user-profiles-500px-api/>

[500px](https://500px.com) 是一个发现、分享、买卖灵感摄影的摄影社区。在这篇文章中，我们将探索他们的 API，并建立一个小的展示应用程序。让我们开始吧。

![Logo](img/06a69efe7e85de09e15b470406f75ec3.png)

## 我们正在建造的东西

在本文中，我们将构建一个小型的 Laravel 应用程序，它有三个主要特性。你可以在 Github 上查看最后的[结果。](https://github.com/sitepoint-editors/500pxAPI_Test/tree/v1.0)

*   索引页将包含 500px 网站上最新的热门照片。
*   一个过滤器将让用户从流行的，即将到来的等中进行选择。并根据投票、评级等对结果进行排序。
*   一个摄影师的个人资料页面与可用的照片列表将是可用的。

## 安装

在使用他们的 API 之前，我们需要获得一些访问凭证。登录网站后，您可以进入[应用程序设置](https://500px.com/settings/applications)并注册一个新的测试应用程序。消费者密钥和消费者秘密将用于访问 API。

我们将使用 [Guzzle](https://github.com/guzzle/guzzle) 来发出 HTTP 请求，并使用 [Guzzle Oauth 订户](https://github.com/guzzle/oauth-subscriber)来使用 Oauth。如果你不熟悉这两个，你可以看看这个 [Guzzle 介绍](https://www.sitepoint.com/guzzle-php-http-client/)和阅读关于[使用 Guzzle OAuth](https://www.sitepoint.com/using-guzzle-twitter-via-oauth/) 。

## 热门照片

让我们首先将 Guzzle 包添加到我们的`composer.json`中，并自动加载我们的`src`文件夹，在那里我们放置我们的认证类。运行`composer update`更新我们的应用程序。

```
// composer.json
...
"require": {
      ...
      "guzzle/guzzle": "3.9.*",
      "guzzlehttp/oauth-subscriber": "0.2.*"
},
"autoload": {
	"classmap": [
	    ...
            "app/src"
	]
}
...
```

在我们的`src/PxOAuth.php`文件中，我们将提供我们的`consumer_key`和`consumer_secret`来构建我们的 OAuth 对象。

```
//src/PxOAth.php

class PxOAuth{

    public function __construct($host, $consumer_key, $consumer_secret){
        $this->consumer_key = $consumer_key;
        $this->consumer_secret = $consumer_secret;
        $this->host = $host;

        $oauth = new Oauth1([
            'consumer_key'      => $this->consumer_key,
            'consumer_secret'   => $this->consumer_secret
        ]);

        $this->client = new Client([ 'base_url' => $this->host, 'defaults' => ['auth' => 'oauth']]);
        $this->client->getEmitter()->attach($oauth);
    }

}
```

为了利用 Laravel 的 IoC，我们可以将我们的类绑定到容器上，这样它就可以被自动解析。

```
// bootstrap/start.php

App::singleton('pxoauth', function(){
    $consumer_key = 'TxNYEWxvU26cylAkxTc1KgNmXCPvFc1EazhIk5Po';
    $consumer_secret = 'n88vhgVgpkaCr3I0h1yB1bmkhy72jPzhhzFSbpYI';
    $host = 'https://api.500px.com/v1/';

    $oauth = new PxOAuth($host, $consumer_key, $consumer_secret);

    return $oauth;
});
```

我们将类绑定为单例类，因为我们只需要解析它一次，然后总是返回相同的实例。

我们的索引路由将检索今天的热门照片，并支持许多参数。您可以查看[文档](https://github.com/500px/api-documentation/blob/master/endpoints/photo/GET_photos.md)中的可用选项列表。

```
// app/routes.php
Route::get('/', ['uses' => 'PXController@index']);
```

```
// controllers/PXController.php
public function index(){
    $filters = [
        'feature'           => Input::get('feature', 'popular'),
        'sort'              => Input::get('sort', 'created_at'),
        'sort_direction'    => Input::get('sort_direction', 'desc'),
        'page'              => Input::get('page', 1)
    ];

    $result = $this->loadPhotos($filters);

    return View::make('index', ['photos' => $result['photos'], "inputs" => $filters]);
}

private function loadPhotos($parameters){
    $parameters = array_merge(['image_size' => 3], $parameters);

    $px = App::make('pxoauth');
    $result = $px->get('photos', $parameters)->json();

    return $result;
}
```

![Photos](img/d29c054f47d6b8764331800d819c106a.png)

我们可以按类别过滤照片列表，并根据特定的集合对它们进行排序。您可以查看[文档](https://github.com/500px/api-documentation/blob/master/endpoints/photo/GET_photos.md#parameters)中支持的参数列表。API 返回照片列表和一些其他有用的属性。单个照片项目中的`images`属性包含一组照片，具体取决于请求的大小。您可以从 1 到 4 中选择，或者传入一个大小数组。

```
// views/index.blade.php

@extends('layouts.master')

@section("content")
<div class="row photos">
    <div class="col-lg-12">
        <h1 class="page-header">Popular photos</h1>
    </div>

    <form action="/" method="GET" id="filters">
      // filter options
    </form>

    @include('partials/photos', ['photos' => $photos])

    <button id="load_more" class="btn btn-primary load_more center-block">Load more</button>
</div>

<hr>
@endsection
```

我们包含了一个`photos`部分和分页按钮来说明`page`查询参数的使用。

```
// views/partials/photos.blade.php

@foreach($photos as $photo)
    <div class="col-lg-3 col-md-4 col-xs-6 thumb" data-photo-id="{{ $photo['id'] }}">
        <a class="thumbnail" target="_blank" href="http://500px.com{{ $photo['url'] }}">
            <img class="img-responsive" src="{{ $photo['images'][0]['url'] }}" alt="{{ $photo['name'] }}">
        </a>
        <div class="caption">
            <a class="pull-left" href="/user/{{ $photo['user']['id'] }}">{{ $photo['user']['fullname'] }}</a>
            <a class="pull-right fa fa-heart favorite" href="#"> {{ $photo['favorites_count'] }}</a>
            <a class="pull-right fa fa-thumbs-up vote" href="#"> {{ $photo['votes_count'] }}</a>
        </div>
    </div>
@endforeach
```

![Photos](img/eb5962ced416c6b8b80873546345a545.png)

## 照片分页

`load more`按钮将触发一个 ajax 请求来获取下一个页面并将其附加到当前页面。

```
// views/index.blade.php

@section("scripts")
<script>
    $(function(){
        $('#load_more').click(function(e){
            var page = $(this).data('page') || 2,
                filter_form = $("#filters"),
                feature = filter_form.find("select[name='feature']").val(),
                sort = filter_form.find("select[name='sort']").val(),
                sort_direction = filter_form.find("select[name='sort_direction']").val();

            $(this).text("Loading...");

            $.ajax({
                url: '/ajax/index_more',
                data: {
                    page: page,
                    feature: feature,
                    sort: sort,
                    sort_direction: sort_direction
                },
                type: 'get',
                success: function(data){
                    var photos = $('.photos'),
                        more_photos = $(data);

                    more_photos.insertAfter(photos.find('.thumb:last'));

                    $('#load_more').data('page', page + 1);
                }//success
            }).done(function(){
                $("#load_more").text("Load more");
            })
        });
    });
</script>
@endsection
```

```
// app/routes.php
Route::get('/ajax/index_more', ['uses' => 'PXController@loadMore']);
```

```
// controllers/PXController.php
public function loadMore(){
    $filters = [
        'feature'           => Input::get('feature', 'popular'),
        'sort'              => Input::get('sort', 'created_at'),
        'sort_direction'    => Input::get('sort_direction', 'desc'),
        'page'              => Input::get('page', 1)
    ];
    $result = $this->loadPhotos($filters);

    return View::make('partials/photos', ['photos' => $result['photos']]);
}
```

请求成功后，我们将照片列表传递给 partial，并将结果发送回页面。我们还增加了当前页面指示器。

## 用户概要

每张照片都有一个所有者，所以当用户选择一个摄影师时，我们会根据用户 id 过滤照片。

```
// app/routes.php

Route::get('/user/{id}', ['uses' => 'PXController@photosByUser']);
```

该 API 提供了一个 [users/show](https://github.com/500px/api-documentation/blob/master/endpoints/user/GET_users_show.md) 端点来检索使用用户 id、用户名或电子邮件的特定用户。之后，我们用一些额外的参数查询前面的照片端点。

```
// controllers/PXController.php

public function photosByUser($uid){
    $px = App::make('pxoauth');

    $user = $px->get('users/show', ['id' => $uid])->json();
    $inputs = ['image_size' => 3, 'feature' => 'user', 'user_id' => $uid, 'rpp' => 100];
    $result = $this->loadPhotos($inputs);

    return View::make('user', ['photos' => $result['photos'], 'user' => $user['user']]);
}
```

![User photos](img/437c2eea7f54721a82c67084d7bc8969.png)

该 API 还提供了一组端点来搜索使用某个术语的用户，列出追随者和朋友。您可以查看[文档](https://github.com/500px/api-documentation/tree/master/endpoints/user)中的端点列表。

我们还可以通过使用更多的 API 端点来创建新的功能。例如，当用户选择一张照片时，我们可以显示评论列表([照片/:id/评论](https://github.com/500px/api-documentation/blob/master/endpoints/photo/GET_photos_id_comments.md))，并可能让用户提交一条[新评论](https://github.com/500px/api-documentation/blob/master/endpoints/photo/POST_photos_id_comments.md)。当`nsfw`属性设置为`true`时，我们也可以隐藏成人内容。

## 结论

在本文中，我们介绍了 500px API 的一些基础知识，在下一部分中，我们将构建更多的功能并尝试扩展我们的应用程序。你可以查看 Github 库来测试我们应用程序的当前版本。请在下面的评论中告诉我你的想法。

## 分享这篇文章