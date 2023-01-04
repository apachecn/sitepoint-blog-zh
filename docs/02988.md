# PHP 中干净的代码架构和测试驱动开发

> 原文：<https://www.sitepoint.com/clean-code-architecture-and-test-driven-development-in-php/>

干净代码架构是由罗伯特·c·马丁在 T2 的博客上介绍的。这个想法是创造一个独立于任何外部机构的架构。您的业务逻辑不应该耦合到框架、数据库或 web 本身。有了独立性，你有几个优势。例如，您可以将技术决策推迟到开发过程中的稍后阶段(例如，选择框架和数据库引擎/提供商)。您还可以轻松地切换实现或比较不同的实现，但最大的优势是您的测试将运行得很快。

想想吧。你真的需要运行一个路由器，加载一个数据库抽象层或者一些 ORM 魔术，或者执行一些其他代码来断言一个或多个结果吗？

我开始学习和实践这个架构是因为我以前最喜欢的框架 *Kohana* 。在某个时候，核心开发人员停止维护代码，这也意味着我的项目不会得到任何进一步的更新或安全修复。这意味着我要么转移到另一个框架并重写整个项目，要么信任社区开发版本。

![Clean Code Architecture](img/49d23d409d3fffcc0e38ce21607aeb41.png)

我本可以选择另一个框架。也许使用 Symfony 1 或者 Zend 1 会更好，但是现在这个框架也已经改变了。

框架将继续变化和发展。有了 composer，很容易安装和替换包，但也很容易放弃一个包(composer 甚至有将一个包标记为放弃的选项)，所以很容易做出“错误的选择”。

在本教程中，我将向您展示我们如何在 PHP 中实现干净的代码架构，以便控制我们自己的逻辑，而不依赖于外部提供者，但仍然使用它们。我们将创建一个简单的留言簿应用程序。

* * *

上图显示了应用程序的不同层。内层对外层一无所知，它们都通过接口进行通信。

最有趣的部分在图像的右下角:*控制流*。该图解释了框架如何与我们的业务逻辑进行通信。控制器将其数据传递到输入端口，由交互器进行处理，产生包含演示者数据的输出端口。

我们将从用例层开始，因为这是包含我们的应用特定逻辑的层。控制器层和其他外层属于一个框架。

注意，下面描述的所有不同阶段都可以从[这个 repo](https://github.com/BlackScorp/guestbook/tags) 中克隆和测试，这个 repo 在 Git 标签的帮助下被整齐地安排成步骤。如果您想看实际操作，只需下载相应的步骤。

## 首次测试

我们通常从 UI 的角度出发。如果我们去留言簿，我们会看到什么？应该有某种输入表单，来自其他访问者的条目，可能还有一个导航面板来搜索条目页面。如果留言簿是空的，我们可能会看到类似“未找到条目”的消息。

在我们的第一个测试中，我们想要断言一个空的条目列表，它看起来像这样:

```
<?php
require_once __DIR__ . '/../../vendor/autoload.php';
class ListEntriesTest extends PHPUnit_Framework_TestCase
{
    public function testEntriesNotExists()
    {
        $request = new FakeViewEntriesRequest();
        $response = new FakeViewEntriesResponse();
        $useCase = new ViewEntriesUseCase();
        $useCase->process($request, $response);
        $this->assertEmpty($response->entries);
    }
} 
```

在这个测试中，我使用了与 Bob 叔叔略有不同的符号。交互器是用例，输入端口是请求，输出端口是响应。

用例总是包含方法过程，该方法过程具有指向其特定请求和响应接口的类型提示。

根据 TDD 中的红色、绿色和重构循环，这个测试应该并且将会失败，因为类不存在。

创建类文件、方法和属性之后，测试通过。由于这些类是空的，我们此时不需要使用重构循环。

接下来，我们要断言我们实际上可以看到一些条目。

```
<?php
require_once __DIR__ . '/../../vendor/autoload.php';
use BlackScorp\GuestBook\Fake\Request\FakeViewEntriesRequest;
use BlackScorp\GuestBook\Fake\Response\FakeViewEntriesResponse;
use BlackScorp\GuestBook\UseCase\ViewEntriesUseCase;
class ListEntriesTest extends PHPUnit_Framework_TestCase
{
    public function testEntriesNotExists()
    {
        $request = new FakeViewEntriesRequest();
        $response = new FakeViewEntriesResponse();
        $useCase = new ViewEntriesUseCase();
        $useCase->process($request, $response);
        $this->assertEmpty($response->entries);
    }
    public function testCanSeeEntries()
    {
        $request = new FakeViewEntriesRequest();
        $response = new FakeViewEntriesResponse();
        $useCase = new ViewEntriesUseCase();
        $useCase->process($request, $response);
        $this->assertNotEmpty($response->entries);
    }
} 
```

如我们所见，测试失败了，我们处于 TDD 周期的*红色*部分。为了通过测试，我们必须在用例中加入一些逻辑。

## 勾画出用例逻辑

在开始逻辑之前，我们应用参数类型提示并创建接口。

```
<?php
namespace BlackScorp\GuestBook\UseCase;

use BlackScorp\GuestBook\Request\ViewEntriesRequest;
use BlackScorp\GuestBook\Response\ViewEntriesResponse;

class ViewEntriesUseCase
{
    public function process(ViewEntriesRequest $request, ViewEntriesResponse $response){

    }
} 
```

这类似于图形艺术家通常的工作方式。他们通常会画一些形状和线条来了解最终的画面，而不是从头到尾画完整幅画。之后，他们使用这些形状作为指导，并添加更多的细节。这个过程叫做“[写生](https://en.wikipedia.org/wiki/Sketch_%28drawing%29)”。

例如，我们使用*仓库*和*工厂*作为我们的向导，而不是形状和线条。

储存库是一个抽象层，用于从存储中检索数据。存储可以是数据库、文件、外部 API 甚至是内存。

要查看留言簿条目，我们必须在我们的存储库中找到实体，将它们转换为视图，并将它们添加到响应中。

```
<?php
namespace BlackScorp\GuestBook\UseCase;

use BlackScorp\GuestBook\Request\ViewEntriesRequest;
use BlackScorp\GuestBook\Response\ViewEntriesResponse;

class ViewEntriesUseCase
{
    public function process(ViewEntriesRequest $request, ViewEntriesResponse $response){
		$entries = $this->entryRepository->findAllPaginated($request->getOffset(), $request->getLimit());

        if(!$entries){
            return;
        }

        foreach($entries as $entry){
            $entryView = $this->entryViewFactory->create($entry);
            $response->addEntry($entryView);
        }
    }
} 
```

您可能会问，为什么我们要将入口实体转换成视图？

原因是实体不应该超出用例层。我们只能在存储库的帮助下找到一个实体，所以我们在必要时修改/复制它，然后将它放回存储库(当修改时)。

当我们开始将实体移动到外层时，最好添加一些额外的方法用于通信目的，但是实体应该只包含核心业务逻辑。

由于我们不确定如何格式化条目，我们可以推迟这一步。

另一个问题可能是“为什么是工厂？”

如果我们在循环中创建一个新的实例，比如

```
$entryView = new EntryView($entry);
$response->addEntry($entryView); 
```

我们会违反[依赖性反转原则](https://en.wikipedia.org/wiki/Dependency_inversion_principle)。如果以后，我们在相同的用例逻辑中需要另一个视图对象，我们将不得不改变代码。有了工厂，我们有一个简单的方法来实现不同的视图，这些视图可能包含不同的格式逻辑，同时仍然使用相同的用例。

## 实现外部依赖关系

至此，我们已经知道了用例的依赖关系:`$entryViewFactory`和`$entryRepository`。我们也知道依赖的方法。`EntryViewFactory`有一个接受`EntryEntity`的 create 方法，`EntryRepository`有一个返回数组`EntryEntities`的`findAll()`方法。现在我们可以用这些方法创建接口，并将它们应用到用例中。

*入口库*看起来会是这样的:

```
<?php
namespace BlackScorp\GuestBook\Repository;

interface EntryRepository {
    public function findAllPaginated($offset,$limit);
} 
```

用例是这样的

```
<?php
namespace BlackScorp\GuestBook\UseCase;

use BlackScorp\GuestBook\Repository\EntryRepository;
use BlackScorp\GuestBook\Request\ViewEntriesRequest;
use BlackScorp\GuestBook\Response\ViewEntriesResponse;
use BlackScorp\GuestBook\ViewFactory\EntryViewFactory;

class ViewEntriesUseCase
{
    /**
     * @var EntryRepository
     */
    private $entryRepository;
    /**
     * @var EntryViewFactory
     */
    private $entryViewFactory;

    /**
     * ViewEntriesUseCase constructor.
     * @param EntryRepository $entryRepository
     * @param EntryViewFactory $entryViewFactory
     */
    public function __construct(EntryRepository $entryRepository, EntryViewFactory $entryViewFactory)
    {
        $this->entryRepository = $entryRepository;
        $this->entryViewFactory = $entryViewFactory;
    }

    public function process(ViewEntriesRequest $request, ViewEntriesResponse $response)
    {
        $entries = $this->entryRepository->findAllPaginated($request->getOffset(), $request->getLimit());
        if (!$entries) {
            return;
        }

        foreach ($entries as $entry) {
            $entryView = $this->entryViewFactory->create($entry);
            $response->addEntry($entryView);
        }
    }
} 
```

如您所见，由于缺少依赖实现，测试仍然会失败。所以这里我们只是创建一些假的物体。

```
<?php
require_once __DIR__ . '/../../vendor/autoload.php';
use BlackScorp\GuestBook\Fake\Request\FakeViewEntriesRequest;
use BlackScorp\GuestBook\Fake\Response\FakeViewEntriesResponse;
use BlackScorp\GuestBook\UseCase\ViewEntriesUseCase;
use BlackScorp\GuestBook\Entity\EntryEntity;
use BlackScorp\GuestBook\Fake\Repository\FakeEntryRepository;
use BlackScorp\GuestBook\Fake\ViewFactory\FakeEntryViewFactory;
class ListEntriesTest extends PHPUnit_Framework_TestCase
{
    public function testEntriesNotExists()
    {
        $entryRepository = new FakeEntryRepository();
        $entryViewFactory = new FakeEntryViewFactory();
        $request = new FakeViewEntriesRequest();
        $response = new FakeViewEntriesResponse();
        $useCase = new ViewEntriesUseCase($entryRepository, $entryViewFactory);
        $useCase->process($request, $response);
        $this->assertEmpty($response->entries);
    }
    public function testCanSeeEntries()
    {
		$entities = [];
        $entities[] = new EntryEntity();
        $entryRepository = new FakeEntryRepository($entities);
        $entryViewFactory = new FakeEntryViewFactory();
        $request = new FakeViewEntriesRequest();
        $response = new FakeViewEntriesResponse();
        $useCase = new ViewEntriesUseCase($entryRepository, $entryViewFactory);
        $useCase->process($request, $response);
        $this->assertNotEmpty($response->entries);
    }
} 
```

因为我们已经为存储库和视图工厂创建了接口，所以我们可以在伪类中实现它们，还可以实现请求/响应接口。

存储库现在看起来像这样:

```
<?php
namespace BlackScorp\GuestBook\Fake\Repository;

use BlackScorp\GuestBook\Repository\EntryRepository;

class FakeEntryRepository implements EntryRepository
{
    private $entries = [];

    public function __construct(array $entries = [])
    {
        $this->entries = $entries;
    }

    public function findAllPaginated($offset, $limit)
    {
        return array_splice($this->entries, $offset, $limit);
    }
} 
```

视图工厂是这样的:

```
<?php
namespace BlackScorp\GuestBook\Fake\ViewFactory;

use BlackScorp\GuestBook\Entity\EntryEntity;
use BlackScorp\GuestBook\Fake\View\FakeEntryView;
use BlackScorp\GuestBook\View\EntryView;
use BlackScorp\GuestBook\ViewFactory\EntryViewFactory;

class FakeEntryViewFactory implements EntryViewFactory
{
    /**
     * @param EntryEntity $entity
     * @return EntryView
     */
    public function create(EntryEntity $entity)
    {

        $view = new FakeEntryView();
        $view->author = $entity->getAuthor();
        $view->text = $entity->getText();
        return $view;
    }
} 
```

你可能会想，为什么我们不使用模仿框架来创建依赖关系呢？有两个原因:

1.  因为用编辑器创建一个实际的类很容易。所以没必要用它们。
2.  当我们开始创建框架的实现时，我们可以在 DI 容器中使用这些假的类，并修改模板，而不必创建真正的实现。

测试现在通过了，我们可以开始重构了。事实上，在用例类中没有什么可以重构的，只有在测试类中。

## 重构测试

执行实际上是相同的，我们只是有不同的设置和断言。因此，我们可以提取假类的初始化，并将用例处理成私有函数`processUseCase`。

测试类现在应该看起来像这样

```
<?php
require_once __DIR__ . '/../../vendor/autoload.php';

use BlackScorp\GuestBook\Entity\EntryEntity;
use BlackScorp\GuestBook\Fake\Repository\FakeEntryRepository;
use BlackScorp\GuestBook\Fake\ViewFactory\FakeEntryViewFactory;
use BlackScorp\GuestBook\Fake\Request\FakeViewEntriesRequest;
use BlackScorp\GuestBook\Fake\Response\FakeViewEntriesResponse;
use BlackScorp\GuestBook\UseCase\ViewEntriesUseCase;

class ListEntriesTest extends PHPUnit_Framework_TestCase
{

    public function testCanSeeEntries()
    {
 		$entries = [
            new EntryEntity('testAuthor','test text')
        ];
        $response = $this->processUseCase($entries);
        $this->assertNotEmpty($response->entries);
    }

    public function testEntriesNotExists()
    {
        $entities = [];
        $response = $this->processUseCase($entities);
        $this->assertEmpty($response->entries);
    }

    /**
     * @param $entities
     * @return FakeViewEntriesResponse
     */
    private function processUseCase($entities)
    {
        $entryRepository = new FakeEntryRepository($entities);
        $entryViewFactory = new FakeEntryViewFactory();
        $request = new FakeViewEntriesRequest();
        $response = new FakeViewEntriesResponse();
        $useCase = new ViewEntriesUseCase($entryRepository, $entryViewFactory);
        $useCase->process($request, $response);
        return $response;
    }
} 
```

## 独立性ˌ自立性

例如，现在我们可以很容易地用无效的实体创建新的测试用例，并且我们可以将存储库和工厂转移到 setup 方法，用真实的实现运行测试。

如您所见，我们可以在 DI 容器中实现一个现成的用例，并在任何框架中使用它。逻辑是框架不可知的。

例如，我们可以创建另一个与外部 API 对话的存储库实现，并将其传递给用例。这种逻辑与数据库无关。

我们可以创建 CLI 请求/响应对象，并将它们传递给控制器内部使用的同一个用例，这样逻辑就独立于平台。

我们甚至可以在一行中执行不同的用例，其中每个用例都可能修改实际的响应对象。

```
class MainController extends BaseController
{
    public function indexAction(Request $httpRequest)
    {
        $indexActionRequest = new IndexActionRequest($httpRequest);
        $indexActionResponse = new IndexActionResponse();
        $this->getContainer('ViewNavigation')->process($indexActionRequest, $indexActionResponse);
        $this->getContainer('ViewNewsEntries')->process($indexActionRequest, $indexActionResponse);
        $this->getContainer('ViewUserAvatar')->process($indexActionRequest, $indexActionResponse);
        $this->render($indexActionResponse);

    }
} 
```

## 页码

现在我们要添加分页。测试可能看起来像这样:

```
 public function testCanSeeFiveEntries(){
        $entities = [];
        for($i = 0;$i<10;$i++){
             $entities[] = new EntryEntity('Author '.$i,'Text '.$i);
        }

        $response = $this->processUseCase($entities);
        $this->assertNotEmpty($response->entries);
        $this->assertSame(5,count($response->entries));
    } 
```

这个测试将会失败，所以我们必须修改`UseCase`的`process`方法，并将方法`findAll`重命名为`findAllPaginated`。

```
 public function process(ViewEntriesRequest $request, ViewEntriesResponse $response){
        $entries = $this->entryRepository->findAllPaginated($request->getOffset(), $request->getLimit());
//....
    } 
```

现在，我们将新参数应用到接口和伪存储库，并将新方法添加到请求接口。

存储库的`findAllPaginated`方法略有变化:

```
 public function findAllPaginated($offset, $limit)
    {
        return array_splice($this->entries, $offset, $limit);
    } 
```

我们必须在测试中移动请求对象；此外，在我们的请求对象的构造函数中将需要 limit 参数。这样，我们将强制安装程序使用新实例创建限制。

```
 public function testCanSeeFiveEntries(){
        $entities = [];
        for($i = 0;$i<10;$i++){
            $entities[] = new EntryEntity();
        }
        $request = new FakeViewEntriesRequest(5);
        $response = $this->processUseCase($request, $entities);
        $this->assertNotEmpty($response->entries);
        $this->assertSame(5,count($response->entries));
    } 
```

测试通过了，但我们必须测试是否能看到接下来的五个条目。因此，我们必须用一个`setPage`方法来扩展请求对象。

```
<?php
namespace BlackScorp\GuestBook\Fake\Request;
use BlackScorp\GuestBook\Request\ViewEntriesRequest;
class FakeViewEntriesRequest implements ViewEntriesRequest{
    private $offset = 0;
    private $limit = 0;

    /**
     * FakeViewEntriesRequest constructor.
     * @param int $limit
     */
    public function __construct($limit)
    {
        $this->limit = $limit;
    }

    public function setPage($page = 1){
        $this->offset = ($page-1) * $this->limit;
    }
    public function getOffset()
    {
        return $this->offset;
    }

    public function getLimit()
    {
        return $this->limit;
    }

} 
```

使用这种方法，我们可以测试是否可以看到接下来的五个条目。

```
 public function testCanSeeFiveEntriesOnSecondPage(){
        $entities = [];
        $expectedEntries = [];
        $entryViewFactory = new FakeEntryViewFactory();
        for($i = 0;$i<10;$i++){
            $entryEntity = new EntryEntity();
            if($i >= 5){
                $expectedEntries[]=$entryViewFactory->create($entryEntity);
            }
            $entities[] =$entryEntity;
        }
        $request = new FakeViewEntriesRequest(5);
        $request->setPage(2);
        $response = $this->processUseCase($request,$entities);
        $this->assertNotEmpty($response->entries);
        $this->assertSame(5,count($response->entries));
        $this->assertEquals($expectedEntries,$response->entries);
    } 
```

现在测试通过了，我们可以重构了。我们将`FakeEntryViewFactory`移动到`setup`方法，我们就完成了这个特性。最终的测试类如下所示:

```
<?php
require_once __DIR__ . '/../../vendor/autoload.php';

use BlackScorp\GuestBook\Entity\EntryEntity;
use BlackScorp\GuestBook\Fake\Repository\FakeEntryRepository;
use BlackScorp\GuestBook\Fake\Request\FakeViewEntriesRequest;
use BlackScorp\GuestBook\Fake\Response\FakeViewEntriesResponse;
use BlackScorp\GuestBook\Fake\ViewFactory\FakeEntryViewFactory;
use BlackScorp\GuestBook\UseCase\ViewEntriesUseCase;

class ListEntriesTest extends PHPUnit_Framework_TestCase
{
    public function testEntriesNotExists()
    {
        $entries = [];
        $request = new FakeViewEntriesRequest(5);
        $response = $this->processUseCase($request, $entries);
        $this->assertEmpty($response->entries);
    }

    public function testCanSeeEntries()
    {
        $entries = [
            new EntryEntity('testAuthor', 'test text')
        ];
        $request = new FakeViewEntriesRequest(5);
        $response = $this->processUseCase($request, $entries);
        $this->assertNotEmpty($response->entries);
    }

    public function testCanSeeFiveEntries()
    {
        $entities = [];
        for ($i = 0; $i < 10; $i++) {
            $entities[] = new EntryEntity('Author ' . $i, 'Text ' . $i);
        }
        $request = new FakeViewEntriesRequest(5);
        $response = $this->processUseCase($request, $entities);
        $this->assertNotEmpty($response->entries);
        $this->assertSame(5, count($response->entries));
    }

    public function testCanSeeFiveEntriesOnSecondPage()
    {
        $entities = [];
        $expectedEntries = [];
        $entryViewFactory = new FakeEntryViewFactory();
        for ($i = 0; $i < 10; $i++) {
            $entryEntity = new EntryEntity('Author ' . $i, 'Text ' . $i);
            if ($i >= 5) {
                $expectedEntries[] = $entryViewFactory->create($entryEntity);
            }
            $entities[] = $entryEntity;
        }
        $request = new FakeViewEntriesRequest(5);
        $request->setPage(2);
        $response = $this->processUseCase($request, $entities);
        $this->assertNotEmpty($response->entries);
        $this->assertSame(5, count($response->entries));
        $this->assertEquals($expectedEntries, $response->entries);
    }

    /**
     * @param $request
     * @param $entries
     * @return FakeViewEntriesResponse
     */
    private function processUseCase($request, $entries)
    {
        $repository = new FakeEntryRepository($entries);
        $factory = new FakeEntryViewFactory();
        $response = new FakeViewEntriesResponse();
        $useCase = new ViewEntriesUseCase($repository, $factory);
        $useCase->process($request, $response);
        return $response;
    }
} 
```

## 结论

在本教程中，我们已经看到了测试如何将我们引向用例，用例将我们引向接口，而接口又将我们引向虚假的实现。

同样，这篇文章的源代码可以在 [Github](https://github.com/BlackScorp/guestbook) 上找到——查看这篇文章所有不同源代码阶段的标签。

本教程演示了将测试驱动开发和干净的代码架构应用到任何新创建的项目中并不困难。最大的好处是，虽然逻辑是完全独立的，但我们仍然可以使用第三方库。

有问题吗？评论？请把它们留在赞按钮正下方的评论区！

## 分享这篇文章