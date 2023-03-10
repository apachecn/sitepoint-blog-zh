# 通过 Vimeo 的 API 进行喜欢、观看列表和上传

> 原文：<https://www.sitepoint.com/liking-watchlisting-and-uploading-through-vimeos-api/>

在之前的文章中，我们使用 Vimeo API 用 Silex 和 Twig 构建了一个基本的视频应用程序。我们添加了登录和用户反馈功能，并用视频搜索功能将它们打包。在这个例子中，我们将添加*喜欢*一个视频，将一个视频添加到一个观察列表中以备后用，并通过 Vimeo API 上传视频。

![Vimeo Logo](img/7cd36615c65d4174643b7d45d8ac3e45.png)

## 先决条件

为了加快速度，请浏览前一部分中介绍的代码，并让它在您的开发环境中运行(最好是 [Homestead 改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/))。或者，在这里下载上一篇文章[的最终代码](https://github.com/sitepoint-editors/vimeoapi-part1)。一旦你准备好了，继续下面的内容。

## 与视频互动

在本节中，您将添加“喜欢”功能，并添加视频供以后观看。但是在此之前，您首先需要更新 Vimeo 登录的范围(在上一篇文章中，我们只要求了`public`和`private`范围权限)。将`interact`添加为其中一项，完成后，在浏览器中访问登录页面并再次尝试登录。

```
$scopes = array('public',  'private',  'interact'); $state = substr(str_shuffle(md5(time())),  0,  10); $_SESSION['state']  = $state; $url = $vimeo->buildAuthorizationEndpoint(REDIRECT_URI, $scopes, $state); $page_data = array(  'url'  => $url );
```

它现在应该在权限下显示一个附加项目。勾选此项以允许视频互动。

![vimeo like](img/895096c668308de5c37359326a969c33.png)

打开`templates`目录下的`videos.php`文件，添加用户用来与视频交互的按钮。每个按钮都有`data-uri`属性，存储视频的`uri`。稍后用 JavaScript 提取这个值，并将其传递给服务器。你可以把这个加在视频描述的正下方。

```
<div>  <button  class="like"  data-uri="{{ row.uri }}">like</button>  <button  class="watch-later"  data-uri="{{ row.uri }}">watch later</button>  </div>
```

在页面底部，添加 jQuery 和脚本，您将在其中添加向服务器发出 AJAX 请求的代码。

```
<script  src="http://cdnjs.cloudflare.com/ajax/libs/jquery/2.1.3/jquery.min.js"></script>  <script  src="assets/js/videos.js"></script>
```

在`assets/js`目录中创建`videos.js`文件，并添加以下代码。

```
$('.like').click(function(){  var  self  = $(this);  var uri =  self.data('uri'); $.post(  '/tester/vimeo-slim/video/like',  {  'uri': uri },  function(response){  if(response.status ==  '204'){  self.prop('disabled',  true);  }  }  );  }); $('.watch-later').click(function(){  var  self  = $(this);  var uri =  self.data('uri'); $.post(  '/tester/vimeo-slim/video/watchlater',  {  'uri': uri },  function(response){  if(response.status ==  '204'){  self.prop('disabled',  true);  }  }  );  });
```

它的作用是提取`data-uri`属性的值，然后用这个值向服务器发出一个 AJAX 请求。如果响应是 204，这意味着服务器已经完成了请求，但是没有响应体。您可以禁用该按钮，作为向用户反馈操作已经完成的一种形式。

在`index.php`文件中，添加处理 AJAX 请求的路由。这些路线都是相似的，只有 API 端点不同。首先，它检查请求中是否传递了`uri`，并通过用空字符串替换`/videos`来提取视频 ID。

然后，设置访问令牌并向`/me/likes/{video_id}`端点发出请求。您还需要为数据传入一个空数组，因为请求方法需要一个参数数组作为它的第二个参数。与前面的例子不同，这里明确指定了`PUT`方法，因为请求默认使用`POST`，API 要求请求是一个`PUT`请求。最后，将响应的内容类型设置为`json`，然后返回响应的状态。如果请求成功，将返回“204”。

```
$app->post('/video/like',  function  ()  use  ($app, $vimeo)  {  if($app->request->post('uri')){ $video_id = str_replace('/videos/',  '', $app->request->post('uri')); $vimeo->setToken($_SESSION['user.access_token']); $response = $vimeo->request('/me/likes/'  . $video_id, array(),  'PUT'); $app->contentType('application/json'); echo '{"status": '  . json_encode($response['status'])  .  '}';  }  });
```

标记后面要看的视频的路线也差不多，只是这次你要用`/me/watchlater/{video_id}`作为终点。

```
$app->post('/video/watchlater',  function  ()  use  ($app, $vimeo)  {  if($app->request->post('uri')){ $video_id = str_replace('/videos/',  '', $app->request->post('uri')); $vimeo->setToken($_SESSION['user.access_token']); $response = $vimeo->request('/me/watchlater/'  . $video_id, array(),  'PUT'); $app->contentType('application/json'); echo '{"status": '  . json_encode($response['status'])  .  '}';  }  });
```

## 上传视频

上传视频也可以通过 API 来完成，不过你必须向 Vimeo 发出请求才能解锁这项功能。如果您计划部署一个允许用户上传视频的应用程序，您可以从您的应用程序页面请求上传权限。

![request upload](img/2c3d760ee3fc5683d5da3669d3099654.png)

可能需要 5 个工作日才能收到对您请求的决定。Vimeo 提供了一种生成带有特定作用域的访问令牌的方法，其中之一就是上传作用域。不幸的是，即使生成了具有上传范围的访问令牌，如果上传功能没有得到 Vimeo 的批准，上传也不会起作用。

![generate access token](img/0d8266ad65c36287a8514bc0bca23722.png)

下面是呈现上传视图的代码。

```
$app->get('/upload',  function  ()  use  ($app, $vimeo)  { $page_data = array(); $app->render('upload.php', $page_data);  });
```

这里是上传视图(`templates/upload.php`)。它所做的是检查闪存会话中是否有任何错误。如果是，它们会显示给用户。在此之下，创建一个允许用户上传视频的表单。

```
{%  if flash.errors %}  <div> error uploading file:  {{ flash.errors[0]  }}  </div>  {% endif %}  <form method="POST" enctype="multipart/form-data">  <input type="file" name="video" value=""/>  <button type="submit">upload</button>  </form>
```

在继续之前，安装用于处理视频上传的[上传库](https://github.com/brandonsavage/Upload)。

```
composer require codeguy/upload
```

对于负责处理上传的路由，设置访问令牌。

```
$app->post('/upload',  function  ()  use  ($app, $vimeo)  { $vimeo->setToken($_SESSION['user.token']);  });
```

接下来，设置上传目录。如有必要，请确保创建目录并更改文件权限，以便上传可以正常进行。还要设置保存用户选择的文件的输入字段。

```
$storage =  new \Upload\Storage\FileSystem('uploads'); $file =  new \Upload\File('video', $storage);
```

重命名该文件，这样在代码中引用它就不会有问题。

```
$new_filename = uniqid(); $file->setName($new_filename);
```

添加验证规则，以便只能上传大小不超过 25Mb 的`mp4`文件。然后，将上传文件的代码包装在 try catch 块中。如果出现错误，将错误刷新到会话中。这是之前在`upload.php`视图中被访问的值。

```
$file->addValidations(array(  new \Upload\Validation\Mimetype('video/mp4'),  new \Upload\Validation\Size('25M')  ));  try  { $file->upload();  }  catch  (\Exception $e)  { $errors = $file->getErrors(); $app->flash('errors', $errors);  }
```

最后，构建上传文件的路径，并尝试上传到 Vimeo。您可以通过调用 Vimeo 库的`upload`方法来实现。它接受文件的路径作为它的参数。如果上传文件时出现错误，请将其刷新到会话中。

```
$new_filepath =  'uploads/'  . $new_filename .  '.'  . $file->getExtension();  try  { $vimeo->upload($new_filepath);  }  catch  (\Exception $e)  { $app->flash('errors', array('error uploading to Vimeo'));  } $app->redirect('upload');
```

## 结论

在本文中，您了解了如何使用 Vimeo API 对用户的 Vimeo 帐户执行不同的操作。你已经学会了如何给应用程序不同的权限，与视频互动，最后上传视频。本文使用的所有代码都可以在 [this Github repo](https://github.com/sitepoint-editors/vimeoapi-part22) 中获得。

有问题吗？评论？把它们留在下面！

## 分享这篇文章