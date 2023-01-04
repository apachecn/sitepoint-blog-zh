# 红宝石变形器

> 原文：<https://www.sitepoint.com/the-ruby-transmorgrifier/>

计算机擅长的事情之一是移动数据。当您必须将数据从一种类型迁移到另一种类型时，我发现 Ruby 让我的工作变得容易多了。不久前，我有一项涉及移动数据的任务。我们得到了几十个数据集，需要将它们从 CSV 格式转换成固定长度的文本文件。这就是我们如何做到的故事。

## 定义完成的输出。

比方说，我们得到了一个定义文件，说明数据需要如何格式化才能导入。假设它看起来像这样。

```
FIELD NAME      FORMAT
NAME            A(50)
ADDRESS1        A(50)
ADDRESS2        A(50)
CITY            A(50)
STATE           A(2)
ZIP             A(10)
CONTACT         A(50)
CONTACTPHONE    A(10)
ACCOUNTOPENED   9(8)
```

这是什么意思？我们真的很注重形式。“a”表示字母数字，右边有空格。“9”表示左边有空格的数字。数字表示字段的长度，因此“A(10)”是一个由 10 个字符组成的字母数字字段。

我们的源数据在一个 CSV 文件中。类似于:

```
name,address1,address2,city,state,zip,contact,contactPhone,accountOpened
Wonder widgets,1600 Vassar Street,,dallas,dallas,tx,75220,Tim Smith,214-555-1212,12052001
Timmy's Bikes,2723 Auburn Street,Building 3,Erie,ERIE,PA,16508-1234,,814-555-4321,03232011
```

输出只需要是一个文本文件。

```
Wonder widgets  1600 Vassar Street             dallas tx75220     Tim Smith 214-555-121220011205
Timmy's Bikes   2723 Auburn Street  Building 3 Erie   PA16508-1234          814-555-432120110323
```

你会怎么做？

## 开始变形的时间到了

你将如何*转换*数据？从目标定义中，我们知道该名称的长度为 50 个字符。您可以编写一个方法来获取*名称*列，并使其长度为 50 个字符。想沿着那条路走下去吗？我们可以以后再考虑。记住你必须传入文本。

```
def nameFixing(name)
  name = name.ljust(50)
end
```

好旧的 [ljust](http://www.ruby-doc.org/core-1.9.3/String.html#method-i-ljust) 会添加我们需要的额外空白。这看起来容易吗？这是红宝石，但也许我们应该测试这个。

```
require 'test/unit'

def nameFixing(name)
  name = name.ljust(50)
end

class NameLengthTest < Test::Unit::TestCase
  def test_name_short_should_equal_50
    name = nameFixing "boo"
    assert_equal(name.length, 50)
  end
end
```

保存文件并继续运行测试。

```
$ ruby ljust_test.rb
Loaded suite ljust_test
Started
.
Finished in 0.000802 seconds.

1 tests, 1 assertions, 0 failures, 0 errors, 0 skips

Test run options: --seed 27164
```

太好了，真的起作用了。

如果长度超过 50 个字符会怎么样？去试试吧。

```
require 'test/unit'

def nameFixing(name)
  name = name.ljust(50)
end

class NameLengthTest < Test::Unit::TestCase
  def test_name_short_should_equal_50
    name = nameFixing "boo"
    assert_equal(name.length, 50)
  end

def test_long_short_should_equal_50
    name = nameFixing "dgrtefdgdfshrtyutyuykhjkmbnmvcbdfgrthjghjgghvdfgrstthg"
    assert_equal(name.length, 50)
  end
end
```

```
$ ruby ljust_test.rb
Loaded suite ljust_test
Started
F.
Finished in 0.001179 seconds.

1) Failure:
test_long_short_should_equal_50(NameLengthTest) [ljust_test.rb:15]:
<54> expected but was
<50>.

2 tests, 2 assertions, 1 failures, 0 errors, 0 skips

Test run options: --seed 40377
```

你预料到了吗？`ljust`添加空白，但不去掉多余的字符。你认为怎样才能去掉多余的字符？你得到了它[片](http://www.ruby-doc.org/core-1.9.3/String.html#method-i-slice)。

如果名称小于 50，我们需要在左侧添加空白。如果名称超过 50，我们需要切掉多余的字符。如果名字有 50 个字符，我们就不去管它。我们刚刚重写了测试吗？

```
require 'test/unit'

def nameFixing(name)
  case
  when name.length 50
    name = name.slice(0..49)
  when name.length < 50
    name = name.ljust(50)
  else
    name
  end
end

class NameLengthTest < Test::Unit::TestCase
  def test_name_short_should_equal_50
    name = nameFixing "boo"
    assert_equal(name.length, 50)
  end

def test_name_long_should_equal_50
    name = nameFixing "dgrtefdgdfshrtyutyuykhjkmbnmvcbdfgrthjghjgghvdfgrstthg"
    assert_equal(name.length, 50)
  end

def test_name_50_should_equal_50
    name = nameFixing "dgrtefdgdfshrtyutyuymbnmvcbdfgrthjghjgghvdfgrstthg"
    assert_equal(name.length, 50)
  end
end
```

你觉得这会过去吗？试试看。

既然您已经有了使名称长度合适的方法，我们需要继续其他列。你注意到定义的模式了吗？字母数字或数字和一定长度。

为什么我们不继续处理柱子的长度。你应该可以传入一个变量来设置它。试试看。

```
require 'test/unit'

def nameFixing(name,len)
  case
  when name.length len
    name = name.slice(0..(len-1))
  when name.length < len
    name = name.ljust(len)
  else
    name
  end
end

class NameLengthTest < Test::Unit::TestCase
  def test_name_short_should_equal_50
    name = nameFixing "boo",50
    assert_equal(name.length, 50)
  end

def test_name_long_should_equal_50
    name = nameFixing "dgrtefdgdfshrtyutyuykhjkmbnmvcbdfgrthjghjgghvdfgrstthg",50
    assert_equal(name.length, 50)
  end

def test_name_50_should_equal_50
    name = nameFixing "dgrtefdgdfshrtyutyuymbnmvcbdfgrthjghjgghvdfgrstthg",50
    assert_equal(name.length, 50)
  end
end
```

现在你也传入了我们需要设置的长度。保存并重新运行测试。记得在毁掉自己之前检查一下自己。我也需要练习一下。

```
$ ruby ljust_test.rb
Loaded suite ljust_test
Started
...
Finished in 0.000863 seconds.

3 tests, 3 assertions, 0 failures, 0 errors, 0 skips

Test run options: --seed 30117
```

还是绿色的。我们可以将长度设置为我们想要的任何值。我们实际上只处理了字母数字部分。对于数字，我们需要在左侧添加空白。我们还没有检查我们是否在字符串的右边添加了空白。你为什么不继续把它加到测试里。我会等的。

```
def test_name_should_have_white_space_on_the_right
  name = nameFixing "should be 18", 18
  assert_equal(name, "should be 18      ")
end
```

你的测试通过了吗？酷毙了。让我们继续讨论数字列。几乎相同的想法，但在左边留白。这是我的快速和肮脏的代码

```
require 'test/unit'

def nameFixing(name,len)
  case
  when name.length len
    name = name.slice(0..(len-1))
  when name.length < len
    name = name.ljust(len)
  else
    name
  end
end

def numbFixing(numb,len)
  case
  when numb.length len
    numb = numb.slice(0..(len-1))
  when numb.length < len
    numb = numb.rjust(len)
  else
    numb
  end
end

class NameLengthTest < Test::Unit::TestCase
  def test_name_short_should_equal_50
    name = nameFixing "boo",50
    assert_equal(name.length, 50)
  end

def test_name_long_should_equal_50
    name = nameFixing "dgrtefdgdfshrtyutyuykhjkmbnmvcbdfgrthjghjgghvdfgrstthg",50
    assert_equal(name.length, 50)
  end

def test_name_50_should_equal_50
    name = nameFixing "dgrtefdgdfshrtyutyuymbnmvcbdfgrthjghjgghvdfgrstthg",50
    assert_equal(name.length, 50)
  end

def test_name_should_have_white_space_on_the_right
    name = nameFixing "should be 18",18
    assert_equal(name, "should be 18      ")
  end

def test_numb_should_have_white_space_on_the_left
    numb = numbFixing "123",8
    assert_equal(numb, "     123")
  end
end
```

来吧，试试看。它是绿色的吗？很好，但是我们不是很干。

## 是时候重构了。

我们可以结合这些方法，当我们需要添加空白时，我们可以在字母数字或数字中放一个 if。让我们试试那个。

```
require 'test/unit'

def allThingsFixing(data,len,type)
  case
  when data.length len
    data = data.slice(0..(len-1))
  when data.length < len
    if type == "A"
      data = data.ljust(len)
    else
      data = data.rjust(len)
    end
  else
    data
  end
end

class NameLengthTest < Test::Unit::TestCase
  def test_name_short_should_equal_50
    data = allThingsFixing "boo",50,"A"
    assert_equal(data.length, 50)
  end

def test_name_long_should_equal_50
    data = allThingsFixing "dgrtefdgdfshrtyutyuykhjkmbnmvcbdfgrthjghjgghvdfgrstthg",50,"A"
    assert_equal(data.length, 50)
  end

def test_name_50_should_equal_50
    data = allThingsFixing "dgrtefdgdfshrtyutyuymbnmvcbdfgrthjghjgghvdfgrstthg",50,"A"
    assert_equal(data.length, 50)
  end

def test_name_should_have_white_space_on_the_right
    data = allThingsFixing "should be 18",18,"A"
    assert_equal(data, "should be 18      ")
  end

def test_numb_should_have_white_space_on_the_left
    data = allThingsFixing "123",8,"X"
    assert_equal(data, "     123")
  end

end
```

你重新测试了吗？他们都通过了吗？太棒了。

## 如何处理约会？

根据定义，日期需要是`yyyymmdd`，幸运的是我们正在接收它`mmddyyyy`。我们只需要把它切碎并重新排列。

你能盲目地将数据传递给我们的方法吗？我们确实知道该列的名称是“accountOpened”为了保持进展，我们可以将一个列名传递给方法。该写测试了。

```
def test_date_should_format_properly
  data = allThingsFixing "08152003",8,"9","accountOpened"
  assert_equal(data, "20030815")
end
```

你记得添加新的变量吗？

这是我用这个方法做的

```
def allThingsFixing(data,len,type,column)
  case
  when data.length len
    data = data.slice(0..(len-1))
  when data.length < len
    if type == "A"
      data = data.ljust(len)
    else
      data = data.rjust(len)
    end
  else
    data
  end
end
```

你可以继续进行测试。它应该会失败。

```
$ ruby ljust_test.rb
Loaded suite ljust_test
Started
F.....
Finished in 0.001405 seconds.

1) Failure:
test_date_should_format_properly(NameLengthTest) [ljust_test.rb:52]:
<"08152003"expected but was
<"20030815">.

6 tests, 6 assertions, 1 failures, 0 errors, 0 skips

Test run options: --seed 25697
```

现在你只需要重新格式化日期。看起来我们只需要把这一年从后面移到前面。我们应该把它放在哪里？因为列中有八位数字，而输出中需要八位数字，所以我们将它放在 where data.len == len 的部分中

```
def allThingsFixing(data,len,type,column)
  case
  when data.length len
    data = data.slice(0..(len-1))
  when data.length < len
    if type == "A"
      data = data.ljust(len)
    else
      data = data.rjust(len)
    end
  else
    if column == "accountOpened"
      data = data.slice(4..7)+data.slice(0..3)
    else
      data
    end
  end
end
```

好像有很多 if 语句。这可以在以后重新分解。测试通过了吗？

```
$ ruby ljust_test.rb
Loaded suite ljust_test
Started
......
Finished in 0.001030 seconds.

6 tests, 6 assertions, 0 failures, 0 errors, 0 skips

Test run options: --seed 48958
```

### 变形完成！

你如何把数据输入变形金刚？敬请期待！

## 分享这篇文章