# 理解 Python Decorators，带示例

> 原文：<https://www.sitepoint.com/understanding-python-decorators/>

这篇文章将帮助你理解 Python 编程中装饰器的概念，以及如何最好地使用它们。我们将介绍什么是 Python decorators，它们的语法是什么样子，如何在脚本或框架中识别它们，以及如何自己应用它们。

Python 中的**函数装饰器**只是一个接受另一个函数作为参数的函数，在不改变其结构的情况下扩展被装饰函数的功能。装饰器包装另一个函数，放大它的行为，并返回它。

Python help 中 decorators 的概念让你的代码<abbr title="don't repeat yourself">保持干燥</abbr>。函数装饰器避免了代码库中不必要的重复，因为一些重复的代码可以放在一起成为函数装饰器。随着您在使用 Python 进行开发方面的进步，decorators 可以帮助您进行分析和日志记录。它们对于设置验证和运行时检查也是至关重要的。

在我们进行的过程中，我假设您对 Python 函数和编程有基本的了解，并且您的设备上至少安装了 Python 3.8。

## 深入研究 Python Decorators 之前需要了解的事情

在 Python 中，函数是一级对象，这意味着它们可以接收参数或作为参数传递。要完全掌握装饰者的概念，有几件事你需要了解。

### 函数是一个对象，这意味着它可以被赋给另一个变量

```
def greet():
    print("Hello John")

greet_john = greet
greet_john()
>>>
Hello John
```

永远记住，在 Python 中，一切都是对象。与给变量赋值的方式相同，函数也可以在必要时给变量赋值。当你学习装饰师时，这很重要。

### 一个函数可以从另一个函数返回

```
def greet():
    def greeting_at_dawn():
        print("Good morning")

    return greeting_at_dawn

salute = greet()
salute()
>>>
Good morning
```

Python 中的内部函数可以从外部函数返回。这是您将会遇到的函数式编程概念的一部分。

### 一个函数可以作为另一个函数的参数传递

```
def greet_some(func):
    print("Good morning", end=' ')
    func()

def say_name():
    print("John")

greet(say_name)
>>>
Good morning John
```

接收函数自变量的函数被称为**高阶函数**。

当学习实现 decorators 并在 Python 程序中有效地使用它们时，记住上面列出的几点很重要。

## Python 装饰器如何工作

一个简单的装饰函数从函数定义、装饰函数开始，然后是外部包装函数中的嵌套函数。

在定义装饰者时，请始终记住这两个要点:

1.  要实现 decorators，请定义一个接受函数参数的外部函数。
2.  在外部装饰函数中嵌套一个包装函数，它也包装被装饰的函数。

下面的代码片段显示了最基本的装饰函数:

```
def increase_number(func):
    def increase_by_one():
        print("incrementing number by 1 ...")
        number_plus_one = func()  + 1
        return number_plus_one
    return increase_by_one 

def get_number():
    return 5

get_new_number = increase_number(get_number)
print(get_new_number())
>>>
incrementing number by 1 ...
6
```

看看上面的代码，外部函数`increase_number`——也称为装饰器——接收一个函数参数`func`。`increase_by_one`是包装函数，在这里可以找到修饰过的`get_number`函数。装饰器被分配给另一个变量。这是使用 Python decorators 时 decorator 语法的样子。然而，有一种更简单的方法来表示装饰者。

当一个简单的装饰函数以前缀`@`开始，再加上下面的装饰函数时，它很容易被识别。前面的示例可以重构为如下所示:

```
def increase_number(func):
    def increase_by_one():
        print("incrementing number by 1 ...")
        number_plus_one = func()  + 1
        return number_plus_one
    return increase_by_one 

@increase_number    
def get_number():
    return 5

print(get_number())
>>>
incrementing number by 1 ...
6
```

示例显示了装饰器扩展了其函数参数的功能。

## 带参数的装饰函数

有些情况下，您可能需要向装饰者传递参数。解决这个问题的方法是将参数传递给包装函数，然后将参数传递给修饰函数。请参见以下示例:

```
 def multiply_numbers(func):
    def multiply_two_numbers(num1, num2):
        print("we're multiplying two number {} and {}".format(num1, num2))
        return func(num1, num2)

    return multiply_two_numbers

@multiply_numbers
def multiply_two_given_numbers(num1, num2):
    return f'{num1} * {num2} = {num1 * num2}'

print(multiply_two_given_numbers(3, 4))
 >>>
we're multiplying two number 3 and 4
3 * 4 = 12
```

将参数传递给内部函数或嵌套函数使它更加强大和健壮，因为它为操作修饰函数提供了更大的灵活性。可以将任意数量的参数(`*args`)或关键字参数(`**kwargs`)传递给修饰函数。`*args`允许收集所有位置参数，而`**kwargs`用于函数调用过程中需要的所有关键字参数。让我们看另一个简单的例子:

```
def decorator_func(decorated_func):
    def wrapper_func(*args, **kwargs):
        print(f'there are {len(args)} positional arguments and {len(kwargs)} keyword arguments')
        return decorated_func(*args, **kwargs)

    return wrapper_func

@decorator_func
def names_and_age(age1, age2, name1='Ben', name2='Harry'):
    return f'{name1} is {age1} yrs old and {name2} is {age2} yrs old'

print(names_and_age(12, 15, name1="Lily", name2="Ola"))
>>>
There are 2 positional arguments and 2 keyword arguments
Lily is 12 yrs old and Ola is 15 yrs old
```

在上面的例子中，`*args`以元组的形式形成了位置参数的 iterable，而`**kwargs`形成了关键字参数的字典。

## Python 中的多个装饰器或装饰器链

在 Python 项目中使用函数装饰器时，有几个选项可供选择。另一个用例是将装饰者(两个或更多)链接到一个函数。一个函数可以用不止一个装饰器来装饰(多个装饰器)，这是通过将一个装饰器堆叠在另一个装饰器上来实现的，没有特定的顺序。无论多个 decorators 放置在彼此之上的顺序如何，您都将得到相同的输出，如下例所示:

```
def increase_decorator(func):
    def increase_by_two():
        print('Increasing number by 2')
        new_number = func()
        return new_number + 2

    return increase_by_two

def decrease_decorator(func):
    def decrease_by_one():
        print('Decreasing number by 1')
        new_number = func()
        return new_number - 1

    return decrease_by_one

@increase_decorator
@decrease_decorator
def get_number():
    return 5

print(get_number())
>>> 
Increasing number by 2
Decreasing number by 1
6
```

## Python 装饰者的真实用例

在 Python 中使用 decorators 的一种非常流行的方式是作为时间记录器。这有助于程序员了解一个函数执行所花费的时间，以此来衡量效率。

记忆化是在 Python 中使用装饰器的另一种很酷的方式。当以后执行计算时，可以很容易地记住没有任何变化的重复函数调用的结果。你可以简单地用装饰器来记忆一个函数。

像`@classmethod`(类方法)`@staticmethod`(静态方法)`@property`这样的内置 Python 装饰器在 Python 的 OOP 装饰器模式中非常流行。

## 结论

Python 装饰者执行软件工程的 DRY 原则，因为它们是可重用的代码。想想许多可以重构为装饰器的 Python 函数。在本文中，我们探索了不同形式的装饰器。还有职业装饰者，虽然我们在这里没有提到。

Decorators 使向一个简单的函数、方法或类添加额外的功能变得更加容易，而不必修改它的源代码，同时保持代码干燥。尝试自己装饰函数，以便更好地理解装饰器模式。

## 分享这篇文章