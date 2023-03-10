# Python Lambda 函数指南，带示例

> 原文：<https://www.sitepoint.com/python-lambda-functions/>

本文介绍了 Python lambda 函数以及如何编写和使用它们。

尽管 Python 是一种面向对象的编程语言，但是当你进行各种函数式编程时，lambda 函数是很方便的。

注意:本文假设你已经理解了 Python 编程以及如何使用正则函数。还假设您的设备上安装了 Python 3.8 或更高版本。

## 解释 Python Lambda 函数

在 Python 中，函数可以接受一个或多个位置参数或关键字参数、变量参数列表、变量关键字参数列表等等。它们可以被传递给一个高阶函数并作为输出返回。正则函数可以有多个表达式和多个语句。他们也总是有一个名字。

Python **lambda 函数**就是一个简单的匿名函数。也可以称之为<q>无名</q>函数。普通的 Python 函数由关键字`def`定义。Python 中的 Lambda 函数通常由`lambda`关键字、任意数量的参数和一个表达式组成。

*注意:术语**λ函数**、**λ表达式**和**λ形式**可以互换使用，这取决于编程语言或程序员。*

Lambda 函数通常用作一行程序。它们经常在高阶函数中使用，如`map()`和`filter()`。这是因为匿名函数作为参数传递给高阶函数，这不仅仅是在 Python 编程中完成的。

lambda 函数对于处理 Python 中的列表理解也非常有用——为此，有多种使用 Python lambda 表达式的选项。

Lambdas 在用于 UI 框架中的条件渲染时非常棒，比如 [Tkinter](https://docs.python.org/3.8/library/tkinter.html) 、 [wxPython](https://www.wxpython.org/) 、 [Kivy](https://kivy.org/) 等。尽管 Python GUI 框架的工作原理不在本文讨论范围之内，但是一些代码片段揭示了 lambda 函数的大量使用，以根据用户的交互来呈现 UI。

## 深入研究 Python Lambda 函数之前需要了解的事情

因为 Python 是面向对象的编程语言，所以一切都是对象。Python 类、类实例、模块和函数都作为对象处理。

一个函数对象可以赋给一个变量。

在 Python 中，将变量赋给常规函数并不罕见。这种行为也适用于 lambda 函数。这是因为它们是函数对象，尽管它们是无名的:

```
def greet(name):
    return f'Hello {name}'

greetings = greet
greetings('Clint')
>>>>
Hello Clint
```

### 高阶函数，如 map()、filter()和 reduce()

很可能你需要在内置函数中使用 lambda 函数，比如`filter()`和`map()`，还有`reduce()`——它是从 Python 中的 functools 模块导入的，因为它不是内置函数。默认情况下，**高阶函数**是接收其他函数作为参数的函数。

如下面的代码示例所示，普通函数可以替换为 lambdas，作为参数传递给这些高阶函数中的任何一个:

```
#map function
names = ['Clint', 'Lisa', 'Asake', 'Ada']

greet_all = list(map(greet, names))
print(greet_all)
>>>>
['Hello Clint', 'Hello Lisa', 'Hello Asake', 'Hello Ada']
```

```
#filter function
numbers = [11, 12, 13, 14, 15, 16, 17, 18, 19, 20]
def multiples_of_three(x):
        return x % 3 == 0

print(list(filter(multiples_of_three, numbers)))
>>>>
[12, 15, 18]
```

```
#reduce function
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
def add_numbers(x, y):
        return x * y

print(reduce(add_numbers, numbers))
>>>>
55
```

### 语句和表达式的区别

开发人员的一个常见困惑是区分编程中的语句和表达式。

一个**语句**是做某事或执行动作的任何一段代码——比如`if`或`while`条件。

一个**表达式**由变量、值和运算符组合而成，并计算出一个新值。

当我们在 Python 中探索 lambda 函数的主题时，这种区别很重要。如下所示的表达式返回值:

```
square_of_three = 3 ** 2
print(square_of_three)
>>>>
9
```

一个语句看起来像这样:

```
for i in range(len(numbers), 0, -1):
        if i % 2 == 1:
                print(i)
        else:
                print('even')
>>>>
even 9 even 7 even 5 even 3 even 1
```

## 如何使用 Python Lambda 函数

Python 风格指南规定每个 lambda 函数都必须以关键字`lambda`开头(不像普通函数以`def`关键字开头)。lambda 函数的语法通常是这样的:

```
lambda arguments : expression
```

Lambda 函数可以接受任意数量的位置参数和/或关键字参数，后跟一个冒号和一个表达式。不能有一个以上的表达式，因为它受到语法限制。下面让我们来看一个 lambda 表达式的例子:

```
add_number = lambda x, y : x + y
print(add_number(10, 4))
>>>>
14
```

在上面的例子中，lambda 表达式被分配给变量`add_number`。通过传递参数进行函数调用，其计算结果为 14。

下面我们再举一个例子:

```
discounted_price = lambda price, discount = 0.1, vat = 0.02 : price * (1 - discount) * (1 + vat)

print(discounted_price(1000, vat=0.04, discount=0.3))
>>>>
728.0
```

如上所示，lambda 函数的计算结果为 728.0。Python lambda 函数中使用了位置参数和关键字参数的组合。当使用位置参数时，我们不能改变函数定义中的顺序。但是，我们可以将关键字参数放在位置参数之后的任何位置。

Lambda 函数总是像 JavaScript 中的[立即调用函数表达式](https://www.sitepoint.com/demystifying-javascript-closures-callbacks-iifes/#iife) (IIFEs)一样被执行。这通常与 Python 解释器一起使用，如下例所示:

```
print((lambda x, y: x - y)(45, 18))
>>>>
27
```

lambda 函数对象包含在括号中，另一对括号紧跟在传递的参数之后。作为一个生命，表达式被求值，函数返回一个赋给变量的值。

Python lambda 函数也可以在列表理解中执行。列表理解总是有一个输出表达式，它被 lambda 函数替换。以下是一些例子:

```
my_list = [(lambda x: x * 2)(x) for x in range(10) if x % 2 == 0]
print(my_list)
>>>>
[0, 4, 8, 12, 16]
```

```
value = [(lambda x: x % 2 and 'odd' or 'even')(x) for x in my_list] 
print(value)
>>>>
['even', 'even', 'even', 'even', 'even']
```

用 Python 写三元表达式时可以用 Lambda 函数。**三元表达式**根据给定条件输出结果。看看下面的例子:

```
test_condition1 = lambda x: x / 5 if x > 10 else x + 5
print(test_condition1(9))
>>>>
14
```

```
test_condition2 = lambda x: f'{x} is even' if x % 2 == 0 else (lambda x: f'{x} is odd')(x)

print(test_condition2(9))
>>>>
9 is odd
```

### 高阶函数中的λ函数

高阶函数的概念在 Python 中很流行，就像在其他语言中一样。它们是接受其他函数作为参数并返回函数作为输出的函数。

在 Python 中，高阶函数有两个参数:一个函数和一个 iterable。函数参数应用于 iterable 对象中的每一项。因为我们可以将一个函数作为参数传递给一个高阶函数，所以我们同样可以传递一个 lambda 函数。

以下是与`map()`函数一起使用的 lambda 函数的一些示例:

```
square_of_numbers = list(map(lambda x: x ** 2, range(10)))

print(square_of_numbers)
>>>>
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

```
strings = ['Nigeria', 'Ghana', 'Niger', 'Kenya', 'Ethiopia', 'South Africa', 'Tanzania', 'Egypt', 'Morocco', 'Uganda']

length_of_strings = list(map(lambda x: len(x), strings))

print(length_of_strings)
>>>>
[7, 5, 5, 5, 8, 12, 8, 5, 7, 6]
```

以下是一些与`filter()`函数一起使用的 lambda 函数:

```
length_of_strings_above_five = list(filter(lambda x: len(x) > 5, strings))

print(length_of_strings_above_five)
>>>>
['Nigeria', 'Ethiopia', 'South Africa', 'Tanzania', 'Morocco', 'Uganda']
```

```
fruits_numbers_alphanumerics = ['apple', '123', 'python3', '4567', 'mango', 'orange', 'web3', 'banana', '890']

fruits = list(filter(lambda x: x.isalpha(), fruits_numbers_alphanumerics))

numbers = list(filter(lambda x: x.isnumeric(), fruits_numbers_alphanumerics))

print(fruits)
print(numbers)
>>>>
['apple', 'mango', 'orange', 'banana']
['123', '4567', '890']
```

以下是一些与`reduce()`函数一起使用的 lambda 函数:

```
values = [13, 6, 12, 23, 15, 31, 16, 21]
max_value = reduce(lambda x,y: x if (x > y) else y, values)
print(max_value)
>>>>
31
```

```
nums = [1, 2, 3, 4, 5, 6]
multiplication_of_nums = reduce(lambda x,y: x*y, nums)

print(multiplication_of_nums)
>>>>
720
```

## 结论

尽管 Python lambdas 可以显著减少您编写的代码行数，但应该谨慎使用它们，仅在必要时使用。代码的可读性应该优先于简洁性。为了提高代码的可读性，正如 [Python 风格指南](https://peps.python.org/pep-0008/#a-foolish-consistency-is-the-hobgoblin-of-little-minds)所推荐的，在适合 lambda 函数的地方，总是使用普通函数。

Lambdas 可以非常方便地使用 Python 三元表达式，但是同样，尽量不要牺牲可读性。当使用高阶函数时，Lambda 函数真正发挥了作用。

总而言之:

*   Python lambdas 适合编写单行函数。
*   它们也用于 IIFEs(立即调用函数表达式)。
*   当有多个表达式时，不应该使用 Lambdas，因为它会使代码不可读。
*   Python 是一种面向对象的编程语言，但 lambdas 是探索 Python 中函数式编程的好方法。

**相关内容:**

*   [了解 Python Decorators，带示例](https://www.sitepoint.com/understanding-python-decorators/)
*   课程: *[用 Python 学习编程基础](https://www.sitepoint.com/premium/courses/wrapping-your-head-around-python-2916)*
*   [Python 的趋势:当今最热门的语言中最热门的是什么](https://www.sitepoint.com/python-trends-whats-hot/)
*   [Python 初学者面临的 5 个常见问题](https://www.sitepoint.com/5-common-problems-faced-by-python-beginners/)
*   [四个程序员如何得到他们的第一份 Python 工作](https://www.sitepoint.com/how-programmers-got-first-python-jobs/)

## 分享这篇文章