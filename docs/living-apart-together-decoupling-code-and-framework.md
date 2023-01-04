# 一起生活:代码和框架的解耦

> 原文：<https://www.sitepoint.com/living-apart-together-decoupling-code-and-framework/>

当然，您使用最新的技术和框架进行开发。你已经[自己编写了 2.5 框架](http://www.brandonsavage.net/why-every-developer-should-write-their-own-framework/ "Why Every Developer Should Write Their Own Framework | BrandonSavage.net")，你的代码是 [PSR-2 兼容的](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md "fig-standards/accepted/PSR-2-coding-style-guide.md at master - php-fig/fig-standards")，完全经过单元测试，有附带的 [PHPMD](http://phpmd.org/ "PHPMD – PHP Mess Detector") 和 [PHPCS](http://pear.php.net/package/PHP_CodeSniffer "PHP_CodeSniffer") config，甚至可能附带适当的文档(真的，存在！).当您最喜欢的框架的新版本发布时，您已经在自己的玩具项目中使用了它，并提交了几份错误报告，甚至可能附带了一个单元测试来证明错误和一个修补程序。如果这描述了你，或者至少是你想成为的开发人员:重新考虑你的代码与框架的关系。

## 框架和你

如今，大多数专业水平的代码都以某种方式依赖于某种框架。这是一件好事，因为这意味着开发人员意识到他们并不孤单，从长远来看，他们正在重用他人的工作来节省大量时间。关于为什么应该使用框架，网上有大量的争论，在本文中，我把它作为一个被证明的最佳实践。但是你的代码到底在多大程度上依赖于框架呢？

在业余时间，我喜欢去 irc.freenode.net[的](http://www.freenode.net/ "About the network") [IRC 频道# ztalk](http://www.zftalk.com/ "Zend Framework IRC Channel | ZFTalk")去帮助别人。当 Zend Framework 2 (ZF2)正在开发时，频道中一个值得注意的趋势是人们询问它何时发布。不是因为他们急于使用它，而是因为他们不想在 ZF2 即将来袭时开始新的 ZF1 项目。一个像样的项目可能需要 3 个月的时间，如果他们必须在开发过程结束时重新开始，才能发布依赖于“最新最棒”的代码，那么现在为 ZF1 开发代码将是巨大的时间浪费。这种想法完全可以理解。没有人喜欢把时间、精力和/或金钱投入到某样东西上，却发现它已经过时了，失去了一半的价值。如果你花了 3 个月编写代码，你希望它是迄今为止发布的最好的东西，没有明显的缺陷。

所以，用 Symfony(或者其他什么框架)代替？很多人走了这条路，甚至完全转换了语言(Python 和 Ruby 很流行)，这样他们就不必推迟他们的项目了。其他人完全推迟他们的项目，直到 ZF2 发布日期之后！推迟一个项目永远不应该是一个选项，这样就可以让切换框架不必遭受版本碰撞。但是现在让我告诉你:你应该和 ZF1 一起发展，即使 ZF2 明天就能上市。如果你愿意，可以用 ZF2 和 ZF3 重新表述，或者插入你喜欢的框架以及当前和未来的版本。

## 城市孤独

为了便于讨论，让我们假设现在是 2011 年，ZF2 的工作正在进行中，但是还没有明确的时间表；该做的时候就要做。

虽然重用您最喜欢的框架所提供的尽可能多的代码是很棒的，但是您的代码必须能够在几天内切换框架。你是 ZF1 的高手吗？然后用 ZF1 写你的新项目，即使 ZF2 下个月可能会打。如果设计正确，即使项目涉众决定项目必须提供 ZF2 支持，这也不会是一个挫折。根据您使用的框架组件的数量，这种改变很容易在一周内完成。同样的努力，你可以完全转换框架供应商，使用 Symfony，CakePHP，Yii，或者其他框架。如果您编写的代码没有耦合依赖关系，而是编写与框架接口的小包装器，那么您真正的逻辑就不会受到框架可能被升级或替换的外部环境的影响。你的代码快乐地生活在它自己的小世界里，它所依赖的一切都保持不变。

这在理论上听起来很好，但是我知道没有一些代码示例很难理解。所以，我们仍然在 2011 年，仍然在等待 ZF2，我们有这个令人敬畏的想法，一个将回答生命、宇宙和一切的终极问题的组件。考虑到计算答案需要一点时间，我们决定存储结果，这样如果这个问题再次被问到，我们就可以从数据存储中获取它，而不是等待 750 万年来重新计算它。我很想展示实际计算答案的代码，但是因为我也不知道最终的问题，所以我将把重点放在数据存储部分。

```
<?php
$solver = new MyUltimateQuestionSolver();
$answer = $solver->compute();
// now that we have the answer, let's cache it
$db = Zend_Db::Factory('PDO_MYSQL', $config['db']);
$record = array('answer' => $answer);
$db->insert('cache', $record);
```

简单明了，设计合理。但当我们用 ZF2、Symfony 等替换掉 ZF1 时，这种情况就会打破。

注意，我们使用了`Zend_Db`的解耦供应商机制。如果我们把`PDO_MYSQL`换成另一个包装器，同样的代码对于另一个数据存储也能很好地工作。`insert()`和`factory()`调用仍然有效，即使我们切换到 SQLite。那么为什么不为框架本身做同样的事情呢？

让我们将代码转移到一个小包装器中:

```
<?php
class MyWrapperDb
{
    protected $db;

    public function __construct() {
        $this->db = Zend_Db::Factory('PDO_MYSQL', $config['db']);
    }

    public function insert($table, $data) {
        $this->db->insert($table, $data);
    }
}

// Business Logic
$solver = new MyUltimateQuestionSolver();
$answer = $solver->compute();
// now that we have the answer, let's cache it
$db = new MyWrapperDb();
$db->insert('cache', array('answer' => $answer));
```

我们已经从业务逻辑中去掉了特定于框架的细节，现在只需修改包装器就可以随时交换框架。

停留在 2011 年，现在让我们假设我们的利益相关者决定我们需要[发布 MongoDB](https://www.sitepoint.com/introduction-to-mongodb/ "Introduction to MongoDB") 支持，因为它是现在最热门的词汇。ZF1 本身不支持 MongoDB，所以我们在这里放弃了这个框架，而是使用 PHP 扩展:

```
<?php
class MyWrapperDb
{
    protected $db;

    public function __construct() {
        $mongo = new Mongo($config['mongoDSN']);
        $this->db = $mongo->{$config['mongoDB']};
    }

    public function insert($table, $data) {
        $this->db->{$table}->insert($data);
    }
}

// Business Logic
$solver = new MyUltimateQuestionSolver();
$answer = $solver->compute();
// now that we have the answer, let's cache it
$db = new MyWrapperDb();
$db->insert('cache', array('answer' => $answer));
```

## 提炼的抽象

如果您注意的话，您会注意到当我们切换到 MongoDB 时，业务逻辑没有任何变化。这正是我想表达的观点:通过编写与框架分离的业务逻辑(无论是第一个例子中的 ZF1 还是第二个例子中的 MongoDB)，您的业务逻辑保持不变。不需要太多的想象力，就可以看出如何在不改变业务逻辑的情况下，使包装器适应各种可能的数据存储框架。因此，无论何时 ZF2 下降，您的代码都保持不变。你不必检查你的应用程序的每一行，看看它是否使用了 ZF1 的任何东西，然后重构它以使用 ZF2 您只需更新您的包装器，就大功告成了。

如果你将它与[依赖注入/服务定位器](https://www.sitepoint.com/managing-class-dependencies-1/ "Managing Class Dependencies: An Introduction to Dependency Injection, Service Locators, and Factories, Part 1")或类似的设计模式一起使用，你可以非常容易地交换包装器。您创建了一个接口，一个该类型的所有包装器都必须遵守的设计契约，每个解决方案的包装器都可以随意交换。您甚至可以编写一个简单的遵循相同接口的原型包装器，单元测试将轻而易举。

让我们添加一个接口和一个原型包装器，因为 ZF2 已经发布，所以让我们也为它添加一个包装器:

```
<?php
Interface MyWrapperDb
{
    public function insert($table, $data);
}

class MyWrapperDbMongo implements MyWrapperDb
{
    protected $db;

    public function __construct() {
        $mongo = new Mongo($config['mongoDSN']);
        $this->db = $mongo->{$config['mongoDB']};
    }

    public function insert($table, $data) {
        $this->db->{$table}->insert($data);
    }
}

class MyWrapperDbZf1 implements MyWrapperDb
{
    protected $db;

    public function __construct() {
        $this->db = Zend_Db::Factory('PDO_MYSQL', $config['db']);
    }

    public function insert($table, $data) {
        $this->db->insert($table, $data);
    }
}

class MyWrapperDbZf2 implements MyWrapperDb
{
    protected $db;

    public function __construct() {
        $this->db = new ZendDbAdapterAdapter($config['db']);
    }

    public function insert($table, $data) {
        $sql = new ZendDbSqlSql($this->db);
        $insert = $sql->insert();
        $insert->into($table);
        $insert->columns(array_keys($data));
        $insert->values(array_values($data));
        $this->db->query(
            $sql->getSqlStringForSqlObject($insert),
            $this->db::QUERY_MODE_EXECUTE);
    }
}

class MyWrapperDbTest implements MyWrapperDb
{
    public function __construct() { }

    public function insert($table, $data) {
        return ($table === 'cache' && $data['answer'] == 42);
    }
}

// -- snip --

public function compute(MyWrapperDb $db) {
    // Business Logic
    $solver = new MyUltimateQuestionSolver();
    $answer = $solver->compute();
    // now that we have the answer, let's cache it
    $db->insert('cache', array('answer' => $answer));
}
```

在依赖注入点使用接口对包装器强加了一条规则:它们必须遵守接口，否则代码将引发错误。这意味着他们必须实现`insert()`方法，否则他们不会满足契约。我们业务逻辑可以依赖于通过类型提示接口呈现的方法，实际上不必关心实现细节。无论是 ZF1 还是 ZF2 为我们存储数据，MongoDB 扩展，WebDAV 模块将数据上传到远程服务器:业务逻辑并不关心。正如您在上一个示例中看到的，我们甚至可以编写一个小的原型包装器，实现相同的接口。如果我们让依赖注入/服务定位器在单元测试期间使用模型，那么我们可以可靠地测试业务逻辑，而不需要任何形式的数据存储。我们真正需要的是界面。

## 结论

即使你的代码可能没有复杂到需要 750 万年才能运行，你仍然应该把它设计成可移植的，以防地球被沃刚人毁灭，你必须把它重新部署到不同的星球(或框架)上。你不能假设你最喜欢的框架会永远向后兼容，甚至会永远存在。框架，即使有大公司的支持，也是一个实现细节，应该像这样去耦合。这样，您的 cool genius 应用程序就可以始终支持最新、最好的。真正的逻辑将快乐地生活在包装器创建的小气泡中，远离所有邪恶的实现细节和愤怒的依赖。因此，当 ZF3/symfony 3/whatever-else-big-thing 宣布:不要停止编写代码，不要因为必须而学习新的框架(尽管你应该学习，因为你想学习更多)，在泡沫中保持生产力，并在下一个大东西发布时为下一个大东西编写包装。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章