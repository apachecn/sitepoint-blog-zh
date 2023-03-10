# Python 初学者面临的 5 个常见问题

> 原文：<https://www.sitepoint.com/5-common-problems-faced-by-python-beginners/>

**你正在学习 Python 吗？这是一门很好学的语言，但是和任何语言一样，它有时确实会带来巨大的挑战，尤其是在你自学的时候。**

考虑到 Python 中所有不同的做事方式，我们决定汇编一份初学者经常面临的问题的有用列表——以及它们的解决方案。

![learning Python](img/00054eddad6079434878d72c97e38138.png)

## 1.从终端读取

如果你在终端上运行一个程序，你需要用户输入，你可能需要通过终端本身来获得。(其他替代方法包括读取文件以获取输入。)

在 Python 中， [`raw_input`](https://docs.python.org/2/library/functions.html#raw_input) 函数有助于实现这一点。该函数接受的唯一参数是提示符。让我们看一个小程序来看看结果:

```
name = raw_input('Type your name and press enter: ')
print 'Hi ' + name
```

如果您将这些行保存在一个文件中(扩展名为`.py`)并执行它，您会得到以下结果:

```
Type your name and press enter: Shaumik
Hi Shaumik
```

这里需要注意的一点是，变量是一个字符串，您需要过滤并转换它，以便以不同的形式使用它(比如迭代次数或矩阵长度的输入):

```
name = raw_input('Type your name and press enter: ')
print 'Hi ' + name
num = raw_input('How many times do you want to print your name: ')
for i in range(int(num)):
    print name
```

## 2.在 Python 中枚举

Python 经常呈现出不同于其他流行编程语言如 C++和 Java 的做事方式。当你在其他语言中遍历一个数组时，你从`0`开始增加一个整数，并访问数组中相应的元素。下面展示了一种简单的方法:

```
for (int i = 0; i < array_length; ++i)
    cout << array[i];
```

然而，在 Python 中，您可以简单地遍历数组的每个元素，而完全不使用索引:

```
for item in array:
    print item
```

如果也需要访问索引呢？ [`enumerate`](https://docs.python.org/2/library/functions.html#enumerate) 功能帮你做到。一个数组的枚举(或者一个<q>列表</q>，Python 中称之为)创建数组中的项目对及其索引。同样的情况可以证明如下:

```
>>> x = [10, 11, 12, 13, 14]
>>> for item in enumerate(x):
...     print item
...
(0, 10)
(1, 11)
(2, 12)
(3, 13)
(4, 14)
```

想象一下这样一种情况，您需要打印数组中的每一个替代项。这样做的一种方法如下:

```
>>> for index, item in enumerate(x):
...     if index % 2 == 0:
...         print item
...
10
12
14
```

## 3.通过 Python 执行外部命令

有时，您可能需要在 Python 脚本中执行终端命令。这可以通过 [`subprocess`](https://docs.python.org/2/library/subprocess.html) 模块下的`call`功能来实现。有许多方法可以做到这一点，其中一种如下所示:

```
>>> from subprocess import call
>>> call('cal')
     March 2016
Su Mo Tu We Th Fr Sa
       1  2  3  4  5
 6  7  8  9 10 11 12
13 14 15 16 17 18 19
20 21 22 23 24 25 26
27 28 29 30 31

0
```

输出中的最后一个零表示我们在脚本中创建的子流程正常结束。换句话说，运行该命令没有问题。

如果您需要为命令使用参数，您需要将它们作为列表添加到主命令中。例如，要运行命令`ls -l`，需要完成以下操作:

```
>>> from subprocess import call
>>> call(['ls', '-l'])
total 16
-rw-------@ 1 donny  staff  439 Oct 21 16:06 chess.csv
-rw-r--r--  1 donny  staff   72 Mar  1 17:28 read.py
0
```

为了检查发生错误时`0`会发生什么，我们可以在非 git 存储库中运行 git 命令:

```
>>> from subprocess import call
>>> call(['git', 'status'])
fatal: Not a git repository (or any of the parent directories): .git
128
```

在输出中，第二行是命令的输出，而`128`是退出代码。

## 4.使用异常

Python 是一种解释型语言，这意味着代码是逐行执行的。如果在一行中遇到错误，代码的进一步执行将会停止。然而，您可以使用 try-except 块在 Python 中处理已知的异常。让我们看一个简单的例子，通过生成除以`0`的运行时错误:

```
>>> x = 1/0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: integer division or modulo by zero
```

当解释器到达这一行时，你的程序执行完全停止！但是，使用 try-except 块有助于避免同样的情况。

```
>>> try:
...     x = 1/0
... except:
...     print "Some error occurred"
...
Some error occurred
```

当在 <q>try</q> 块中出现这种错误时，解释器只执行除块之外的<q>。除了</q>块之外的<q>可以通过捕捉单个错误来进一步扩展:</q>

```
>>> try:
...     x = 1/0
... except ZeroDivisionError:
...     print "You tried to divide by zero"
... except:
...     print "Some unknown error occurred"
...
You tried to divide by zero
```

您可以向前一步，捕捉异常并通过修改除了块之外的<q>来进一步处理它(比如在某处记录错误):</q>

```
>>> try:
...     x = 1/0
... except Exception as e:
...     print "Exception occurred: " + str(e)
...
Exception occurred: integer division or modulo by zero
```

## 5.使用模块

在查看其他人的代码时，您会经常遇到这样的代码块:

```
def some_function():
    ...

if __name__ == '__main__':
    ...
```

当您在 Python 中创建模块时，这是经常使用的。理想情况下，最后一个块中的代码通常演示了上述函数的一些基本用法。当您在终端中运行该文件时，会执行以下代码。但是，如果为了使用函数而在不同的文件中使用该模块，则不会执行该块。迷惑？让我们通过一个例子来更好地理解这一点:

```
# File print.py
def print_me():
    print "me"

# demonstrating use of print_me
print_me()
```

现在，如果我导入这个文件以在新文件中使用该函数，请注意会发生什么:

```
>>> import print
me
>>>
```

一旦导入文件，就会执行该函数。为了避免这种情况，我们将该函数放在一个`if __name__ == '__main__':`块下:

```
# File print.py
def print_me():
    print "me"

if __name__ == '__main__':
    # demonstrating use of print_me
    print_me()
```

当我们修改代码时，`print_me()`函数只有在作为文件运行时才被执行，而不是作为模块导入时:

```
>>> import print
>>>
```

## 最后的想法

如果 Python 不是你的第一门编程语言，你需要花一些时间来适应它的做事方式。我推荐你看一个有趣的 YouTube 视频，Raymond Hettinger 解释了这种 Pythonic 式的编程方式。在这篇文章中，我试图涵盖其中的一些 Pythonic 问题。

你的 Pythonic 之旅怎么样了，或者还在继续吗？你喜欢用不同的方式完成这些任务吗？请在下面的评论中告诉我们吧！

## 分享这篇文章