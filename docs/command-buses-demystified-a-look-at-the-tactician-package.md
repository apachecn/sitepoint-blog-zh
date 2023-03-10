# 去神秘化的命令总线:看看战术家软件包

> 原文：<https://www.sitepoint.com/command-buses-demystified-a-look-at-the-tactician-package/>

命令总线最近得到了很多社区的关注。当试图理解所有的概念和术语时，这个主题一开始可能会让人不知所措，但是本质上——命令总线所做的事情实际上非常简单。

在本文中，我们将仔细研究命令模式的变体；它们的组件；什么是命令总线；和一个使用 Tactician 软件包的示例应用程序。

![Hero Image?](img/5e13c366e594f324f946db01ee4b6452.png)

## 概观

那么，到底什么是命令总线呢？

![Command Pattern](img/c356b49a00d2e5c58862a13ff8f4242a.png)

命令总线的作用是确保将命令传输到其处理程序。命令总线接收命令，该命令只不过是描述意图的消息，并将其传递给负责执行预期行为的处理程序。因此，这个过程可以被看作是对服务层的调用——在服务层中，命令总线在两者之间传递消息。

在引入命令总线之前，服务层通常是类的集合，没有标准的调用方式。命令总线通过提供一致的接口和更好地定义两层之间的边界来解决这个问题。标准接口还使得通过包装装饰器或添加中间件来添加额外的功能成为可能。

因此，命令总线不仅仅是调用服务层。基本实现可能只基于命名约定定位处理程序，但是更复杂的配置可能通过管道传递命令。管道可以执行额外的行为，例如:在事务中包装行为；通过网络发送命令；或者一些排队和日志记录。

在我们进一步了解使用命令总线的优势之前，让我们先来看看使之成为可能的各个组件。

### 命令

命令模式是四人组描述的行为模式之一，作为两个对象交流的方式。

让事情变得复杂一点的是，这种模式已经演变成了可供选择的设计。像 CQS(命令-查询分离)和 CQRS(命令查询责任分离)这样的架构模式也使用命令，但是在它们的上下文中——命令只是一条消息。

传统上，GoF 命令会自己处理:

```
final class CreateDeck
{
    /**
       * @var string
     */
    private $id;

    /**
       * @param string $id
     */
    public function __construct($id)
    {
        $this->id = $id;
    }

    /**
       * @return Deck
     */
    public function execute()
    {
        // do things
    }
} 
```

由于命令模式的这种方法包含了行为，因此没有必须路由到处理程序的消息。不需要命令总线。

然而，命令消息模式建议将意图与解释分开，在域内表达动作:

```
final class CreateDeck
{
    /**
       * @var string
     */
    private $id;

    /**
       * @param string $id
     */
    public function __construct($id)
    {
        $this->id = $id;
    }

    /**
       * @return DeckId
     */
    public function getId()
    {
        return DeckId::fromString($this->id);
    }
} 
```

在本例中，以及本文的其余部分，我们将使用命令作为消息。它捕捉用户的意图，并包含执行任务所需的输入。它明确描述了系统可以执行的行为。因此命令的命名是命令性的，比如:`CreateDeck`、`ShuffleDeck`和`DrawCard`。

命令通常被称为 d to(数据传输对象)，因为它们用于包含从一个位置传输到另一个位置的数据。因此，命令是不可变的。创建后，数据预计不会改变。您会注意到我们的`CreateDeck`示例命令不包含 setters，或者任何其他改变内部状态的方法。这确保了它在传输到处理程序的过程中不会改变。

### 命令处理程序

处理程序解释特定命令的意图，并执行预期的行为。它们与命令有 1:1 的关系——这意味着每个命令只有一个处理程序。

```
final class CreateDeckHandler
{
    /**
       * @var DeckRepository
     */
    private $decks;

    /**
       * @param DeckRepository $decks
     */
    public function __construct(DeckRepository $decks)
    {
        $this->decks = $decks;
    }

    /**
       * @param CreateDeck $command
     */
    public function handle(CreateDeck $command)
    {
        $id = $command->getId();

        $deck = Deck::standard($id);

        $this->decks->add($deck);
    }
} 
```

在我们上面的例子中，创建了一个新的甲板。同样需要注意的是没有发生的事情。它不填充视图；返回 HTTP 响应代码或写入控制台。命令可以在任何地方执行，因此处理程序对调用环境是不可知的。当设计应用程序和外部世界之间的边界时，这是非常强大的。

### 命令总线

最后是命令总线本身。如上所述，命令总线的职责是将命令传递给它的处理程序。让我们看一个例子。

假设我们需要公开一个 RESTful API 端点来创建新的卡片组:

```
use Illuminate\Http\Request;

final class DeckApiController
{
    /**
       * @var CommandBus
     */
    private $bus;

    /**
       * @var CommandBus $bus
     */
    public function __construct(CommandBus $bus)
    {
        $this->bus = $bus;
    }

    /**
       * @var Request $request
     */
    public function create(Request $request)
    {
        $deckId = $request->input('id');

        $this->bus->execute(
            new CreateDeck($deckId)
        );

        return Response::make("", 202);
    }
} 
```

现在想象一下需要从控制台创建新的卡片组。我们可以通过总线再次传递相同的命令来处理这一要求:

```
class CreateDeckConsole extends Console
{
    /**
       * @var string
     */
    protected $signature = 'deck';

    /**
       * @var string
     */
    protected $description = 'Create a new Deck';

    /**
       * @var CommandBus
     */
    private $bus;

    /**
       * @var CommandBus $bus
     */
    public function __construct(CommandBus $bus)
    {
        $this->bus = $bus;
    }

    public function handle()
    {
        $deckId = $this->argument('id');

        $this->bus->execute(
            new CreateDeck($deckId)
        );

        $this->comment("Created: " . $deckId);
    }
} 
```

这些示例不涉及创建卡片组的实现细节。我们的控制器和控制台命令使用命令总线将指令传递给应用程序，使它们能够专注于如何响应它们的请求类型。它还允许我们删除潜在的大量重复逻辑。

测试我们的控制器和控制台命令现在是一项简单的任务。我们需要做的就是根据请求断言传递给总线的命令是格式良好的。

## 例子

[示例应用程序](https://github.com/acairns/sitepoint-command-bus)为一个域考虑一副牌。该应用程序有一系列命令:`CreateDeck`、`ShuffleDeck`和`DrawCard`。

到目前为止,`CreateDeck`只是在探索概念时提供了背景。接下来，我们将设置[战术家](https://github.com/thephpleague/tactician)作为我们的指挥总线，执行我们的命令。

### 配置战术家

一旦[安装了 Tactician](https://tactician.thephpleague.com/installation/) ，您将需要在应用程序的引导过程中对其进行配置。

在我们的例子中，我们把它放在一个引导脚本中，但是你可能希望把它添加到一个依赖注入容器中。

在创建我们的 Tactician 实例之前，我们需要设置我们的管道。Tactician 使用中间件来提供管道，并作为软件包的插件系统。事实上，Tactician 中的一切都是中间件——甚至是对命令的处理。

![Middleware Pipeline](img/52a9e4b00738967fa6210177b2f04147.png)

我们在管道中需要的唯一中间件是处理命令执行的东西。如果这听起来很复杂，那么你可以很快地拼凑出你自己的中间件。然而，我们将使用战术家提供的`CommandHandlerMiddleware`。这个对象的创建有几个依赖项。

```
public function __construct(
    CommandNameExtractor $commandNameExtractor,
    HandlerLocator $handlerLocator,
    MethodNameInflector $methodNameInflector
) 
```

正如我们前面所学的，命令总线为特定的命令定位处理程序。让我们仔细看看这些依赖项是如何处理它们的任务部分的。

#### CommandNameExtractor

`CommandNameExtractor`的作用是获取命令的名称。你可能认为 PHP 的 [get_class()](https://php.net/manual/en/function.get-class.php) 函数可以完成这项工作——你是对的！因此 Tactician 包含了 ClassNameExtractor，它就是这样做的。

#### 手柄定位器

我们还需要一个`HandlerLocator`实例，它将根据命令的名称找到正确的处理程序。Tactician 提供了这个接口的两个实现:CallableLocator 和 InMemoryLocator。

从容器中解析处理程序时，使用`CallableLocator`非常有用。然而，在我们的例子中，我们将用一个`InMemoryLocator`手动注册我们的处理程序。

我们现在拥有了根据命令名获取处理程序实例所需的所有依赖关系——但这还不够。

#### MethodNameInflector

我们仍然需要告诉战术家如何调用处理程序。这就是`MethodNameInflector`发挥作用的地方。屈折器返回期望命令传递给它的方法的名称。

Tactician 通过提供几个流行惯例的实现再次帮助我们。在我们的例子中，我们将遵循在 HandleInflector 中实现的`handle($command)`方法约定。

#### 简单的战术配置

让我们看看目前为止的设置:

```
use League\Tactician\CommandBus;
use League\Tactician\Handler\Locator\InMemoryLocator;
use League\Tactician\Handler\CommandHandlerMiddleware;
use League\Tactician\Handler\MethodNameInflector\HandleInflector;
use League\Tactician\Handler\CommandNameExtractor\ClassNameExtractor;

$handlerMiddleware = new CommandHandlerMiddleware(
    new ClassNameExtractor,
    new InMemoryLocator([]),
    new HandleInflector
);

$bus = new CommandBus([
    $handlerMiddleware
]); 
```

这看起来不错，但仍有一个问题——`InMemoryLocator`需要注册处理程序，以便在运行时可以找到它们。因为这只是几个示例的引导代码，所以现在让我们存储一个对定位器的引用，以便以后需要时可以注册处理程序。

```
$locator = new InMemoryLocator([]);

$handlerMiddleware = new CommandHandlerMiddleware(
    new ClassNameExtractor,
    $locator,
    new HandleInflector
); 
```

在一个合适的应用程序中，您可能希望使用一个定位器，它可以根据命名约定找到命令的处理程序。

战术家现在配置好了。让我们用它来执行`CreateDeck`命令。

### 执行命令

为了创建命令的实例，我们填写了所有必需的构造函数要求:

```
<?php require 'bootstrap.php';

$deckId = DeckId::generate();

$newDeckCommand = new CreateDeck((string) $deckId); 
```

在我们通过总线发送命令之前，剩下的最后一项任务是用之前存储的引用`InMemoryLocator`注册我们的处理程序:

```
$decks = new InMemoryDeckRepository;

$locator->addHandler(
    new CreateDeckHandler($decks),
    CreateDeck::class
); 
```

最后，我们准备好将命令传递到总线中:

```
$bus->handle($newDeckCommand);

var_dump(
    $decks->findById($deckId)
); 
```

真的就这么简单！

## 优势

当应用命令模式或利用命令总线时，有许多优点。

1.  建筑边界

    最重要的好处之一是围绕应用程序的架构边界。诸如用户接口的上层可以跨越边界并通过命令总线提供的一致接口向下层发送命令。

    上层知道发出命令的上下文，但是，一旦消息通过边界——它只是一个可能从任意数量的上下文发出的消息:HTTP 请求；一份乏味的工作；或者其他完全不同的东西。

    界限有助于分离顾虑，让一方不用担心另一方。高层不再需要知道任务是如何完成的——低层也不需要担心它们的使用环境。

2.  框架和客户端解耦

    被边界包围的应用程序对它们的框架是不可知的。它们不处理 HTTP 请求或 cookies。执行行为所需的所有属性都作为命令有效负载的一部分传递。

    与框架的分离允许你在框架改变或更新时维护你的应用程序——并使它更容易测试。

3.  将意图与解释分开

    命令总线的作用是将命令传输到它的处理程序。这本质上意味着执行动作的意图与执行是分开的，否则就不需要命令总线。命令使用业务语言命名，并明确描述应用程序的使用方式。

    当不需要知道如何执行行为时，序列化命令变得容易得多。在分布式系统中，消息可以在一个系统上生成，但在另一个系统上执行，在不同的操作系统上用不同的语言编写。

    当一个代码单元所做的只是产生一条消息时，测试就非常容易了。注入到单元中的命令总线可以被替换，并且消息可以被断言。

你觉得怎么样？命令总线是过度工程化，还是系统架构的伟大工具？

## 分享这篇文章