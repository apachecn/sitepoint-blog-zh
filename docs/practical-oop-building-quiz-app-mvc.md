# 实用面向对象程序设计:构建一个测验应用程序——MVC

> 原文：<https://www.sitepoint.com/practical-oop-building-quiz-app-mvc/>

在本系列的第一部分中，我们开始使用自底向上的设计方法，创建我们的`Quiz`和`Question`实体，为`Quiz`实体编写一个虚拟的[数据映射器](http://martinfowler.com/eaaCatalog/dataMapper.html)类，并为我们的主服务`\QuizApp\Service\Quiz`绘制接口草图，这将为用户解决一个测验定义流程。如果你还没有阅读第一部分，我建议你在继续第二部分之前快速浏览一下，或者从[这里](https://github.com/moteutsch/Practical-OOP/tree/part-1)下载代码。

这一次我们将创建并充实`\QuizApp\Service\Quiz`服务，它将成为我们测验应用程序的主干。然后，我们将使用 Slim MVC 框架编写控制器和视图，最后，创建一个 MongoDB 映射器来代替我们上次编写的虚拟映射器。

## 服务编码:

好了，现在我们已经定义了映射器的接口并创建了实体类，我们拥有了实现具体服务类所需的所有构件。

```
<?php

namespace QuizApp\Service;

use QuizApp\Service\Quiz\Result;

// ...

class Quiz implements QuizInterface
{
    const CURRENT_QUIZ = 'quizService_currentQuiz';
    const CURRENT_QUESTION = 'quizService_currentQuestion';
    const CORRECT = 'quizService_correct';
    const INCORRECT = 'quizService_incorrect';

    private $mapper;

    public function __construct(\QuizApp\Mapper\QuizInterface $mapper)
    {
        $this->mapper = $mapper;
    }

    /** @return Quiz[] */
    public function showAllQuizes()
    {
        return $this->mapper->findAll();
    }

    public function startQuiz($quizOrId)
    {
        if (!($quizOrId instanceof \QuizApp\Entity\Quiz)) {
            $quizOrId = $this->mapper->find($quizOrId);
            if ($quizOrId === null) {
                throw new \InvalidArgumentException('Quiz not found');
            }
        }

        $_SESSION[self::CURRENT_QUIZ] = $quizOrId->getId();
        $_SESSION[self::CORRECT] = $_SESSION[self::INCORRECT] = 0;
    }

    /**
     * @return Question
     * @throws \LogicException
     */
    public function getQuestion()
    {
        $questions = $this->getCurrentQuiz()->getQuestions();
        $currentQuestion = $this->getCurrentQuestionId();
        if ($this->isOver()) {
            throw new \LogicException();
        }
        return $questions[$currentQuestion];
    }

    /** @return bool */
    public function checkSolution($solutionId)
    {
        $result = $this->getQuestion()->isCorrect($solutionId);
        $_SESSION[self::CURRENT_QUESTION] = $this->getCurrentQuestionId() + 1;
        $this->addResult($result);
        if ($this->isOver()) {
            $_SESSION[self::CURRENT_QUESTION] = $_SESSION[self::CURRENT_QUIZ] = null;
        }
        return $result;
    }

    /** @return bool */
    public function isOver()
    {
        try {
            return $this->getCurrentQuestionId() >= count($this->getCurrentQuiz()->getQuestions());
        } catch (\LogicException $e) {
            return true;
        }
    }

    /** @return Result */
    public function getResult()
    {
        return new Result(
            $_SESSION[self::CORRECT], $_SESSION[self::INCORRECT],
            ($_SESSION[self::CORRECT] + $_SESSION[self::INCORRECT]) / 2
        );
    }

    private function getCurrentQuiz()
    {
        if (!isset($_SESSION[self::CURRENT_QUIZ])) {
            throw new \LogicException();
        }
        $quiz = $this->mapper->find($_SESSION[self::CURRENT_QUIZ]);
        if ($quiz === null) {
            throw new \LogicException();
        }
        return $quiz;
    }

    private function getCurrentQuestionId()
    {
        return isset ($_SESSION[self::CURRENT_QUESTION]) ? $_SESSION[self::CURRENT_QUESTION] : 0;
    }

    private function addResult($isCorrect)
    {
        $type = ($isCorrect ? self::CORRECT : self::INCORRECT);
        if (!isset($_SESSION[$type])) {
            $_SESSION[$type] = 0;
        }
        $_SESSION[$type] += 1;
    }
}
```

那是一个长的。让我们一个方法一个方法地检查它。

`showAllQuizes()`方法包装了`QuizMapper::findAll()`方法。我们可以将`$mapper`公开，但这会破坏封装，将低级操作泄露给高级类。

`startQuiz()`方法通过将测验存储在会话中供将来参考来开始作为参数传递的测验。它接受测验实体对象或测验 ID。在后一种情况下，它试图使用`$mapper`找到测验。该方法直接使用`$_SESSION`超全局，这不是最佳实践——例如，如果在命令行环境中使用，服务会中断——但是还没有必要使服务过于复杂。如果我们想让服务在命令行上运行，我们可以将会话中用于存储数据的操作提取到一个接口中。web 控制器将传递一个在内部使用`$_SESSION`超全局的实现，而命令行控制器可能将“会话”变量存储为类属性。

`getQuestion()`方法尝试从数据库中获取当前测验的下一个问题，委托给其他有用的方法，如果测验已经结束或者用户没有在测验中，则抛出异常。`checkSolution()`方法返回用户的答案是否正确，并在回答问题后更新会话以反映测验的状态。

如果当前测验已经结束或者没有测验正在进行，则`isOver()`方法返回 true。

`getResult()`方法返回一个`\QuizApp\Service\Quiz\Result`对象，告诉用户他是否通过了测验以及他答对了多少题。

## 纤巧的控制器和视图:

既然我们已经设置好了 MVC 应用程序的“M ”,是时候编写控制器和视图了。我们使用的是 Slim 框架，但是很容易用任何其他 MVC 框架替换 Slim，因为我们的代码是解耦的。用以下内容创建一个`index.php`文件:

```
<?php

require 'vendor/autoload.php';

session_start();

$service = new \QuizApp\Service\Quiz(
    new \QuizApp\Mapper\HardCoded()
);
$app = new \Slim\Slim();
$app->config(['templates.path' => './views']);
// Controller actions here
$app->run();
```

这是我们 Slim 应用程序的基础。我们创建服务并启动 PHP 会话，因为我们在服务中使用了`$_SESSION` superglobal。最后，我们设置了我们的 Slim 应用程序。关于 Slim 的更多信息，请阅读 Slim 项目网站上的大量文档。

让我们先创建主页。主页上会列出用户可以参加的测验。这方面的控制器代码很简单。通过我们的`index.php`文件中的注释添加以下内容。

```
$app->get('/', function () use ($service, $app) {
    $app->render('choose-quiz.phtml', [
        'quizes' => $service->showAllQuizes()
    ]);}
);
```

我们用`$app->get()`方法定义主页路径。我们以匿名函数的形式，将路由作为第一个参数传递，将要运行的代码作为第二个参数传递。在这个函数中，我们呈现了`choose-quiz.phtml`视图文件，向它传递了我们从服务中检索到的测验列表。让我们对视图进行编码。

```
<h3>choose a quiz</h3>
    <ul>
        <?php foreach ($quizes as $quiz) : ?>
            <li><a href="choose-quiz/<?php echo $quiz->getId();?>"><?php  echo $quiz->getTitle(); ?></a></li>
        <?php endforeach; ?>
    </ul>
```

此时，如果您使用浏览器导航到应用程序的主页，您将看到我们之前硬编码的两个测验，“测验 1”和“测验 2”
主页上的测验链接指向`choose-quiz/:id`，其中`:id`是测验的 ID。这条路线应该从用户选择的测验开始，并将他重定向到他的第一个问题。添加以下路线到`index.php`:

```
$app->get('/choose-quiz/:id', function($id) use ($service, $app) {
        $service->startQuiz($id);
        $app->redirect('/solve-question');
    });
```

现在我们来定义一下`/solve-question`路线。这条路线将向用户显示他正在解决的测验的当前问题。

```
$app->get('/solve-question', function () use ($service, $app) {
        $app->render('solve-question.phtml', [
            'question' => $service->getQuestion(),
        ]);
    }
);
```

route 呈现视图`solve-question.phtml`,其中包含从服务返回的问题。让我们来定义视图。

```
<h3><?php echo $question->getQuestion(); ?></h3>
<form action="check-answer" method="post">
    <ul>
        <?php foreach ($question->getSolutions() as $id => $solution): ?>
        <li><input type="radio" name="id" value="<?php echo $id; ?>"> <?php echo $solution; ?></li>
        <?php endforeach; ?>
    </ul>
    <input type="submit" value="submit">
</form>
```

我们向用户显示一个表格，每个答案有一个单选按钮。表单将结果发送到`check-answer`路径。

```
$app->post('/check-answer', function () use ($service, $app) {
        $isCorrect = $service->checkSolution($app->request->post('id'));
        if (!$service->isOver()) {
            $app->redirect('/solve-question');
        } else {
            $app->redirect('/end');
        }
    });
```

这次我们为“POST”请求定义一个路由，所以我们使用了`$app->post()`方法。为了获得用户发送的解决方案 ID，我们调用`$app->request->post('id')`。该服务返回该答案是否正确。如果有更多的问题需要回答，我们就把他引回“解决问题”的路线。如果他完成了测验，我们就把他送到“终点”路线。这将告诉用户他是否通过了测验，以及他正确回答了多少个问题。

```
$app->get('end', function () use ($service, $app) {
        $app->render('end.phtml', [
            'result' => $service->getResult(),
        ]);
    });
```

我们通过从服务中检索一个`\QuizApp\Service\Quiz\Result`对象并将其传递给视图来实现这一点。

```
<?php if ($result->hasPassed()) : ?>
    <h3>You passed!</h3>
<?php else: ?>
    <h3>You failed!</h3>
<?php endif; ?>
<p>You got <?php echo $result->getCorrect(); ?> out of <?php echo $result->getTotal(); ?> questions right.</p>
<a href="/">Back to quizes</a>
```

## 使用 MongoDB 编写真正的映射器:

至此，应用程序已经完成，并将正常运行——但是我们应该编写一个真正的`\QuizApp\Mapper\QuizInterface`实例来连接 MongoDB。现在，我们正在从我们的`Hardcoded`映射器中获取我们的查询。

如果您还没有安装 MonogoDB，请安装它。

我们需要创建一个数据库，一个集合，并用一个模拟测验来传播这个集合。运行 Mongo–`mongo`–并在终端内部运行以下命令:

```
> use practicaloop
    > db.quizes.insert((
          title: 'First Quiz',
          questions: [{
              question: 'Who\'s buried in Grant\'s tomb?',
              solutions: ['Jack', 'Joe', 'Grant', 'Jill'],
              correctIndex: 2
          }]
      })
```

现在我们需要编写另一个实现 QuizInterface 的映射器。

```
<?php

namespace QuizApp\Mapper;

class Mongo implements QuizInterference
{
    private static $MAP = [];

    /** @var \MongoCollection */
    private $collection;

    public function __construct(\MongoCollection $collection)
    {
        $this->collection = $collection;
    }

    /**
     * @return \QuizApp\Entity\Quiz[]
     */
    public function findAll()
    {
        $entities = [];
        $results = $this->collection->find();
        foreach ($results as $result) {
            $entities[] = $e = $this->rowtoEntity($result);
            $this->cacheEntity($e);
        }
        return $entities;
    }

    /**
     * @param int $id
     * @return \QuizApp\Entity\Quiz
     */
    public function find($id)
    {
        $id = (string) $id;
        if (isset(self::$MAP[$id])) {
            return self::$MAP[$id];
        }
        $row = $this->collection->findOne(['_id' => new \MongoId($id)]);
        if ($row === null) {
            return null;
        }
        $entity = $this->rowtoEntity($row);
        $this->cacheEntity($entity);
        return $entity;
    }

    private function cacheEntity($entity)
    {
        self::$MAP[(string) $entity->getId()] = $entity;
    }

    private function rowToEntity($row)
    {
        $result = new \QuizApp\Entity\Quiz(
            $row['title'],
            array_map(function ($question) {
                return new \QuizApp\Entity\Question(
                    $question['question'],
                    $question['solutions'],
                    $question['correctIndex']
                );
            }, $row['questions'])
        );
        $result->setId($row['_id']);
        return $result;
    }
}
```

让我们看看这是怎么回事。该类接受一个`\MongoCollection`作为构造函数参数。然后，它使用集合在`find()`和`findAll()`方法中从数据库中检索行。这两种方法遵循相同的步骤:从数据库中检索一行或多行，将这些行转换成我们的`\QuizApp\Entity\Quiz`和`\QuizApp\Entity\Question`对象，并在内部缓存它们以避免以后必须查找相同的实体。

我们剩下要做的就是在`index.php`文件中将一个新映射器的实例传递给我们的服务。

## 结论:

在本系列中，我们使用服务层和领域模型设计模式构建了一个 MVC web 应用程序。通过这样做，我们遵循了 MVC 的“胖模型，瘦控制器”的最佳实践，将我们的整个控制器代码保持在 40 行。我向您展示了如何创建与实现无关的映射器来访问数据库，并且我们创建了一个服务来运行测验，而不考虑用户界面。我将让您创建应用程序的命令行版本。您可以在此处找到该部分[的完整源代码。](https://github.com/moteutsch/Practical-OOP/tree/part-2)

评论？有问题吗？把它们留在下面！

## 分享这篇文章