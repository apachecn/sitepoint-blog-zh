# 实用面向对象程序设计:构建一个测验应用程序——引导

> 原文：<https://www.sitepoint.com/practical-oop-building-quiz-app-bootstrapping/>

在我作为 PHP 程序员发展的某个阶段，我是在按常规构建 MVC 应用程序，而不理解其中的细节。我按要求做了:胖模特，瘦控制器。不要在你的观点中加入逻辑。我不明白的是如何创建一个内聚的应用程序结构，使我能够以可维护的代码来表达我的业务想法，也不明白如何真正将我的关注点分离到紧密的层中，而不会将低级逻辑泄漏到更高层中。我听说过可靠的原则，但是将它们应用到 web 应用程序中是一个谜。

在本系列中，我们将使用这些概念构建一个测验应用程序。我们将把应用程序分成不同的层，允许我们替换组件:例如，从 MongoDB 切换到 MySQL，或者从 web 界面切换到命令行界面将是轻而易举的事情。

## 为什么 MVC 是不够的:

MVC 是模型-视图-控制器的缩写，是一种强大的 web 应用程序设计模式。不幸的是，随着[成为流行词汇](https://www.sitepoint.com/mvc-problem-solution/)，它被断章取义，并被用作一种神奇的疗法。使用 MVC 框架已经成为标准实践，许多开发人员已经成功地使用它们来分离显示逻辑和领域逻辑。问题是开发人员止步于此，最好的情况是构建准面向对象的系统，最坏的情况是用类(通常是控制器)包装过程代码。

在构建我们的测验应用程序时，我们将使用在[马丁·福勒的企业应用架构模式](http://martinfowler.com/books/eaa.html)中描述的[域模型](http://martinfowler.com/eaaCatalog/domainModel.html)模式。领域模型只是一种奇特的说法，即我们将使用面向对象的方法来设计系统:一个具有不同职责的对象网络，作为一个整体，将组成我们的应用程序。

领域模型方法使用“实体”对象来表示数据库中的信息；但是我们不是让面向对象的代码模拟数据库，而是让数据库模拟我们的面向对象设计。为什么？因为它允许我们构建良好的面向对象代码。这种映射称为对象关系映射，是一个很大的主题，超出了本文的范围。幸运的是，PHP 中有几个成熟的库可以解决这个问题。其中最成熟的，也是我个人最喜欢的，是[主义](http://www.doctrine-project.org/)。我们将通过手动编写本文所需的特定映射代码来完全回避这个问题。

即使使用域模型模式，仍然存在执行需要多个类协同工作的操作的问题。我们将用服务层模式来解决这个问题。

## 服务层模式:

正确的面向对象设计要求您应该编写解耦的代码。每个班级都应该有一个单独的职责。那么，我们如何组合这些独立的类来执行我们的业务逻辑呢？

服务层模式解决了这个问题。我们将系统的所有操作(注册、购买产品、解答测验)分组为服务类，每个操作或一组密切相关的操作一个服务。我们将这些服务类从它们委托的类中分离出来。这允许我们在不同的用例之间重用服务，比如 web 接口和 CLI 接口、前端和后端接口等等。

## 开始使用:

我们将使用 Slim 作为我们的 MVC 框架。Slim 是一个轻量级的框架，易于学习，非常适合我们简单的应用程序。正如您将在下一篇文章中看到的，当我们编写控制器代码时，您可以很容易地用您喜欢的任何框架替换 Slim。我们将为 Slim 安装 Composer。用下面的`composer.json`文件为项目创建一个目录:

```
{
        "require": {
            "slim/slim": "2.*"
        }
        "autoload": {
            "psr-O": {"QuizApp\\": "./lib/"}
        }
    }
```

## 服务类别编码:

我们需要一个服务来处理测验流:选择测验，检查用户的答案，等等。该服务将包含应用程序的大部分业务逻辑。剩下的代码将解决更多技术上的具体问题，比如访问数据库。

让我们为服务定义一个接口。创建一个包含以下内容的文件`lib/QuizApp/service/QuizInterface.php`:

```
<?php

namespace QuizApp\Service;

interface QuizInterface
{
    /** @return Quiz[] */
    public function showAllQuizes();

    public function startQuiz($quizOrId);

    /** @return Question */
    public function getQuestion();

    /** @return bool */
    public function checkSolution($id);

    /** @return bool */
    public function isOver();

    /** @return Result */
    public function getResult();
}
```

大多数操作应该是不言自明的，但是`getQuestion()`和`getResult()`可能不是那么清楚。`getQuestion()`返回下一个问题供用户回答。`getResult()`返回一个对象，该对象包含正确答案和错误答案的数量，以及用户是否通过了测验。

在我们实现这个服务之前，我们应该定义映射器接口，因为服务将需要使用它。该服务需要两个操作:`find()`返回一个 ID 测验，和`findAll()`。

```
<?php

    namespace QuizApp\Mapper;

    interface QuizInterface
    {
        /** @return \QuizApp\Entity\Quiz[] */
        public function findAll();

        /**
         * @param int $i
         * @return \QuizApp\Entity\Quiz
         */
        public function find($i);
    }
```

这些操作返回类`\QuizApp\Entity\Quiz`的对象，表示单个测验。反过来，`Quiz`类包含了代表测验问题的`\QuizApp\Entity\Question`对象。让我们在返回服务之前实现这些。

```
<?php

    namespace QuizApp\Entity;

    class Question 
    {
        private $id;
        private $questions;
        private $solutions;
        private $correctIndex;

        /**
        * @param string $question
        * @param string[] $solutions
        * @param int $correctSolutionIndex
        */
        public function __construct ($question, array $solutions, $correctSolutionIndex)
        {
            $this->question = $question;
            $this->solutions = $solutions;
            $this->correctIndex = $correctSolutionIndex;
            if (!isset($this->solutions[$this->correctIndex])) {
                throw new \InvalidArgumentException('Invalid index');
            }
        }

        public function setId($id)
        {
            $this->id = $id;
        }

        public function getId()
        {
            return $this->id;
        }

        public function getQuestion()
        {
            return $this->question;
        }

        public function getSolutions()
        {
            return $this->solutions;
        }

        public function getCorrectSolution()
        {
            return $this->solutions[$this->correctIndex];
        }

        public function isCorrect($solutionId)
        {
            return $this->correctIndex == $solutionId;
        }
    }
```

注意，除了 getters 和 setters 之外，`\QuizApp\Entity\Question`还有一个方法`isCorrect()`用于检查问题的某个答案是否正确。

和`\QuizApp\Entity\Quiz`类:

```
<?php

    namespace QuizApp\Entity;

    class Quiz
    {
        private $id;
        private $title;
        private $questions;

        /**
        * @param string $title
        * @param Question[] $questions
        */
        public function __construct($title, array $questions)
        {
            $this->title     = $title;
            $this->questions = $questions;
        }

        public function setId($id)
        {
            $this->id = $id;
        }

        public function getId()
        {
            return $this->id;
        }

        public function getTitle()
        {
            return $this->title;
        }

        public function getQuestions()
        {
            return $this->questions;
        }
    }
```

和`\QuizApp\Service\Quiz\Result`类:

```
<?php

    namespace QuizApp\Service\Quiz;

    class Result
    {
        private $correct;
        private $incorrect;
        private $passScore;

        public function __construct($correct, $incorrect, $passScore)
        {
            $this->correct = $correct;
            $this->incorrect = $incorrect;
            $this->passScore = $passScore;
        }

        public function getCorrect()
        {
            return $this->correct;
        }

        public function getIncorrect()
        {
            return $this->incorrect;
        }

        public function getTotal()
        {
            return $this->correct + $this->incorrect;
        }

        public function getPassScore()
        {
            return $this->passScore;
        }

        public function hasPassed()
        {
            return $this->correct >= $this->passScore;
        }
    }
```

## 编写占位符映射器:

我们需要一个具体的`\QuizApp\MapperMapper\MapperInterface`类供服务使用。让我们暂时定义一个虚拟实现，以便在编写访问 MongoDB 数据库的真正映射器之前，我们可以测试代码。虚拟对象使用硬编码的' \QuizApp\Entity\Question '对象从`find($id)`和`findAll()`方法返回。

```
<?php

namespace QuizApp\Mapper;

class Hardcoded implements QuizInterface
{
    private static $MAP = array();

    /** @return \QuizApp\Entity\Quiz[] */
    public function findAll()
    {
        return [ $this->find(0), $this->find(1) ];
    }

    /**
     * @param int $id
     * @return \QuizApp\Entity\Quiz
     */
    public function find($id)
    {
        if (isset (self::$MAP[$id])) {
            return self::$MAP[$id];
        }
        $result = new \QuizApp\Entity\Quiz(
            'Quiz' . $id, [
                new \QuizApp\Entity\Question(
                    'What colour was George Washington\'s white horse?',
                    [ 'White', 'Gray', 'Yellow', 'All of the above' ],
                    0
                ),
                new \QuizApp\Entity\Question(
                    'Who\'s buried in Grant\'s tomb?',
                    [ 'Grant', 'George Washington', 'George Washingtion\'s horse', 'All of the above' ],
                    0
                ),
            ]
        );
        $result->setId($id);
        self::$MAP[$id] = $result;
        return $result;
    }
}
```

该类通过返回几个硬编码的测验对象来实现接口。它使用`$MAP`静态属性作为身份映射，以确保该类每次被调用时都返回相同的对象。

## 结论:

在我们实用 OOP 系列的第一部分中，我们开始开发我们的测验应用程序。我们讨论了 MVC 以及为什么它不是银弹；我们讨论了领域模型和服务层设计模式；我们为我们的测验服务勾画了界面，它将包含用户解答测验背后的逻辑；我们将提问和问题建模为实体；我们还创建了一个虚拟映射器，用于从“数据库”中查找测验，这在第二部分中会派上用场。

敬请期待！下一次我们将充实我们的应用程序，编写服务类，以及一个将连接到 MongoDB 的真正的数据库映射器。一旦我们有了这些，你将会看到编写我们的控制器和视图是多么容易，以及我们优雅的设计是如何保持“M”、“V”和“C”的分离、可维护和可扩展的。你可以在这里找到这部分[的完整源代码。](https://github.com/moteutsch/Practical-OOP/tree/part-1)

## 分享这篇文章