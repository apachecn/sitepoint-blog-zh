# 用 Laravel 构建 REST 资源

> 原文：<https://www.sitepoint.com/build-rest-resources-laravel/>

在这一部分，我们将开始使用 REST 接口。在 Laravel 上创建 REST Api 并不困难。我们需要记住的是，我们正在与 EmberJS 打交道，我们不想从头开始编写一个新的适配器。和往常一样，你可以在 [github](https://github.com/AleksanderKoko/sitepoint-RESTAPI-laravel-emberJs-part3) 上找到这部分的源代码。

## 从哪里开始？

这是个很难回答的问题。Ember 有自己的工作流程和逻辑。如果我们开始用这种逻辑编写 REST，我们将节省一些时间，我们将有一个好的架构和一些可重用的东西。我认为 Ember 用他们的 REST 架构做了一个很好的选择。看看 Ember 对数据的预期。

假设我们想要检索一个用户。Ember 预计事情会是这样的:

```
{
      "user": {
        "firstName": "firstName",
        "lastName": "lastName"
      }
    }
```

如果我们想要检索一个用户列表，Ember 将会得到这样一个 json:

```
{
      "users": 
      [
          {
            "firstName": "firstPersonsName",
            "lastName": "lastname"
          },
          {
            "firstName": "secondPersonName",
            "lastName": "lastName"
          }
      ]
    }
```

第一个需要“用户”，第二个需要“用户”。第二个是复数。Ember 也为此制定了一些规则。如果您没有自己指定复数，请使用:

```
Ember.Inflector.inflector.irregular('formula', 'formulae');
```

EmberJs 会做一个假设，要求“公式”。有时候，框架本身提供这样的东西很好，但是另一方面，如果你忘记了这些细节，事情就会失去控制。

在冒险深入了解余烬之前，有一个警告:余烬很难对付，也很强大。花时间了解它是如何工作的。

如果我们把事情复杂化一点，并在对象之间建立一些关系，例如，我们说用户有一些照片。我们该如何输出呢？

```
{
      "user": {
        "id": 1,
        "name": "firstName",
        "lastname": "lastname,
        "photos": [1, 2, 3]
      },

      "photos": 
      [
          {
            "id": 1,
            "title": "Lorem Ipsum"
          },
          {
            "id": 2,
            "title": "Lorem Ipsum"
          }
      ]
    }
```

这是一对多的关系。如果我们请求一个用户，他的照片也会被拉出来。我们已经在 Laravel 建立了一些关系，如果你想的话，你可以使用它们，也可以在 Ember 上使用这些关系。

我从 Ember 开始，看看这个框架如何需要数据。如果你知道如何建立结构，那就更容易了。验证和从数据库中获取数据很容易，但是构建一个可靠的、智能的 REST 接口是困难的。

## 为剩下的做准备

当你开发某样东西时，模型会很有帮助。即使你是一个编程大师，你讨厌处理 Photoshop 或 Gimp，也有很好的原型工具。我用了 balsamiq，我的首页原型是这样的:

![Prototyping with Balsamiq](img/c7a7568fd3f35e28b9a6e3ba28ec3115.png)

让我们开始建造它。打开`/app/views/index.php`。这是我们的单页应用程序。我们在本系列的第一部分中创建了这个文件。

```
<script type="text/x-handlebars">

        <!-- The navigation top-bar -->
        <nav class="top-bar" data-topbar>

            <ul class="title-area">
                <li class="name">
                    <h1><a href="#">Photo Upload</a></h1>
                </li>
            </ul>

            <section class="top-bar-section">

                <!-- Left Nav Section -->
                <ul class="left">
                    <li class="has-dropdown">
                        <a href="#">Categories</a>
                        <ul class="dropdown">
                            <li><a href="#">Category1</a></li>
                            <li><a href="#">Category2</a></li>
                            <li><a href="#">Category3</a></li>
                            <li><a href="#">Category4</a></li>
                        </ul>
                    </li>
                </ul>

            </section>

            <div class="clearfix"></div>

        </nav><!-- END Navigation -->

        <!-- Content -->
        <div style="margin-top: 50px;">
            <!-- The content will be here -->
        </div><!-- END Content -->

    </script>
```

请允许我解释一下。`nav`标签负责导航。带有类`title-area`的`ul`标签是一个链接到应用程序第一层的标志。我还添加了一个带有类别列表的下拉列表。如果你想了解更多，请前往[基础 5 文档](http://foundation.zurb.com/docs/)。大部分时间只是复制/粘贴操作，这部分不用担心。

另外，我在内容区域使用了 Foundation 的网格系统。这将填充所有信息，并会在导航时更改。所有的内部更新将由 Ember 处理。我们将在这里只建立 3 个模板。一个用于用户，一个用于单张照片，一个用于登录页面。

你有没有注意到我们所有的代码都在一个脚本标签里面？Ember 使用把手作为它的模板语言。`text/x-handlebars`的类型是一种特殊的脚本类型。如果你已经使用 Ember 和 Handlebars 有一段时间了，你可能会使用模板名称。我没有在这里指定它们，因为这个模板将被用作所有应用程序的容器。如果不指定名称，Ember 会将其用作应用程序模板。

## 资源控制器

当我开发这个非常简单的应用程序时，我发现资源控制器在开发 REST Apis 时非常方便。这就是 REST 架构的要点——一切都是资源。所有资源都可以应用 HTTP 谓词:GET、POST、DELETE、PUT (update)。并非所有动词都是必需的。

```
php artisan controller:make PhotoController --except=create,edit
```

这就是我们如何通过 Artisan 创建资源控制器。选项`--except`从控制器中省略了这两种方法。我们不需要`create`和`edit`方法。`create`方法处理创建资源的图形界面。因为我们正在制作一个单页应用程序，所以在 Ember 之外创建一个视图是不明智的。

为类别创建另一个资源控制器。如您所见，在这个控制器中只有`show`和`index`方法可用。我认为显示单个类别并检索所有类别就足够了。

```
php artisan controller:make CategoryController --only=show,index
```

另一个控制器是图像控制器。为什么我们已经有一个图像控制器？因为我们需要一个端点来提供图像。Dropbox 保存了我们的图像，但我们无法从外部访问它们。如果你想公开一个文件夹，你必须付费。这是第一个原因。第二个原因是我不希望每张图片都公开。简而言之，这个控制器将从 Dropbox 获取图像，并将其提供给客户端。

```
php artisan controller:make ImagesController --only=show
```

最后但同样重要的是用户控制器:

```
php artisan controller:make UserController --only=show,index
```

## 路线

现在我们已经有了控制器，我们需要将这些控制器与它们相关的路线联系起来。来更新一下`/app/routes.php`。首先，使用`Route::group`将它们分组到一个 url 名称空间中。

```
Route::group(array('prefix' => 'api/v1'), function()
    {

    });
```

这里我们指定了一个前缀，命名空间。该组中的所有内容都可以像这样访问:

```
example.com/api/v1
```

此外，我们可以在该组中指定过滤器。例如，您可以添加一个`Auth::onceBasic('username')`过滤器或创建一个并将其添加到该组中。您也可以使用其他认证。

在该组中添加三个控制器。光控制器、用户控制器和类别控制器。

```
Route::group(array('prefix' => 'api/v1'), function()
    {
        Route::resource('photos', 'PhotoController');
        Route::resource('users', 'UserController');
        Route::resource('categories', 'CategoryController');
    });
```

将 ImagesController 添加到该组之外。我不认为这个控制器需要命名空间——图像就是图像，给它们命名空间没有意义。

```
Route::resource('files', 'ImagesController');
```

最后，`/app/routes.php`文件应该是这样的:

```
Route::get('/', function()
    {
    	return View::make('index');
    });

    Route::group(array('prefix' => 'api/v1'), function()
    {
        Route::resource('photos', 'PhotoController');
        Route::resource('users', 'UserController');
        Route::resource('categories', 'CategoryController');

    });

    Route::resource('files', 'ImagesController');
```

注意，由于 Ember 的要求，资源名称是复数。

## 填充这些控制器

现在我们可以开始建造一些东西了。我不打算在这里涵盖所有关于 REST 的内容，因为很难解释所有的事情——要更深入地了解更多内容，请参见本系列的。先说拍照控制器。

方法应该从数据库中返回最新的照片。在这里，我们可以做一些分页，但我不想事情变得太复杂。如果评论中有足够的兴趣，我们将在以后的文章中更新这个应用程序。

```
public function index()
	{
        try{
            $statusCode = 200;
            $response = [
              'photos'  => []
            ];

            $photos = Photo::all()->take(9);

            foreach($photos as $photo){

                $response['photos'][] = [
                    'id' => $photo->id,
                    'user_id' => $photo->user_id,
                    'url' => $photo->url,
                    'title' => $photo->title,
                    'description' => $photo->description,
                    'category' => $photo->category,
                ];
            }

        }catch (Exception $e){
            $statusCode = 400;
        }finally{
            return Response::json($response, $statusCode);
        }

	}
```

我来解释一下。我把所有东西都插入到一个`try`、`catch`和`finally`块中。如果出了问题，返回一个不同的 json 和一个状态代码。

```
$photos = Photo::all()->take(9);
```

这从数据库中抓取 9 张照片。然后，拍摄每张照片，并将其显示在一个格式化的数组中，稍后该数组将被转换为 json 格式。

如果一切顺利，或者 concertive 没有抛出异常，就会显示正确的输出。如果你想显示一个特定的状态代码，捕捉每一个由 concertive 抛出的异常并显示正确的状态代码。

现在来填充`show()`方法。同样，我们希望检索具有给定 id 的照片的所有信息。

```
public function show($id) 
	{
        try{
            $photo = Photo::find($id);
            $statusCode = 200;
            $response = [ "photo" => [
                'id' => (int) $id,
                'user_id' => (int) $photo->user_id,
                'title' => $photo->title,
                'url' => $photo->url,
                'category' => (int) $photo->category,
                'description' => $photo->description
            ]];

        }catch(Exception $e){
            $response = [
                "error" => "File doesn`t exists"
            ];
            $statusCode = 404;
        }finally{
            return Response::json($response, $statusCode);
        }

	}
```

在构建自己的应用程序时，不要忘记对用户输入进行验证。

用户控制器的逻辑几乎是相同的。这一次，我们将请求用户模型。

```
public function index()
	{
		try{

            $response = [
                'users' => []
            ];
            $statusCode = 200;
            $users = User::all()->take(9);

            foreach($users as $user){

                $response['users'][] = [
                    'id' => $user->id,
                    'username' => $user->username,
                    'lastname' => $user->lastname,
                    'name' => $user->username
                ];

            }

        }catch (Exception $e){
            $statusCode = 404;
        }finally{
            return Response::json($response, $statusCode);
        }
	}
```

一切都几乎相同，只有模型和字段发生了变化。输出 json。`show`方法将如下所示:

```
public function show($id)
	{
	    try{

            $response = [
                'user' => []
            ];
            $statusCode = 200;

            $user = User::find($id);

            $response = [
                'id' => $user->id,
                'name' => $user->name,
                'lastname' => $user->lastname,
                'username' => $user->username
            ];

        }catch (Exception $e){
            $statusCode = 404;
        }finally{
            return Response::json($response, $statusCode);
        }

	}
```

该函数检索具有给定 id 的用户。

我们要处理的最后一个控制器是 ImagesController。逻辑很简单，从文件系统中获取图像并提供给它们。当您使用本地文件系统或服务器文件系统保存和检索文件时，这很简单。遗憾的是，您不能将文件保存到 Heroku，所以我们将使用 Dropbox 并从这个端点提供这些文件。

导入 Dropbox 客户端和 Flysystem 适配器。如果我们的环境是本地的，那么我们将使用带有本地适配器的 flysystem 如果环境是生产环境，那么使用 Dropbox 适配器。将 Flysystem 类分配到该控制器内的私有变量中。

```
if(App::environment() === "local"){

        $this->filesystem = new Filesystem(new Adapter( public_path() .img/'));

    }else{

        $client = new Client(Config::get('dropbox.token'), Config::get('dropbox.appName'));
        $this->filesystem = new Filesystem(new Dropbox($client,img/'));

    }
```

`show`方法将服务于该文件，而`destroy`方法将从文件系统中删除该文件。通过使用这个库，我们在应用程序中加入了一个抽象层次。

```
public function show($name)
	{
        try{
            $file = $this->filesystem->read($name); // Read the file
        }catch (Exception $e){
            return Response::json("{}", 404);       // Return a 404 status code in a error case
        }

        $response = Response::make($file, 200);     // If everything goes ok then return that file and 200 status code

        return $response;

	}
```

`destroy()`功能非常简单。只需使用 delete 方法选择该文件，并传递要删除的文件名。如果找不到文件，则返回 404。

```
public function destroy($name)
	{
		try{
            $this->filesystem->delete($name);
            return Response::json("{}", 200);
        }catch (\Dropbox\Exception $e){
            return Response::json("{}", 404);
        }
	}
```

最后，ImageController 应该看起来像这样:

```
/* /app/controllers/ImagesController.php */

    use Dropbox\Client;
    use League\Flysystem\Filesystem;
    use League\Flysystem\Adapter\Local as Adapter;
    use League\Flysystem\Adapter\Dropbox;

    class ImagesController extends \BaseController {

        private $filesystem;

        public function __construct(){

            if(App::environment() === "local"){

                $this->filesystem = new Filesystem(new Adapter( public_path() .img/'));

            }else{

                $client = new Client(Config::get('dropbox.token'), Config::get('dropbox.appName'));
                $this->filesystem = new Filesystem(new Dropbox($client,img/'));

            }

        }

    	public function show($name)
    	{
            try{
                $file = $this->filesystem->read($name);
            }catch (Exception $e){
                return Response::json("{}", 404);
            }

            $response = Response::make($file, 200);

            return $response;

    	}

    	public function destroy($name)
    	{
    		try{
                $this->filesystem->delete($name);
                return Response::json("{}", 200);
            }catch (\Dropbox\Exception $e){
                return Response::json("{}", 404);
            }
    	}

    }
```

我们提供的格式是 HTML。好吧，这有点奇怪。我们想提供图像，而不是 HTML。但是，这不是问题，因为浏览器会查找文件格式并识别如何使用该文件。

继续并尝试创建 CategoryController。我把它留出来给你做练习。

## 测试 Api

我不得不承认，我爱上了 PhpStorm，为了测试 Rest APIs，我使用了一个叫做 Rest Client 的工具。这是一个简化测试的图形界面。如果您愿意，也可以在终端使用 CURL。让我们做一些测试:

```
curl http://localhost:8000/api/v1/users
```

这是返回的结果:

![Using Curl for testing Rest Apis](img/d65f97254054f89de8982cc6cdfab810.png)

使用 PhpStorm 的 Rest 客户端，我在 json 中得到相同的结果。

![Testing Rest Apis with Rest Client of PhpStorm](img/c3b70ec688843d0b3004ceb23e456e7b.png)

如果我想以更好的格式查看结果，我可以简单地按下工具左侧的 **js** 图标，Rest 客户端会给我一个更好的表示。

![The Json file from the Rest Client of PhpStorm](img/99414c314e38fb895845c6e78f059ec4.png)

您还可以测试其他动词，如 delete 和 post。尽可能多地进行测试。您还可以使用其他客户端进行测试:Rest Console 和 Postman 就是其中的两个。第一个只在 Chrome 上可用，第二个是 Postman，在 Chrome 和 Firefox 上都可用。Postman 看起来更简单，更人性化。去吧，给他们一个尝试。

## 结论

Laravel 简化了我们用资源控制器构建 REST Apis 的工作。我们看到了应该如何使用 Ember 约定来构建接口。Ember 选择了一个好的接口，通过坚持这个逻辑，你可以很容易地在其他平台上重用你的代码。

在这一部分，我更多地关注概念，没有做太多的编码。填充所有的方法和添加验证会不必要地延长这篇文章，因为它已经够长了，是一个很长的系列。开发时，你应该**总是**验证输入。别忘了，测试，测试，测试。测试应该是你最好的朋友。

在本系列的最后一部分中，我们将把它们组合成一个功能完整的实时应用程序。

## 分享这篇文章