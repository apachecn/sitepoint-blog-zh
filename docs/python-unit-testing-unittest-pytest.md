# 用 unittest 和 pytest 介绍 Python 单元测试

> 原文：<https://www.sitepoint.com/python-unit-testing-unittest-pytest/>

**在这篇文章中，我们将看看什么是软件测试，以及为什么你应该关心它。我们将学习如何设计单元测试以及如何编写 Python 单元测试。特别是，我们将看看 Python 中最常用的两个单元测试框架，`unittest`和`pytest`。**

## 软件测试介绍

软件测试是检查软件产品行为的过程，以评估和验证它是否符合规范。软件产品可以有数千行代码，以及数百个协同工作的组件。如果某一行不能正常工作，bug 就会传播并导致其他错误。所以，为了确保一个程序按照它应该的那样运行，它必须被测试。

由于现代软件可能相当复杂，因此有多个级别的测试来评估正确性的不同方面。正如 [ISTQB 认证考试基础等级大纲](https://istqb-main-web-prod.s3.amazonaws.com/media/documents/ISTQB-CTFL_Syllabus_2018_v3.1.1.pdf)所述，软件测试有四个等级:

1.  **单元测试**，测试特定的代码行
2.  **集成测试**，测试多个单元之间的集成
3.  **系统测试**，测试整个系统
4.  **验收测试**，检查是否符合业务目标

在本文中，我们将讨论单元测试，但是在深入探讨之前，我想介绍一下软件测试中的一个重要原则。

> 测试显示缺陷的存在，而不是不存在。
> 
> — <cite>[ISTQB CTFL 教学大纲 2018](https://istqb-main-web-prod.s3.amazonaws.com/media/documents/ISTQB-CTFL_Syllabus_2018_v3.1.1.pdf)</cite>

换句话说，即使你运行的所有测试都没有显示任何失败，这也不能证明你的软件系统没有 bug，或者另一个测试用例不会在你的软件的行为中发现缺陷。

### 什么是单元测试？

这是第一级测试，也称为**组件测试**。在这一部分，测试单个软件组件。根据编程语言的不同，软件单元可能是一个类、一个函数或一个方法。例如，如果你有一个名为`ArithmeticOperations`的 Java 类，它有`multiply`和`divide`方法，那么`ArithmeticOperations`类的单元测试将需要测试`multiply`和`divide`方法的正确行为。

单元测试通常由软件测试人员执行。为了运行单元测试，软件测试人员(或开发人员)需要访问源代码，因为源代码本身就是被测试的对象。因此，这种直接测试源代码的软件测试方法被称为**白盒测试**。

您可能想知道为什么您应该担心软件测试，以及它是否值得。在下一部分，我们将分析测试你的软件系统背后的动机。

### 为什么您应该进行单元测试

软件测试的主要优点是它提高了软件质量。软件质量是至关重要的，尤其是在这个软件处理我们日常活动的世界里。提高软件质量仍然是一个非常模糊的目标。让我们试着更好地定义我们所说的软件质量。根据 ISO/IEC 标准 9126-1 [ISO 9126](https://en.wikipedia.org/wiki/ISO/IEC_9126) ，软件质量包括以下因素:

*   可靠性
*   功能
*   效率
*   可用性
*   可维护性
*   轻便

如果你拥有一家公司，软件测试是一项你应该仔细考虑的活动，因为它会对你的业务产生影响。例如，2022 年 5 月，由于车辆信息娱乐系统的问题，特斯拉[召回了 13 万辆汽车](https://www.theverge.com/2022/5/10/23065987/tesla-recall-130000-vehicles-fix-touchscreen-issues-caused-overheating-cpu-amd-ryzen)。该问题随后通过“空中下载”分发的软件更新得以解决。这些故障耗费了公司的时间和金钱，也给客户带来了问题，因为他们有一段时间不能使用他们的汽车。测试软件确实要花钱，但公司能在技术支持上节省数百万也是事实。

**单元测试**关注于检查软件是否正确运行，这意味着检查输入和输出之间的映射是否都正确完成。作为一种低级别的测试活动，单元测试有助于早期识别错误，这样它们就不会传播到软件系统的更高级别。

单元测试的其他优势包括:

*   **简化集成**:通过确保所有组件单独工作良好，解决集成问题变得更加容易。
*   **最小化代码回归**:有了大量的测试用例，如果将来对源代码的一些修改会导致问题，那么定位问题就更容易了。
*   **提供文档**:通过测试输入和输出之间的正确映射，单元测试提供了关于测试中的方法或类如何工作的文档。

## 设计测试策略

现在让我们看看如何设计一个测试策略。

### 测试范围的定义

在开始计划测试策略之前，有一个重要的问题需要回答。你想测试你的软件系统的哪些部分？

这是一个至关重要的问题，因为详尽的测试是不可能的。由于这个原因，您不能测试每一个可能的输入和输出，但是您应该根据所涉及的风险来确定测试的优先级。

在定义您的测试范围时，需要考虑许多因素:

*   风险:如果一个 bug 影响了这个组件，会有什么样的商业后果？
*   时间:你希望你的软件产品多久能准备好？你有最后期限吗？
*   预算:你愿意在测试活动中投入多少钱？

一旦您定义了测试范围，它指定了您应该测试什么和不应该测试什么，您就可以谈论一个好的单元测试应该具有的品质了。

### 单元测试的质量

*   **快**。单元测试大多是自动执行的，这意味着它们必须很快。缓慢的单元测试更有可能被开发人员跳过，因为它们不提供即时的反馈。
*   **孤立的**。根据定义，单元测试是独立的。它们测试单个的代码单元，并且不依赖于任何外部的东西(比如文件或者网络资源)。
*   **可重复**。单元测试被重复执行，并且结果必须随时间保持一致。
*   **可靠**。单元测试只有在被测试的系统中存在缺陷时才会失败。环境或者测试的执行顺序都不重要。
*   **名正言顺**。测试的名称应该提供测试本身的相关信息。

在深入研究 Python 中的单元测试之前，还缺少最后一步。我们如何组织我们的测试，使它们简洁易读？我们使用一种叫做排列、动作和断言(AAA)的模式。

### AAA 模式

**安排、行动和断言**模式是用于编写和组织单元测试的常用策略。它的工作方式如下:

*   在**排列**阶段，测试所需的所有对象和变量都已设置好。
*   接下来，在 **Act** 阶段，被测试的函数/方法/类被调用。
*   最后，在**断言**阶段，我们验证测试的结果。

这种策略通过分离测试的所有主要部分:设置、执行和验证，提供了一种组织单元测试的干净方法。另外，单元测试更容易阅读，因为它们都遵循相同的结构。

## Python 中的单元测试:unittest 还是 pytest？

我们现在将讨论 Python 中两种不同的单元测试框架。这两个框架是`unittest`和`pytest`。

### 单元测试简介

Python 标准库包括 [unittest](https://docs.python.org/3/library/unittest.html) 单元测试框架。这个框架受到 JUnit 的启发，JUnit 是 Java 中的一个单元测试框架。

如官方文档中所述，`unittest`支持我们将在本文中提到的几个重要概念:

*   **测试用例**，测试的单一单元
*   **测试套件**，是一组一起执行的测试用例
*   **测试运行器**，它是处理所有测试用例的执行和结果的组件

写测试自有它的方法。特别是，我们需要:

1.  将我们的测试写成一个子类`unittest.TestCase`的方法
2.  使用特殊的断言方法

既然已经安装了`unittest`,我们就可以开始编写我们的第一个单元测试了！

### 使用 unittest 编写单元测试

假设我们有一个`BankAccount`类:

```
import unittest

class BankAccount:
  def __init__(self, id):
    self.id = id
    self.balance = 0

  def withdraw(self, amount):
    if self.balance >= amount:
      self.balance -= amount
      return True
    return False

  def deposit(self, amount):
    self.balance += amount
    return True 
```

我们不能提取比存款额更多的钱，所以让我们测试一下我们的源代码是否正确处理了这个场景。

在同一个 Python 文件中，我们可以添加以下代码:

```
class TestBankOperations(unittest.TestCase):
    def test_insufficient_deposit(self):
      # Arrange
      a = BankAccount(1)
      a.deposit(100)
      # Act
      outcome = a.withdraw(200)
      # Assert
      self.assertFalse(outcome) 
```

我们正在创建一个名为`TestBankOperations`的类，它是`unittest.TestCase`的子类。这样，我们就创建了一个新的测试用例。

在这个类中，我们用一个以`test`开始的方法定义了一个测试函数。这很重要，因为每个测试方法都必须以单词`test`开始。

我们期望这个测试方法返回`False`，这意味着操作失败。为了断言结果，我们使用一种叫做`assertFalse()`的特殊断言方法。

我们准备好进行测试了。让我们在命令行上运行这个命令:

```
python -m unittest example.py 
```

这里，`example.py`是包含所有源代码的文件的名称。输出应该如下所示:

```
.
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK 
```

很好！这意味着我们的测试是成功的。现在让我们看看出现故障时的输出是什么样子。我们给之前的课程增加了一个新的测试。让我们试着存一笔负的钱，这当然是不可能的。我们的代码能处理这种情况吗？

这是我们新的测试方法:

```
 def test_negative_deposit(self):
    # Arrange
    a = BankAccount(1)
    # Act
    outcome = a.deposit(-100)
    # Assert
    self.assertFalse(outcome) 
```

我们可以使用`unittest`的详细模式，通过放置`-v`标志来执行这个测试:

```
python -m unittest -v example.py 
```

现在输出是不同的:

```
test_insufficient_deposit (example.TestBankOperations) ... ok
test_negative_deposit (example.TestBankOperations) ... FAIL

======================================================================
FAIL: test_negative_deposit (example.TestBankOperations)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "example.py", line 35, in test_negative_deposit
    self.assertFalse(outcome)
AssertionError: True is not false

----------------------------------------------------------------------
Ran 2 tests in 0.002s

FAILED (failures=1) 
```

在这种情况下，verbose 标志为我们提供了更多信息。我们知道`test_negative_deposit`失败了。特别是，`AssertionError`告诉我们预期的结果应该是`false`，而不是`True is not false`，这意味着该方法返回了`True`。

基于我们的需求,`unittest`框架提供了不同的断言方法:

*   `assertEqual(x,y)`，测试`x == y`
*   `assertRaises(exception_type)`，检查是否出现特定异常
*   `assertIsNone(x)`，测试是否`x is None`
*   `assertIn(x,y)`，测试是否`x in y`

既然我们已经对如何使用`unittest`框架编写单元测试有了基本的了解，那么让我们来看看另一个叫做`pytest`的 Python 框架。

### pytest 简介

`pytest`框架是一个 Python 单元测试框架，它有一些相关的特性:

*   它允许使用较少的代码进行复杂的测试
*   它支持`unittest`测试套件
*   它提供了 800 多个外部插件

由于默认情况下没有安装`pytest`，我们必须先安装它。注意`pytest`需要 [Python 3.7+](https://docs.pytest.org/en/7.1.x/) 。

### 安装 pytest

安装`pytest`相当容易。您只需运行以下命令:

```
pip install -U pytest 
```

然后，通过键入以下命令，检查是否已经正确安装了所有东西:

```
pytest --version 
```

输出应该如下所示:

```
pytest 7.1.2 
```

很好！让我们使用`pytest`编写第一个测试。

### 使用 pytest 编写单元测试

我们将使用之前编写的`BankAccount`类，我们将测试与之前相同的方法。这样，比较使用两种框架编写测试所需的工作量就更容易了。

要使用`pytest`进行测试，我们需要:

*   创建一个目录并将我们的测试文件放入其中。
*   在文件名以`test_`开头或以`_test.py`结尾的文件中编写我们的测试。`pytest`将在当前目录及其子目录中查找那些文件。

因此，我们创建了一个名为`test_bank.py`的文件，并将它放入一个文件夹中。这是我们第一个测试函数的样子:

```
def test_insufficient_deposit():
  # Arrange
  a = BankAccount(1)
  a.deposit(100)
  # Act
  outcome = a.withdraw(200)
  # Assert
  assert outcome == False 
```

正如您已经注意到的，相对于`unittest`版本唯一改变的是断言部分。这里我们使用普通的 Python 断言方法。

现在我们可以看看`test_bank.py`文件:

```
class BankAccount:
  def __init__(self, id):
    self.id = id
    self.balance = 0

  def withdraw(self, amount):
    if self.balance >= amount:
      self.balance -= amount
      return True
    return False

  def deposit(self, amount):
    self.balance += amount
    return True

def test_insufficient_deposit():
  # Arrange
  a = BankAccount(1)
  a.deposit(100)
  # Act
  outcome = a.withdraw(200)
  # Assert
  assert outcome == False 
```

为了运行这个测试，让我们在`test_bank.py`文件所在的文件夹中打开一个命令提示符。然后，运行以下命令:

```
pytest 
```

输出将是这样的:

```
======== test session starts ======== 
platform win32 -- Python 3.7.11, pytest-7.1.2, pluggy-0.13.1
rootdir: \folder
plugins: anyio-2.2.0
collected 1 item

test_bank.py .                                                                                                   [100%]

======== 1 passed in 0.02s ======== 
```

在这种情况下，我们可以看到编写和执行一个测试是多么容易。此外，我们可以看到，与`unittest`相比，我们编写了更少的代码。测试的结果也相当容易理解。

让我们继续看一个失败的测试！

我们用之前写过的第二种方法，叫做`test_negative_deposit`。我们重构了断言部分，结果如下:

```
def test_negative_deposit():
  # Arrange
  a = BankAccount(1)
  # Act
  outcome = a.deposit(-100)
  # Assert
  assert outcome == False 
```

我们以与之前相同的方式运行测试，输出应该是这样的:

```
======= test session starts =======
platform win32 -- Python 3.7.11, pytest-7.1.2, pluggy-0.13.1
rootdir: \folder
plugins: anyio-2.2.0
collected 2 items

test_bank.py .F                                                                                                  [100%]

======= FAILURES =======
_____________ test_negative_deposit _____________
    def test_negative_deposit():
      # Arrange
      a = BankAccount(1)
      # Act
      outcome = a.deposit(-100)
      # Assert
>     assert outcome == False
E     assert True == False

test_bank.py:32: AssertionError
======= short test summary info =======
FAILED test_bank.py::test_negative_deposit - assert True == False
======= 1 failed, 1 passed in 0.15s ======= 
```

通过解析输出，我们可以读取`collected 2 items`，这意味着已经执行了两个测试。向下滚动，我们可以看到在测试`test_negative_deposit`方法时发生了故障。特别是，错误发生在评估断言时。另外，报告还说`outcome`变量的值是`True`，所以这意味着`deposit`方法包含一个错误。

由于`pytest`使用默认的 Python 断言关键字，我们可以将得到的任何输出与存储预期结果的另一个变量进行比较。所有这些都不需要使用特殊的断言方法。

## 结论

最后，在本文中，我们介绍了软件测试的基础知识。我们发现了为什么软件测试是必不可少的，为什么每个人都应该测试他们的代码。我们讨论了单元测试，以及如何用 Python 设计和实现简单的单元测试。

我们使用了两个叫做`unittest`和`pytest`的 Python 框架。两者都有有用的特性，它们是 Python 单元测试中最常用的两个框架。

最后，我们看到了两个基本的测试案例，让您了解如何按照 Arrange、Act 和 Assert 模式编写测试。

我希望我已经让你相信了软件测试的重要性。选择一个框架，如`unittest`或`pytest`，并开始测试——因为它值得你付出额外的努力！

**如果你喜欢这篇文章，你可能会发现下面的内容很有用:**

*   Cypress 测试:运行 Web 应用程序测试的指南
*   [如何使用 Jest 测试 React 组件](https://www.sitepoint.com/test-react-components-jest/)
*   [使用木偶师学习端到端测试](https://www.sitepoint.com/puppeteer-end-to-end-testing/)
*   [3 种免提连续测试方法](https://www.sitepoint.com/3-methods-for-hands-free-continuous-testing/)
*   [重新介绍 Jenkins:流水线自动化测试](https://www.sitepoint.com/re-introducing-jenkins-automated-testing-with-pipelines/)

## 分享这篇文章