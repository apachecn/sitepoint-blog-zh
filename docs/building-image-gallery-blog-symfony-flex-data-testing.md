# 用 Symfony Flex 构建图片库博客:数据测试

> 原文：<https://www.sitepoint.com/building-image-gallery-blog-symfony-flex-data-testing/>

本文是构建一个示例应用程序——一个多图片画廊博客——的系列文章的一部分，用于性能基准测试和优化。(点击此处查看[回购](https://github.com/sitepoint-editors/multi-image-gallery-blog/)。)

* * *

在[上一篇文章](https://www.sitepoint.com/building-image-gallery-blog-symfony-flex-setup/)中，我们展示了如何使用 Flex 从头开始建立一个 Symfony 项目，以及如何创建一组简单的设备并启动和运行项目。

我们旅程的下一步是用一定数量的真实数据填充数据库，以测试应用程序的性能。

*注意:如果你在上一篇文章中做了“[开始使用应用程序](https://www.sitepoint.com/building-image-gallery-blog-symfony-flex-setup/#gettingstartedwiththeapp)”的步骤，你就已经遵循了这篇文章中概述的步骤。如果是这样的话，用这篇文章来解释这是怎么做到的。*

作为奖励，我们将演示如何用基本的[冒烟测试](https://en.wikipedia.org/wiki/Smoke_testing_(software))建立一个简单的 PHPUnit 测试套件。

## 更多虚假数据

一旦你的实体被抛光，你就有了“就是这样！我完了！”现在是创建更重要的数据集的最佳时机，该数据集可用于进一步测试和准备应用程序的生产。

像我们在上一篇文章中创建的简单 fixtures 非常适合开发阶段，在开发阶段加载大约 30 个实体很快，并且在更改 DB 模式时经常可以重复。

测试应用程序性能、模拟真实世界流量和检测瓶颈需要更大的数据集(即，该项目需要更大量的数据库条目和图像文件)。生成数千个条目需要一些时间(和计算机资源)，所以我们只想做一次。

我们可以尝试增加 fixture 类中的`COUNT`常量，看看会发生什么:

```
// src/DataFixtures/ORM/LoadUsersData.php
class LoadUsersData extends AbstractFixture implements ContainerAwareInterface, OrderedFixtureInterface
{
    const COUNT = 500;
    ...
}

// src/DataFixtures/ORM/LoadGalleriesData.php
class LoadGalleriesData extends AbstractFixture implements ContainerAwareInterface, OrderedFixtureInterface
{
    const COUNT = 1000;
    ...
} 
```

现在，如果我们运行 [bin/refreshDb.sh](https://github.com/sitepoint-editors/multi-image-gallery-blog/blob/master/bin/refreshDb.sh) ，过一段时间后我们可能会得到一个不太好的消息，比如`PHP Fatal error: Allowed memory size of N bytes exhausted`。

除了执行缓慢之外，每个错误都将导致一个空数据库，因为 EntityManager 只在 fixture 类的最后刷新。此外，Faker 会为每个图库条目下载一张随机图片。对于 1，000 个画廊，每个画廊有 5 到 10 张图片，这将是 5，000 到 10，000 次下载，这真的很慢。

有关于优化批处理的[原则](http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/reference/batch-processing.html)和 [Symfony](https://groups.google.com/forum/#!topic/symfony-devs/0Ez-TpsC3I0) 的优秀资源，我们将使用其中一些技巧来优化夹具加载。

首先，我们将定义 100 个画廊的批量大小。在每个批处理之后，我们将刷新并清除`EntityManager`(即，分离持久化的实体)并告诉垃圾收集器去做它的工作。

为了跟踪进度，让我们打印出一些元信息(批处理标识符和内存使用)。

**注意:**在调用`$manager->clear()`之后，所有持久化的实体现在都没有被管理。实体管理器不再知道它们，您可能会得到一个“实体未持久化”的错误。

关键是将实体合并回管理器`$entity = $manager->merge($entity);`

如果没有优化，运行`LoadGalleriesData` fixture 类时内存使用会增加:

```
> loading [200] App\DataFixtures\ORM\LoadGalleriesData
100 Memory usage (currently) 24MB / (max) 24MB
200 Memory usage (currently) 26MB / (max) 26MB
300 Memory usage (currently) 28MB / (max) 28MB
400 Memory usage (currently) 30MB / (max) 30MB
500 Memory usage (currently) 32MB / (max) 32MB
600 Memory usage (currently) 34MB / (max) 34MB
700 Memory usage (currently) 36MB / (max) 36MB
800 Memory usage (currently) 38MB / (max) 38MB
900 Memory usage (currently) 40MB / (max) 40MB
1000 Memory usage (currently) 42MB / (max) 42MB 
```

内存使用量从 24 MB 开始，每批增加 2mb(100 个图库)。如果我们试图加载 100，000 个图库，我们将需要 24 MB + 999 (999 批 100 个图库，99，900 个图库)* 2 MB = **~2 GB 内存**。

在为每个批处理添加了`$manager->flush()`和`gc_collect_cycles()`之后，使用`$manager->getConnection()->getConfiguration()->setSQLLogger(null)`移除 SQL 日志记录，并通过注释掉`$this->addReference('gallery' . $i, $gallery);`移除实体引用，内存使用对于每个批处理都变得有些恒定。

```
// Define batch size outside of the for loop
$batchSize = 100;

...

for ($i = 1; $i <= self::COUNT; $i++) {
    ...

    // Save the batch at the end of the for loop
    if (($i % $batchSize) == 0 || $i == self::COUNT) {
        $currentMemoryUsage = round(memory_get_usage(true) / 1024);
        $maxMemoryUsage = round(memory_get_peak_usage(true) / 1024);
        echo sprintf("%s Memory usage (currently) %dKB/ (max) %dKB \n", $i, $currentMemoryUsage, $maxMemoryUsage);

        $manager->flush();
        $manager->clear();

        // here you should merge entities you're re-using with the $manager
        // because they aren't managed anymore after calling $manager->clear();
        // e.g. if you've already loaded category or tag entities
        // $category = $manager->merge($category);

        gc_collect_cycles();
    }
} 
```

正如预期的那样，内存使用现在稳定了:

```
> loading [200] App\DataFixtures\ORM\LoadGalleriesData
100 Memory usage (currently) 24MB / (max) 24MB
200 Memory usage (currently) 26MB / (max) 28MB
300 Memory usage (currently) 26MB / (max) 28MB
400 Memory usage (currently) 26MB / (max) 28MB
500 Memory usage (currently) 26MB / (max) 28MB
600 Memory usage (currently) 26MB / (max) 28MB
700 Memory usage (currently) 26MB / (max) 28MB
800 Memory usage (currently) 26MB / (max) 28MB
900 Memory usage (currently) 26MB / (max) 28MB
1000 Memory usage (currently) 26MB / (max) 28MB 
```

而不是每次都下载随机图像，我们可以准备 15 个随机图像，更新 fixture 脚本随机选择其中一个，而不是使用 Faker 的`$faker->image()`方法。

让我们从 [Unsplash](https://unsplash.com) 中取出 15 张图像，并保存在`var/demo-data/sample-images`中。

然后，更新`LoadGalleriesData::generateRandomImage`方法:

```
private function generateRandomImage($imageName)
    {
        $images = [
            'image1.jpeg',
            'image10.jpeg',
            'image11.jpeg',
            'image12.jpg',
            'image13.jpeg',
            'image14.jpeg',
            'image15.jpeg',
            'image2.jpeg',
            'image3.jpeg',
            'image4.jpeg',
            'image5.jpeg',
            'image6.jpeg',
            'image7.jpeg',
            'image8.jpeg',
            'image9.jpeg',
        ];

        $sourceDirectory = $this->container->getParameter('kernel.project_dir') . '/var/demo-data/sample-images/';
        $targetDirectory = $this->container->getParameter('kernel.project_dir') . '/var/uploads/';

        $randomImage = $images[rand(0, count($images) - 1)];
        $randomImageSourceFilePath = $sourceDirectory . $randomImage;
        $randomImageExtension = explode('.', $randomImage)[1];
        $targetImageFilename = sha1(microtime() . rand()) . '.' . $randomImageExtension;
        copy($randomImageSourceFilePath, $targetDirectory . $targetImageFilename);

        $image = new Image(
            Uuid::getFactory()->uuid4(),
            $randomImage,
            $targetImageFilename
        );

        return $image;
    } 
```

在重载 fixtures 时，删除`var/uploads`中的旧文件是个好主意，所以我在删除 DB 模式后，立即向`bin/refreshDb.sh`脚本添加了`rm var/uploads/*`命令。

加载 500 个用户和 1000 个图库现在需要大约 7 分钟和大约 28 MB 内存(峰值使用)。

```
Dropping database schema...
Database schema dropped successfully!
ATTENTION: This operation should not be executed in a production environment.

Creating database schema...
Database schema created successfully!
  > purging database
  > loading [100] App\DataFixtures\ORM\LoadUsersData
300 Memory usage (currently) 10MB / (max) 10MB
500 Memory usage (currently) 12MB / (max) 12MB
  > loading [200] App\DataFixtures\ORM\LoadGalleriesData
100 Memory usage (currently) 24MB / (max) 26MB
200 Memory usage (currently) 26MB / (max) 28MB
300 Memory usage (currently) 26MB / (max) 28MB
400 Memory usage (currently) 26MB / (max) 28MB
500 Memory usage (currently) 26MB / (max) 28MB
600 Memory usage (currently) 26MB / (max) 28MB
700 Memory usage (currently) 26MB / (max) 28MB
800 Memory usage (currently) 26MB / (max) 28MB
900 Memory usage (currently) 26MB / (max) 28MB
1000 Memory usage (currently) 26MB / (max) 28MB 
```

看看夹具类来源:[LoadUsersData.php](https://github.com/sitepoint-editors/multi-image-gallery-blog/blob/master/src/DataFixtures/ORM/LoadUsersData.php)和[LoadGalleriesData.php](https://github.com/sitepoint-editors/multi-image-gallery-blog/blob/master/src/DataFixtures/ORM/LoadGalleriesData.php)。

## 表演

在[这个点](https://github.com/sitepoint-editors/multi-image-gallery-blog/releases/tag/1.0)，主页渲染非常慢——对于生产来说太慢了。

用户可以感觉到应用程序正在努力交付页面，可能是因为应用程序正在渲染所有的图库，而不是有限的几个。

我们可以更新应用程序，只立即渲染前 12 个图库，并引入延迟加载，而不是一次渲染所有图库。当用户滚动到屏幕的末尾时，该应用程序将获取接下来的 12 个图库，并将它们呈现给用户。

### 性能测试

为了跟踪性能优化，我们需要建立一组固定的测试，用于测试和比较性能改进。

我们将使用围城进行负载测试。在这里你可以找到更多关于[围攻和性能测试](https://www.sitepoint.com/web-app-performance-testing-siege-plan-test-learn/)的信息。我们可以利用[Docker](https://www.docker.com/)——一个强大的容器平台，而不是在我的机器上安装围攻。

简单来说，Docker 容器类似于虚拟机([但它们不是同一个东西](https://www.docker.com/what-container#comparing))。除了构建和部署应用程序之外，Docker 还可以用来试验应用程序，而不需要在本地机器上安装它们。你可以创建自己的图片或者使用 [Docker Hub](https://hub.docker.com/) 上的图片，这是一个 Docker 图片的公共注册中心。

当您想要试验同一软件的不同版本时(例如，PHP 的不同版本)，这尤其有用。

我们将使用[横河/围攻](https://hub.docker.com/r/)图像来测试应用程序。

#### 测试主页

测试主页并不简单，因为只有当用户滚动到页面末尾时才会执行 Ajax 请求。

我们可以期望所有用户都登陆主页(即 100%)。我们还可以估计，他们中的 50%会向下滚动到最后，因此要求画廊的第二页。我们还可以猜测，其中 30%的人会加载第三页，15%的人会请求第四页，5%的人会请求第五页。

这些数字是基于预测的，如果我们能够使用分析工具来获得用户行为的实际洞察力，那会好得多。但这对于一个全新的应用来说是不可能的。不过，偶尔看看分析数据，并在初始部署后调整测试套件是个好主意。

我们将通过两个并行运行的测试来测试主页(和惰性加载 URL)。第一个将只测试主页 URL，而另一个将测试延迟加载端点 URL。

文件`lazy-load-urls.txt`包含一个随机列表，以预测的比率显示延迟加载的页面 URL:

*   第二页 10 个 URL(50%)
*   第三页的 6 个 URL(30%)
*   第四页的 3 个 URL(15%)
*   1 第五页的 URLs)

```
http://blog.app/galleries-lazy-load?page=2
http://blog.app/galleries-lazy-load?page=2
http://blog.app/galleries-lazy-load?page=2
http://blog.app/galleries-lazy-load?page=4
http://blog.app/galleries-lazy-load?page=2
http://blog.app/galleries-lazy-load?page=2
http://blog.app/galleries-lazy-load?page=3
http://blog.app/galleries-lazy-load?page=2
http://blog.app/galleries-lazy-load?page=2
http://blog.app/galleries-lazy-load?page=4
http://blog.app/galleries-lazy-load?page=2
http://blog.app/galleries-lazy-load?page=4
http://blog.app/galleries-lazy-load?page=2
http://blog.app/galleries-lazy-load?page=3
http://blog.app/galleries-lazy-load?page=3
http://blog.app/galleries-lazy-load?page=3
http://blog.app/galleries-lazy-load?page=5
http://blog.app/galleries-lazy-load?page=3
http://blog.app/galleries-lazy-load?page=2
http://blog.app/galleries-lazy-load?page=3 
```

测试主页性能的脚本将并行运行两个围攻过程，一个针对主页，另一个针对生成的 URL 列表。

要使用 Siege(在 Docker 中)执行单个 HTTP 请求，请运行:

```
docker run --rm -t yokogawa/siege -c1 -r1 blog.app 
```

**注意:**如果你没有使用 Docker，你可以省略`docker run --rm -t yokogawa/siege`部分，用同样的参数运行围攻。

要对主页运行 50 个并发用户的 1 分钟测试，延迟 1 秒，请执行:

```
docker run --rm -t yokogawa/siege -d1 -c50 -t1M http://blog.app 
```

要针对`lazy-load-urls.txt`中的 URL 运行 50 个并发用户的 1 分钟测试，请执行:

```
docker run --rm -v `pwd`:/var/siege:ro -t yokogawa/siege -i --file=/var/siege/lazy-load-urls.txt -d1 -c50 -t1M 
```

从您的`lazy-load-urls.txt`所在的目录中执行此操作(该目录将被挂载为 Docker 中的只读卷)。

运行一个脚本 [test-homepage.sh](https://github.com/sitepoint-editors/multi-image-gallery-blog/blob/master/scripts/test-homepage.sh) 会启动 2 个围攻进程(按照这个[栈溢出答案](https://stackoverflow.com/a/5553774)建议的方式)并输出结果。

假设我们已经在装有 Nginx 和 PHP-FPM 7.1 的服务器上部署了该应用程序，加载了 25，000 个用户和 30，000 个图库。对应用主页进行负载测试的结果如下:

```
./test-homepage.sh

Transactions:               499 hits
Availability:               100.00 %
Elapsed time:               59.10 secs
Data transferred:           1.49 MB
Response time:              4.75 secs
Transaction rate:           8.44 trans/sec
Throughput:                 0.03 MB/sec
Concurrency:                40.09
Successful transactions:    499
Failed transactions:        0
Longest transaction:        16.47
Shortest transaction:       0.17

Transactions:               482 hits
Availability:               100.00 %
Elapsed time:               59.08 secs
Data transferred:           6.01 MB
Response time:              4.72 secs
Transaction rate:           8.16 trans/sec
Throughput:                 0.10 MB/sec
Concurrency:                38.49
Successful transactions:    482
Failed transactions:        0
Longest transaction:        15.36
Shortest transaction:       0.15 
```

尽管主页和惰性负载测试的应用程序可用性都是 100%，但响应时间只有 5 秒左右，这不是我们对高性能应用程序的预期。

#### 测试单个图库页面

测试单个图库页面稍微简单一点:我们将对`galleries.txt`文件运行围攻，其中有一个要测试的单个图库页面 URL 列表。

从`galleries.txt`文件所在的目录(该目录将作为 Docker 中的只读卷挂载)，运行以下命令:

```
docker run --rm -v `pwd`:/var/siege:ro -t yokogawa/siege -i --file=/var/siege/galleries.txt -d1 -c50 -t1M 
```

单个图库页面的负载测试结果略好于主页:

```
./test-single-gallery.sh
** SIEGE 3.0.5
** Preparing 50 concurrent users for battle.
The server is now under siege...
Lifting the server siege...      done.

Transactions:               3589 hits
Availability:               100.00 %
Elapsed time:               59.64 secs
Data transferred:           11.15 MB
Response time:              0.33 secs
Transaction rate:           60.18 trans/sec
Throughput:                 0.19 MB/sec
Concurrency:                19.62
Successful transactions:    3589
Failed transactions:        0
Longest transaction:        1.25
Shortest transaction:       0.10 
```

## 测试，测试，测试

为了确保我们未来实现的改进不会破坏任何东西，我们至少需要一些测试。

首先，我们需要 PHPUnit 作为开发依赖项:

```
composer req --dev phpunit 
```

然后，我们将通过将 Flex 创建的`phpunit.xml.dist`复制到`phpunit.xml`来创建一个简单的 PHPUnit 配置，并更新环境变量(例如，测试环境的`DATABASE_URL`变量)。另外，我在`.gitignore`中加入了`phpunit.xml`。

接下来，我们为博客主页和单个图库页面创建基本的[功能/冒烟测试](https://symfony.com/doc/current/best_practices/tests.html#functional-tests)。冒烟测试是一种*“揭示严重到足以拒绝未来软件发布的简单故障的初步测试”*。因为实现冒烟测试很容易，所以没有正当的理由为什么要避免它们！

这些测试只会断言您在`urlProvider()`方法中提供的 URL 会产生一个成功的 HTTP 响应代码(即 HTTP 状态代码是 2xx 或 3xx)。

对主页和五个单独的图库页面进行简单的冒烟测试[可能看起来像这样](https://github.com/sitepoint-editors/multi-image-gallery-blog/blob/master/tests/SmokeTest.php):

```
namespace App\Tests;

use App\Entity\Gallery;
use Psr\Container\ContainerInterface;
use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;
use Symfony\Component\Routing\RouterInterface;

class SmokeTest extends WebTestCase
{
    /** @var  ContainerInterface */
    private $container;

    /**
     * @dataProvider urlProvider
     */
    public function testPageIsSuccessful($url)
    {
        $client = self::createClient();
        $client->request('GET', $url);

        $this->assertTrue($client->getResponse()->isSuccessful());
    }

    public function urlProvider()
    {
        $client = self::createClient();
        $this->container = $client->getContainer();

        $urls = [
            ['/'],
        ];

        $urls += $this->getGalleriesUrls();

        return $urls;
    }

    private function getGalleriesUrls()
    {
        $router = $this->container->get('router');
        $doctrine = $this->container->get('doctrine');
        $galleries = $doctrine->getRepository(Gallery::class)->findBy([], null, 5);

        $urls = [];

        /** @var Gallery $gallery */
        foreach ($galleries as $gallery) {
            $urls[] = [
                '/' . $router->generate('gallery.single-gallery', ['id' => $gallery->getId()],
                    RouterInterface::RELATIVE_PATH),
            ];
        }

        return $urls;
    }

} 
```

运行`./vendor/bin/phpunit`并查看测试是否通过:

```
./vendor/bin/phpunit
PHPUnit 6.5-dev by Sebastian Bergmann and contributors.

...

5 / 5 (100%)

Time: 4.06 seconds, Memory: 16.00MB

OK (5 tests, 5 assertions) 
```

请注意，硬编码重要的 URL(例如，静态页面或一些众所周知的 URL)比在测试中生成它们更好。点击了解更多关于 PHPUnit 和 TDD [的信息。](https://www.sitepoint.com/re-introducing-phpunit-getting-started-tdd-php/)

## 敬请关注

本系列即将发布的文章将详细介绍 PHP 和 MySQL 性能优化、提高整体性能感知以及其他提高应用程序性能的技巧和诀窍。

## 分享这篇文章