# 用口才，Faker 和 Flysystem 建立一个数据库

> 原文：<https://www.sitepoint.com/build-database-eloquent-faker-flysystem/>

在这一部分，我们将创建数据库的结构。我们将使用迁移创建表，并使用 seeders 播种数据库。此外，您将学习如何从 [LoremPixel](http://lorempixel.com) 中抓取一些随机图像，并使用 [Flysystem](https://www.sitepoint.com/abstract-file-systems-flysystem/) 将它们放在文件系统中。您还将使用 [Faker](https://www.sitepoint.com/simplifying-test-data-generation-with-faker/) 库添加一些随机生成的数据。
与第 1 部分非常相似，您可以从 [github](https://github.com/sitepoint-examples/sitepoint-RESTAPI-laravel-emberJs-part2) 下载这一部分的代码。

## 安装库

使用 [Composer](https://www.sitepoint.com/re-introducing-composer/) 安装非常简单。

需要这些项目:

```
"fzaninotto/faker": "1.5.*@dev",
    "league/flysystem": "dev-feature/eventable-filesystem",
    "dropbox/dropbox-sdk": "dev-master"
```

执行`composer update`，一切都将顺利安装和更新。

## 构建数据库的结构

到目前为止，我们只有一个迁移文件。我们的申请还需要两个。一个表保存照片数据，另一个表保存类别数据。想了解更多关于工匠和移民的信息，请看[这篇文章](https://www.sitepoint.com/laravel-migrations/)。

```
php artisan migrate:make create_photos_table
    php artisan migrate:make create_categories_table
```

这两个命令创建两个迁移文件。让我们开始用一些模式填充它们。首先，我们从照片迁移文件开始。停下来想一想我们需要什么。之前，我们安装了 flysystem 库。

flysystem 库可以处理不同位置的文件:本地文件系统、Dropbox、AWS3、Rackspace 云文件等等。我们需要它作为我们开发环境中的本地文件系统，并且我们必须使用 Dropbox 进行生产。这是为什么呢？Heroku 是一个 Paas(平台即服务),所以我们不能完全控制文件系统。只有两个目录可以写:`./temp`和`./logs`。您只能使用 git 为部署编写文件，而不能在任何其他文件夹中编写文件。为什么我们不用那两个目录呢？因为它们是暂时的。如果出现问题，Dyno 重新启动，这些目录中的所有文件都会被删除。使用这两个目录是不安全的。

如果你想了解更多关于 flysystem 的信息，请阅读 sitepoint 上的这篇文章，作者是[卢卡斯·怀特](https://www.sitepoint.com/author/lwhite/)或[他们的文档](https://github.com/thephpleague/flysystem)。

## Dropbox 应用程序

在将 flysystem 和 Dropbox 用于生产之前，我们必须创建一个允许我们使用它们的 Api 的 Dropbox 应用程序。然后，我们需要做一些配置。如果您还没有 Dropbox 帐户，请创建一个，然后返回阅读本文的其余部分。如果您使用 Rackspace 或 AWS3，那么您可以自由使用其中任何一个。我在这里使用 Dropbox 是因为它不需要信用卡，而且免费使用。跟随[链接](https://www.dropbox.com/developers/apps)创建一个 Dropbox 应用。下面是如何创建一个。

![Create a DropBox App](img/74aee372493e1364c7de48bf1c079060.png)

接下来是配置。我们将只在生产中使用它。将配置从应用程序逻辑中分离出来是一个好主意。在`/app/config`或`/app/config/production`中创建`dropbox.php`。

```
return array(
        'token'     =>  'token_here',
        'appName'   =>  'Your App Name'
    );
```

对于检索使用:

```
Config::get('dropbox.appName');
    Config::get('dropbox.token');
```

我们需要 flysystem 来创建 images 文件夹(保存`images/photos`的文件夹)并删除它。在`up`方法中，我们将创建文件夹，在`down`方法中，我们将删除它。在`__construct`中，我们通过传入一个本地适配器(或者 Dropbox、Rackspace……)来创建一个文件系统对象。在本地适配器中，我们指定了一个路径。

请注意，在迁移中创建文件夹并删除它是一种反模式。没什么关键的，但是打破了单一责任原则。不要担心，这段代码只是用于演示目的，向您展示库是如何工作的——只要记住这一点，下次尝试在控制器中使用它。

`public_path()`函数是一个 Laravel 辅助函数，它返回`public`文件夹的完整路径。

要定义模式，只需指定想要使用的字段类型，并将字段名作为参数。有关模式的更多信息，请阅读 [Laravel 文档](http://laravel.com/docs/schema)。

```
/* /app/database/migrations/some_date_CreatePhotosTable.php */

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Database\Migrations\Migration;
    use Dropbox\Client;
    use League\Flysystem\Filesystem;
    use League\Flysystem\Adapter\Local as Adapter;
    use League\Flysystem\Adapter\Dropbox;

    class CreatePhotosTable extends Migration {

        private $filesystem;

        public function __construct(){

            if(App::environment() === "local"){     // If its on local use the local filesystem

                $this->filesystem = new Filesystem(new Adapter( public_path() ));

            }else{                                  // Use dropbox on other cases, 
                                                    // including testing here (not a good idea)

                $client = new Client(Config::get('dropbox.token'), Config::get('dropbox.appName'));
                $this->filesystem = new Filesystem(new Dropbox($client));

            }

        }

    	public function up()
    	{

            $this->filesystem->createDir('images');

            Schema::create('photos', function($table)
            {
                $table->increments('id');
                $table->integer('user_id');
                $table->string('title');
                $table->string('url')->unique();
                $table->text('description');
                $table->integer("category");
                $table->timestamps();
            });
    	}

    	public function down()
    	{

            Schema::dropIfExists('photos');

            try{
                $this->filesystem->deleteDir('images');
            }catch (\Dropbox\Exception_BadResponse $e){}

    	}

    }
```

对于`up`方法，我们为照片创建了一个`id`、`user_id`、`title`、`url`、`category`和`description`。我们还用了和其他不一样的东西。`$table->timestamps()`是一个特殊的函数，自动增加两个字段:`created_at`和`updated_at`。

在`down`方法中，删除表格(如果它存在)并删除我们在`up`方法中创建的文件夹。我们将使用 seeders 在那里设置一些图像，所以删除所有照片并用新照片播种是个好主意。

这个种子文件在生产中也可以工作。为了开发，我们使用了本地文件系统，为了生产，我们使用了 Dropbox 应用程序。将所有更改上传到 Heroku，并进行迁移+播种，您会看到在您的 Dropbox 应用程序中创建了一个文件夹。

创建照片迁移文件后，我们需要类别迁移文件。这样更容易。在该模式中，我们创建一个 id，每当有新记录添加到表中时，该 id 就会递增。我们还需要一个类别的名称。通过为类别指定名称和 id，我们可以让事情变得更加动态。在 down 方法中，删除该表(如果它存在)。

```
/* /app/database/migrations/some_date_CreateCategoriesTable.php */

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Database\Migrations\Migration;

    class CreateCategoriesTable extends Migration {

    	public function up()
    	{
            Schema::create('categories', function($table)
            {
                $table->increments('id');
                $table->string('name');
                $table->timestamps();
            });
    	}

    	public function down()
    	{
            Schema::dropIfExists('categories');
    	}

    }
```

创建这两个文件后，让我们通过执行以下命令来应用迁移:

```
php artisan migrate:refresh
    php artisan db:seed
```

或者

```
php artisan migrate:refresh --seed
```

这将恢复旧的迁移(如果您已经应用了一个迁移)并构建新的模式。之后，播种者将被处死。

如果您尚未进行迁移，则会显示一个错误。使用`php artisan migrate`之后一切都会正常工作。

## 创建模型

在进一步使用 seeders 之前，我们最好先创建模型。当创建模型、迁移和种子时，您必须知道您在做什么。想清楚自己到底想要什么。模型之间的关系很重要，可以节省时间。

Laravel 的模型扩展了雄辩的 ORM 类。雄辩的一个很好的特点是关系。因为我们在本系列中处理 REST Api，所以这个特性在端点上帮助我们放置与给定资源相关的数据。EmberJs 在前端有一个关于关系的类似逻辑，这自动化了大部分代码。这种奇偶校验使得代码更易于维护和扩展。

对于用户模型，就不用担心了。它是由 Laravel 创建的，可以随时使用。编辑该模型，并添加一些与我们将要创建其他模型的关系。模型位于`/app/models`。

```
/* /app/models/User.php */

    use Illuminate\Auth\UserTrait;
    use Illuminate\Auth\UserInterface;

    class User extends Eloquent implements UserInterface{

    	use UserTrait;

    	protected $table = 'users';     // The table that this model uses

        public function photos()
        {
            return $this->hasMany('Photo');
        }

    }
```

`photos()`方法指定了一个现在不存在的与照片模型的关系。此外，受保护的变量`$table`用于指定该模型正在使用的表名。作为一个额外的特性，这个模型实现了`UserInterface`,以便更容易地与身份验证集成。

让我们创建照片模型。在`/app/models`中创建一个新文件，命名为`Photo.php`。请记住，类名应该以大写字母开头，并扩展到雄辩。

```
/* /app/models/Photo.php */

    class Photo extends Eloquent {

        protected $table = 'photos';

        public function author(){
            return $this->belongsTo('User', 'user_id');
        }

    }
```

在这种情况下，不必指定`$table`变量。如果一个`$table`变量不存在，Laravel 使用小写名称的复数作为表名。我们在这里指定了另一个关系:`belongsTo`关系。第一个参数用于与之有关系的模型。第二个是用于该模型的字段，用于指定哪个用户拥有这张照片。它可以这样使用:

```
Photo::find($id)->author;
```

这将返回一个 id 与照片模型上的`user_id`相同的用户对象。

对于类别，在`/app/models`中创建另一个文件，并将其命名为`Category.php`。

```
class Category extends Eloquent {

        protected $table = 'categories';

    }
```

## 在表格中加入一些数据

现在我们已经有了数据库的结构，是时候用一些数据来播种它了。这就是 Faker 库的用武之地。有关 Faker 的更多信息，请阅读 [Faker 文档](https://github.com/fzaninotto/Faker)或者更多高级特性和用例，请阅读[使用 Faker 简化测试数据生成](https://www.sitepoint.com/simplifying-test-data-generation-with-faker/)。

目前，我们只有一个种子文件。我们来编辑一下，用 Faker 库生成一个假的用户名和密码。编辑`/app/database/seeds`文件夹中的`UserTableSeeder.php`文件。

我们需要知道一个用户进行身份验证。数据库中的一个用户应该创建没有骗子。

初始化 Faker 对象:

```
$faker = Faker\Factory::create();   // Faker uses Factory design pattern
```

如何创建用户名和密码:

```
$faker->userName;                           // returns a username
    Hash::make($faker->name . $faker->year);    // Creates a password
```

最后，UserTableSeeder 应该看起来像这样:

```
/* /app/database/seeds/UserTableSeeder.php */

    class UserTableSeeder extends Seeder {

        public function run()
        {

            Eloquent::unguard();

            DB::table('users')->delete();

            $faker = Faker\Factory::create();

            for($i = 0; $i < 2; $i++){
                User::create(array(
                    'username' => $faker->userName,
                    'password' => Hash::make($faker->name . $faker->year),
                    'name' => $faker->name,
                    'lastname' => $faker->lastName
                ));
            }

            User::create(array(
                'username' => 'foo',
                'password' => Hash::make('password'),
                'name' => $faker->name,
                'lastname' => $faker->lastName
            ));

        }

    }
```

通过执行这个 seeder 类，我们创建了两个假用户和一个硬编码用户。稍后，我们将通过使用基本身份验证来测试此应用程序，因此我们需要有一个已知的用户。

剩下的就是类别和照片种子文件。在`/app/database/seeds`内部创建`PhotoTableSeeder.php`和`CategoryTableSeeder.php`。

```
/* /app/database/seeds/CategoryTableSeeder.php */

    class CategoryTableSeeder extends Seeder {

        public function run()
        {

            Eloquent::unguard();

            DB::table('categories')->delete();

            for($i = 1; $i < 5; $i++){
                Category::create(array(
                    'name' => 'category' . $i
                ));
            }

        }

    }
```

对于 categories 种子文件，我们甚至不需要 Faker。仅仅用`categorynumber`作为类别的名称就可以了。每次插入新类别时，id 都会自动增加。

照片种子类有点棘手。在这个迁移文件中，我们需要从 [LoremPixel](http://lorempixel.com) 获取一些图像，并将它们放入`/public/images`文件夹中。此外，我们需要带有本地适配器或 Dropbox 适配器的 flysystem 库。

这就是我们如何创建一个随机字符串。参数的长度是随机字符串的长度。这将是图像的名称。

```
$faker->lexify($string = '???????????????????');
```

从 lorempixel 获取图像很简单:宽度和高度是 URL 片段。这样的链接然后返回给定大小的图像。通过多次请求此 url，将返回不同的图像，因此不会出现重复。获取该 url 的内容。在这种情况下，url 应该是`https://lorempixel.com/400/200/`，它是一个图像

```
file_get_contents('https://lorempixel.com/640/400/');
```

Faker 的句子方法返回指定字数的句子作为参数

```
$faker->sentence(40);
```

最后，`PhotoTableSeeder.php`应该是这样的。

```
/* /app/database/seeds/PhotoTableSeeder.php */

    use Dropbox\Client;                     // DropBox Client
    use League\Flysystem\Filesystem;
    use League\Flysystem\Adapter\Local as Adapter;
    use League\Flysystem\Adapter\Dropbox;   // The DropBox Adapter

    class PhotoTableSeeder extends Seeder {

        private $filesystem;

        public function __construct(){
            if(App::environment() === "local"){
                $this->filesystem = new Filesystem(new Adapter( public_path() .img/'));
            }else{
                $client = new Client(Config::get('dropbox.token'), Config::get('dropbox.appName'));
                $this->filesystem = new Filesystem(new Dropbox($client, '/images'));
            }

        }

        public function run()
        {

            Eloquent::unguard();

            DB::table('photos')->delete();

            $faker = Faker\Factory::create();

            for($i = 0; $i < 10; $i++){

                $file = file_get_contents('https://lorempixel.com/640/400/');

                $url = $faker->lexify($string = '???????????????????');
                try{
                    $this->filesystem->write($url, $file);
                }catch (\Dropbox\Exception $e){
                    echo $e->getMessage();
                }

                Photo::create(array(
                    'url' => $url,
                    'user_id' => (int) rand(1, 4),
                    'title' => $faker->sentence(5),
                    'description' => $faker->sentence(40),
                    'category' => rand(1, 4)
                ));
            }

        }

    }
```

不要忘记将这些迁移文件调入`DatabaseSeeder.php`。将这些行添加到`/app/database/seeds/DatabaseSeeder.php`:

```
$this->call('PhotoTableSeeder');
    $this->call('CategoryTableSeeder');
```

这样，DatabaseSeeder 将调用两个新的种子文件。让我们在 Heroku 上应用迁移和播种。先推项目

```
git add .
    git commit -m "a message here"
    git push heroku master
```

然后运行迁移和种子

```
heroku run php artisan migrate:refresh --seed
```

在服务器上执行迁移和播种后，这是我的 dropbox apps 文件夹上的截屏。

![Dropbox apps folder](img/e257b98a81e9c02c6b9629ae5372dc29.png)

## 结论

在这一部分中，我们看到了如何使用迁移创建数据库，以及如何使用带有一些虚假数据的种子来播种数据库。我们使用 Faker 库和 Flysystem 来处理文件系统。雄辩是一个易于使用的 ORM，但是非常强大——理解雄辩是如何简化你的工作流程的。不要忘记，一切都是从数据库架构开始的，在开始时定义一个智能的架构会让你在以后省去很多麻烦。请继续关注第 3 部分！

## 分享这篇文章