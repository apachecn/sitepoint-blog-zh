# 快速提示:理解 Python 中的 Yield 关键字

> 原文：<https://www.sitepoint.com/quick-tip-understanding-the-yield-keyword-in-python/>

*感谢 [Shaumik Daityari](https://www.sitepoint.com/author/sdaityari/) 好心帮助[同行评审](https://www.sitepoint.com/introduction-to-sitepoints-peer-review/)这篇文章。*

当我们在 Python 中调用一个函数时，该函数通常会开始工作，直到遇到一个`return`、`exception`，或者到达它的结尾——之后，它会将控制权返回给调用者。每当你再次调用那个函数，这个过程将从*开始*！

假设你让一个人跟踪路上的红色汽车。这个人会不断地问他们是否看到了一辆红色的车，然后这个人会回答“是”或者“不是”。如果这个人回答“是”，那么这辆红色汽车被发现的次数就会增加。

让我们看看如何在 Python 中做到这一点:

```
import time

def red_cars(answer):
    n = 0
    while True:
        if answer == 'yes':
            n = n + 1
            return n
        else:
            return n

stop = time.time() + 5 * 60
while time.time() < stop:
    answer = raw_input('Did you spot a red car on the road? ("yes" or "no"): ')
    times = red_cars(answer)
    print 'You have spotted ' + str(times) + ' cars so far!'
```

如果你运行这个程序，你会注意到什么？你是否注意到回答“是”的次数总是以`1`为上限，而当你回答“否”时，无论之前回答“是”与否，回答次数都是`0`？

这就是 Python 的`yield`关键字发挥作用的地方。`yield`是我们*暂时*将控制权交给调用者，并期望从控制权已经移交的点继续下去的一种手段。

在给出上述例子的解决方案之前，让我演示一个非常简单的例子来更好地说明`yield`是如何工作的。

假设我们有以下简单的 Python 脚本:

```
def step_by_step():
    return 'step 1'
    return 'step 2'
    return 'step 3'

step = step_by_step()
for i in range (3):
    print step
```

如果运行该脚本，您将获得以下输出:

```
step 1
step 1
step 1
```

现在，如果我们用`yield`代替，如下:

```
def step_by_step():
    yield 'step 1'
    yield 'step 2'
    yield 'step 3'

step = step_by_step()
for i in range (3):
    print step.next()
```

输出如下所示:

```
 step 1
step 2
step 3 
```

如你所见，我们能够创建一系列*值*，对于每个调用，函数从产生值的点继续。这种功能被称为**发生器**。这样的函数创建了一个**生成器迭代器**，每次调用方法`next()`时，我们都移动到下一个`yield`语句。

如果我们回到我们的主要例子(红色汽车)，它可以被写成如下来执行所需的任务:

```
import time

def red_cars(answer = None):
    n = 0
    while True:
        if answer=="yes":
            n = n + 1
            answer = yield n
        else:
            answer = yield n

car_color = red_cars()
car_color.next()

stop = time.time() + 5 * 60
while time.time() < stop:
    answer = raw_input('Did you spot a red car on the road? ("yes" or "no"): ')
    print 'You have spotted ' + str(car_color.send(answer)) + ' cars so far!'
```

因此，正如我们所看到的，当我们对在函数(生成器)退出的最后一点恢复执行感兴趣时，`yield`被认为是重要的，并且我们也对在不同调用之间保持局部变量的值感兴趣——不像普通函数，当退出函数时这些值被破坏。

然而，`yield`还有其他的用法。例如，如果您有一个返回序列的函数(例如，excel 表中的行),并且您需要在序列上迭代，而不需要一次在内存中保存每个值，那么您可以使用`yield`。也就是节省内存。

在处理 iterables 时也可以使用`yield`,因为我们有一个很大的列表，很难在函数之间传递。例如，Python 在 [itertools 模块](https://docs.python.org/3/library/itertools.html)中内置的用于排列和组合的函数使用了`yield`。

## 分享这篇文章