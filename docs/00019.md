# 理解 Python 正则表达式函数，带示例

> 原文：<https://www.sitepoint.com/python-regex/>

正则表达式(regex)是特殊的字符序列，用于查找或匹配字符串中的模式，正如 regex 的介绍所解释的。我们之前已经展示了如何在 [JavaScript](https://www.sitepoint.com/basic-javascript-regular-expression/) 和 [PHP](https://www.sitepoint.com/regular-expressions-php/) 中使用正则表达式。本文的重点是 Python 正则表达式，目的是帮助您更好地理解如何在 Python 中操作正则表达式。

随着我们讲述处理 Python 正则表达式对象的细微差别，您将学习如何在您的程序中有效地使用 Python 正则表达式函数和方法。

![python regex](img/994dac50cdae7cfcff6d1fdb2d730a86.png)

## Python 中的正则表达式模块:re 和 regex

Python 有两个模块——`re`和`regex`——便于使用正则表达式。`re`模块内置于 Python 中，而`regex`模块由 Matthew Barnett 开发，可在 [PyPI](https://pypi.org/project/regex/) 上获得。Barnett 的`regex`模块是使用内置的`re`模块开发的，两个模块具有相似的功能。它们在实现方面有所不同。内置的`re`模块是两者中更受欢迎的，所以我们将在这里使用那个模块。

## Python 内置的 re 模块

Python 开发人员在执行正则表达式时，通常会使用`re`模块。正则表达式语法的一般结构保持不变(字符和符号)，但是该模块提供了一些函数和方法来有效地在 Python 程序中执行正则表达式。

在使用`re`模块之前，我们必须像其他 Python 模块或库一样将其导入到我们的文件中:

```
import re 
```

这使得模块在当前文件中可用，这样 Python 的 regex 函数和方法就很容易访问。使用`re`模块，我们可以创建 Python 正则表达式对象，操作匹配的对象，并在必要的地方应用标志。

## 环函数精选。

`re`模块有`re.search()`、`re.match()`、`re.compile()`等功能，我们先来讨论一下。

### 重新搜索(模式，字符串，标志=0)与重新匹配(模式，字符串，标志=0)

`re.search()`和`re.match()`在一个字符串中搜索 Python 正则表达式模式，如果找到就返回一个匹配，如果没有找到匹配对象就返回`None`。

这两个函数总是返回给定字符串中找到的第一个匹配的子字符串，并为 flag 维护默认值`0`。但是当`search()`函数扫描整个字符串来寻找匹配时，`match()`只搜索字符串开头的匹配。

Python 的`re.search()`文档:

> 扫描*字符串*，寻找正则表达式*模式*产生匹配的第一个位置，并返回相应的匹配对象。如果字符串中没有与模式匹配的位置，则返回`None`；请注意，这不同于在字符串中的某个位置查找零长度匹配。

Python 的`re.match()`文档:

> 如果*字符串*开头的零个或多个字符与正则表达式*模式*匹配，则返回相应的匹配对象。如果字符串与模式不匹配，则返回`None`；请注意，这不同于零长度匹配。

让我们看一些代码示例来进一步阐明:

```
search_result = [re.search](http://re.search)(r'\d{2}', 'I live at 22 Garden Road, East Legon')

print(search_result)

print(search_result.group())

>>>>

<re.Match object; span=(10, 12), match='22'>

22 
```

```
match_result = re.match(r'\d{2}', 'I live at 22 Garden Road, East Legon')

print(match_result)

print(match_result.group())

>>>>

None

Traceback (most recent call last):

File "/home/ini/Dev./sitepoint/regex.py", line 4, in <module>

print(match_result.group())

AttributeError: 'NoneType' object has no attribute 'group' 
```

在上面的例子中，`None`被返回，因为在字符串的开头没有匹配项。调用`group()`方法时引发了一个`AttributeError`，因为没有匹配对象:

```
match_result = re.match(r'\d{2}', "45 cars were used for the president's convoy")

print(match_result)

print(match_result.group())

>>>>

<re.Match object; span=(0, 2), match='45'>

45 
```

使用字符串开头的 match 对象 45，`match()`方法工作得很好。

### 重新编译(模式，标志=0)

`compile()`函数获取给定的正则表达式模式，并将其编译成正则表达式对象，用于在字符串或文本中查找匹配。它还接受一个`flag`作为可选的第二个参数。这个方法很有用，因为 regex 对象可以赋给一个变量，稍后在 Python 代码中使用。当创建一个 Python 正则表达式对象时，一定要记住使用一个原始字符串`r"..."`。

下面是它如何工作的一个例子:

```
regex_object = re.compile(r'b[ae]t')

mo = regex_object.search('I bet, you would not let a bat be your president')

print(regex_object)

>>>>

re.compile('b[ae]t') 
```

### re.fullmatch(模式，字符串，标志=0)

这个函数有两个参数:一个作为正则表达式模式传递的字符串、一个要搜索的字符串和一个可选的标志参数。如果整个字符串匹配给定的正则表达式模式，则返回一个匹配对象。如果不匹配，它返回`None`:

```
regex_object = re.compile(r'Tech is the future')

mo = regex_object.fullmatch('Tech is the future, join now')

print(mo)

print([mo.group](http://mo.group)())

>>>>

None

Traceback (most recent call last):

File "/home/ini/Dev./sitepoint/regex.py", line 16, in <module>

print([mo.group](http://mo.group)())

AttributeError: 'NoneType' object has no attribute 'group' 
```

代码引发了一个`AttributeError`，因为没有匹配的字符串。

### re.findall(模式，字符串，标志=0)

函数的作用是:返回给定字符串中所有匹配对象的列表。它从左到右遍历字符串，直到返回所有匹配。请参见下面的代码片段:

```
regex_object = re.compile(r'[A-Z]\w+')

mo = regex_object.findall('Pick out all the Words that Begin with a Capital letter')

print(mo)

>>>>

['Pick', 'Words', 'Begin', 'Capital'] 
```

在上面的代码片段中，正则表达式由一个字符类和一个单词字符组成，这确保了匹配的子字符串以大写字母开头。

### re.sub(模式，复制，字符串，计数=0，标志=0)

在`sub()`函数的帮助下，可以用另一个子串替换一个字符串的一部分。它至少需要三个参数:搜索模式、替换字符串和要处理的字符串。如果没有找到匹配项，则原始字符串不变地返回。在不传递 count 参数的情况下，默认情况下，该函数会查找正则表达式的一个或多个匹配项，并替换所有匹配项。

这里有一个例子:

```
regex_object = re.compile(r'disagreed')

mo = regex_object.sub('agreed',"The founder and the CEO disagreed on the company's new direction, the investors disagreed too.")

print(mo)

>>>>

The founder and the CEO agreed on the company's new direction, the investors agreed too. 
```

### subn(模式，复制，字符串，计数=0，标志=0)

`subn()`函数执行与`sub()`相同的操作，但是它返回一个替换完成的字符串和数量的元组。请参见下面的代码片段:

```
regex_object = re.compile(r'disagreed')

mo = regex_object.subn('agreed',"The founder and the CEO disagreed on the company's new direction, the investors disagreed too.")

print(mo)

>>>>

("The founder and the CEO agreed on the company's new direction, the investors agreed too.", 2) 
```

## 匹配对象和方法

当正则表达式模式匹配正则表达式对象的`search()`或`match()`方法中的给定字符串时，返回一个**匹配对象**。在 Python 中操作正则表达式时，Match 对象有几个证明有用的方法。

### Match.group([group1，…])

此方法返回匹配对象的一个或多个子组。单个参数将返回一个信号子组；多个参数将根据它们的索引返回多个子组。默认情况下，`group()`方法返回整个匹配子串。当`group()`中的参数大于或小于子组时，会抛出一个`IndexError`异常。

这里有一个例子:

```
regex_object = re.compile(r'(\+\d{3}) (\d{2} \d{3} \d{4})')

mo = regex_object.search('Pick out the country code from the phone number: +233 54 502 9074')

print([mo.group](http://mo.group)(1))

>>>>

+233 
```

传递给`group(1)`方法的参数`1`——如上面的例子所示——选择加纳`+233`的国家代码。调用不带参数或不带参数`0`的方法将返回匹配对象的所有子组:

```
regex_object = re.compile(r'(\+\d{3}) (\d{2} \d{3} \d{4})')

mo = regex_object.search('Pick out the phone number: +233 54 502 9074')

print([mo.group](http://mo.group)())

>>>>

+233 54 502 9074 
```

### Match.groups(默认值=无)

`groups()`返回与给定字符串匹配的子组元组。正则表达式模式组总是用圆括号— `()` —捕获，当有匹配时，这些组作为元组中的元素返回:

```
regex_object = re.compile(r'(\+\d{3}) (\d{2}) (\d{3}) (\d{4})')

mo = regex_object.search('Pick out the phone number: +233 54 502 9074')

print(mo.groups())

>>>>

('+233', '54', '502', '9074') 
```

### match . start([组])& match . end([组])

`start()`方法返回开始索引，而`end()`方法返回匹配对象的结束索引:

```
regex_object = re.compile(r'\s\w+')

mo = regex_object.search('Match any word after a space')

print('Match begins at', mo.start(), 'and ends', mo.end())

print([mo.group](http://mo.group)())

>>>>

Match begins at 5 and ends 9

any 
```

上面的例子有一个正则表达式模式，用于匹配空格后的任何单词字符。找到匹配项— `' any'` —从位置 5 开始，到位置 9 结束。

### Pattern.search(string[，pos[，endpos]])

`pos`值表示开始搜索匹配对象的索引位置。`endpos`指示匹配搜索应该停止的位置。`pos`和`endpos`的值可以作为参数在字符串后的`search()`或`match()`方法中传递。它是这样工作的:

```
regex_object = re.compile(r'[a-z]+[0-9]')

mo = regex_object.search('find the alphanumeric character python3 in the string', 20 , 30)

print([mo.group](http://mo.group)())

>>>>

python3 
```

上面的代码挑选出搜索字符串中的任何字母数字字符。

搜索从字符串索引位置 20 开始，在 30 处停止。

## 关于正则表达式标志

Python 允许在使用`search()`和`match()`等`re`模块方法时使用**标志**，这为正则表达式提供了更多的上下文。这些标志是可选参数，用于指定 Python 正则表达式引擎如何查找匹配对象。

### 关于。我(回复。IGNORECASE)

执行不区分大小写的匹配时使用此标志。正则表达式引擎将忽略正则表达式模式大写或小写变体:

```
regex_object = [re.search](http://re.search)('django', 'My tech stack comprises of python, Django, MySQL, AWS, React', re.I)

print(regex_object.group())

>>>>

Django 
```

`re.I`确保找到匹配对象，不管它是大写还是小写。

### 关于。s(回复。DOTALL)

`'.'`特殊字符匹配除换行符之外的任何字符。引入这个标志也将匹配文本块或字符串中的换行符。请参见下面的示例:

```
regex_object= [re.search](http://re.search)('.+', 'What is your favourite coffee flavor \nI prefer the Mocha')

print(regex_object.group())

>>>>

What is your favourite coffee flavor 
```

`'.'`字符只从字符串的开头开始寻找匹配，并在换行符处停止。引入`re.DOTALL`标志将匹配一个换行符。请参见下面的示例:

```
regex_object= [re.search](http://re.search)('.+', 'What is your favourite coffee flavor \nI prefer the Mocha', re.S)

print(regex_object.group())

>>>>

What is your favourite coffee flavor

I prefer the Mocha 
```

### 关于。m(回复。多线)

默认情况下，`'^'`特殊字符只匹配字符串的开头。引入该标志后，该函数在每行的开头搜索匹配项。`'$'`字符只匹配字符串末尾的模式。但是`re.M`标志确保它也能在每行的末尾找到匹配项:

```
regex_object = [re.search](http://re.search)('^J\w+', 'Popular programming languages in 2022: \nPython \nJavaScript \nJava \nRust \nRuby', re.M)

print(regex_object.group())

>>>>

JavaScript 
```

### 关于。x(回复。冗长)

有时，Python 正则表达式模式会变得冗长而混乱。当我们需要在正则表达式模式中添加注释时,`re.X`标志会有所帮助。我们可以使用`'''`字符串格式创建一个带有注释的多行正则表达式:

```
email_regex = [re.search](http://re.search)(r'''

[a-zA-Z0-9._%+-]+ # username composed of alphanumeric characters

@ # @ symbol

[a-zA-Z0-9.-]+ # domain name has word characters

(\.[a-zA-Z]{2,4}) # dot-something

''', 'extract the email address in this string [kwekujohnson1@gmail.com](mailto:kwekujohnson1@gmail.com) and send an email', re.X)

print(email_regex.group())

>>>>

[kwekujohnson1@gmail.com](mailto:kwekujohnson1@gmail.com) 
```

## Python 中正则表达式的实际例子

现在让我们深入一些更实际的例子。

### Python 密码强度测试正则表达式

正则表达式最流行的用例之一是测试密码强度。注册任何新账户时，都会进行一项检查，确保我们输入了字母、数字和字符的适当组合，以确保密码安全。

下面是一个用于检查密码强度的示例正则表达式模式:

```
password_regex = re.match(r"""

^(?=.*?[A-Z]) # this ensures user inputs at least one uppercase letter

(?=.*?[a-z]) # this ensures user inputs at least one lowercase letter

(?=.*?[0-9]) # this ensures user inputs at least one digit

(?=.*?[#?!@$%^&*-]) # this ensures user inputs one special character

.{8,}$ #this ensures that password is at least 8 characters long

""", '@Sit3po1nt', re.X)

print('Your password is' ,password_regex.group())

>>>>

Your password is @Sit3po1nt 
```

*注意使用`'^'`和`'$'`来确保输入的字符串(密码)是正则表达式匹配的。*

### 文件正则表达式中的 Python 搜索和替换

这个例子的目标是:

*   创建一个文件“pangram.txt”。
*   向文件中添加简单的一些文本，`"The five boxing wizards climb quickly."`
*   编写一个简单的 Python 正则表达式进行搜索，并将“climb”替换为“jump ”,这样我们就有了一个 pangram。

下面是实现这一点的一些代码:

```
#importing the regex module

import re

file_path="pangram.txt"

text="climb"

subs="jump"

#defining the replace method

def search_and_replace(filePath, text, subs, flags=0):

with open(file_path, "r+") as file:

#read the file contents

file_contents = [file.read](http://file.read)()

text_pattern = re.compile(re.escape(text), flags)

file_contents = text_pattern.sub(subs, file_contents)

[file.seek](http://file.seek)(0)

file.truncate()

file.write(file_contents)

#calling the search_and_replace method

search_and_replace(file_path, text, subs) 
```

### Python web 抓取正则表达式

有时，您可能需要在互联网上收集一些数据，或者自动执行简单的任务，如 web 抓取。在线提取某些数据时，正则表达式非常有用。下面是一个例子:

```
import urllib.request

phone_number_regex = r'\(\d{3}\) \d{3}-\d{4}'

url = 'https://www.summet.com/dmsi/html/codesamples/addresses.html'

# get response

response = urllib.request.urlopen(url)

# convert response to string

string_object = [response.read](http://response.read)().decode("utf8")

# use regex to extract phone numbers

regex_object = re.compile(phone_regex)

mo = regex_object.findall(string_object)

# print top 5 phone numbers

print(mo[: 5])

>>>>

['(257) 563-7401', '(372) 587-2335', '(786) 713-8616', '(793) 151-6230', '(492) 709-6392'] 
```

## 结论

正则表达式可以从简单到复杂。正如上面的例子所示，它们是编程的重要部分。为了更好地理解 Python 中的 regex，最好从熟悉字符类、特殊字符、锚点和分组结构开始。

为了加深我们对 Python 中正则表达式的理解，我们还有很多工作要做。Python `re`模块使快速启动和运行变得更加容易。

Regex 大大减少了我们验证输入和实现搜索算法所需的代码量。

能够回答关于正则表达式使用的问题也很好，因为它们经常出现在软件工程师和开发人员的技术面试中。

## 分享这篇文章