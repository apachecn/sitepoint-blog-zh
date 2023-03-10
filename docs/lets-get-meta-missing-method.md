# 让我们得到元:缺少方法

> 原文：<https://www.sitepoint.com/lets-get-meta-missing-method/>

如果你来自 PHP 或 C#，你可能不会发现自己在考虑元编程——虽然这两种语言都可以进行元编程，但它似乎没有被大量使用。在 Ruby 中不是这样——恰恰相反——元编程是 Ruby 的一个重要部分。

那么什么是元编程呢？它基本上是一种动态修改程序的方式，就像在程序开始运行后(或者我们喜欢称之为运行时)添加或更改方法一样。

rails 中的 find_by_*方法就是一个很好的例子。如果您有一个名为“User”的模型，其中有一个名为“age”的列，那么您可以使用神奇的方法 User.find_by_age 按年龄搜索您的记录。这是使用 method_missing 方法完成的。

如果您调用一个不存在的方法，Ruby 将调用 method_missing 方法，并传递该方法的名称和您提供的任何参数，这意味着您可以动态处理该方法。

```
class MyClass
  def find(name, value)
    puts "You want results from #{name} with a value of #{value}"
  end

  def method_missing(id, *args)
    return self.find(Regexp.last_match(1),  args[0]) if id.id2name =~ /find_by_(.+)/
    raise NoMethodError
  end
end

m = MyClass.new
m.find('name', 'madpilot')
> You want results from name with a value of madpilot
m.find_by_name('madpilot')
> You want results from name with a value of madpilot
m.search_by_name('madpilot')
> NoMethodError: NoMethodError

```

如您所见，肯定没有“find_by_name”方法，但是 Ruby 足够聪明，可以用正确的参数调用 find 方法。我们所做的是对方法名运行一个正则表达式，如果它匹配模式 find_by_[string],那么它调用 find 方法，将[string]部分作为第一个参数，将第一个参数作为第二个参数。很强大，你不觉得吗？

只是一个提示——小心无限循环——如果你的 method_missing 方法没有终止，而你调用了一个不存在于 method_missing 中的方法，你会有各种各样的麻烦！`` 

## ``分享这篇文章``