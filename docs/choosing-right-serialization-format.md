# 选择正确的序列化格式

> 原文：<https://www.sitepoint.com/choosing-right-serialization-format/>

![Digital touch](img/6557b64e9e4bc4b3b17d7bfee4b25c72.png)

在保存或交流某种信息时，我们经常使用*序列化*。序列化接受一个 Ruby 对象并将其转换成一个字节串，反之亦然。例如，如果您有一个表示用户信息的对象，并且需要通过网络发送它，那么它必须被序列化为一组可以通过套接字发送的字节。然后，在另一端，接收者必须将对象反序列化，将其转换回 Ruby(或另一种语言)可以理解的内容。

事实证明，有很多方法可以序列化 Ruby 对象。我将在本文中介绍 YAML、JSON 和 MessagePack，探索他们的钢琴和强项，看看他们在 Ruby 中的表现。最后，我们将使用一些元编程技巧构建一个模块化序列化方法。

让我们跳进来吧！

## 亚姆

YAML 是代表“YAML 不是标记语言”的递归首字母缩写词。它是一种序列化格式，但也是(容易)可读的，这意味着它可以用作配置语言。事实上，Rails 使用 YAML 进行各种配置，例如数据库连接。

让我们来看一个例子:

```
name: "David"
height: 124
age: 28
children:
  "John":
    age: 1
    height: 10
  "Adam":
    age: 2
    height: 20
  "Robert":
    age: 3
    height: 30
traits:
  - smart
  - nice
  - caring
```

YAML 的格式*居然*简单易懂。让它点击的最快方法是将其转换成 Ruby hash 或 Javascript 对象。我们就用前者吧(在 **test.yaml** 中省去了上面的 YAML):

```
require 'yaml'

YAML.load File.read('test.yaml')
```

在 [Pry](http://pryrepl.org/) 中运行上述内容将会得到一个格式良好的结果，如下所示:

```
{"name"=>"David",
 "height"=>124,
 "age"=>28,
 "children"=>{"John"=>{"age"=>1, "height"=>10},
             "Adam"=>{"age"=>2, "height"=>20},
             "Robert"=>{"age"=>3, "height"=>30}},
 "traits"=>["smart", "nice", "caring"]}
```

如您所见，冒号代表“键-值”配对，制表符创建新的散列。小连字符告诉 YAML，我们想要一个列表，而不是一个散列。这种在 YAML 和 Ruby 字典之间的简单转换是 YAML 的主要好处之一。

```
require 'yaml'

class Person
  attr_accessor :name, :age, :gender

  def initialize(name, age, gender)
    @name = name
    @age = age
    @gender = gender
  end

  def to_yaml
    YAML.dump ({
      :name => @name,
      :age => @age,
      :gender => @gender
    })
  end

  def self.from_yaml(string)
    data = YAML.load string
    p data
    self.new(data[:name], data[:age], data[:gender])
  end
end

p = Person.new "David", 28, "male"
p p.to_yaml

p = Person.from_yaml(p.to_yaml)
puts "Name #{p.name}"
puts "Age #{p.age}"
puts "Gender #{p.gender}"
```

让我们分解代码。我们有`to_yaml`方法:

```
def to_yaml
  YAML.dump ({
    :name => @name,
    :age => @age,
    :gender => @gender
  })
end
```

我们正在使用标准库提供的模块制作一个 Ruby hash 并将其转换成 YAML 字符串。要从另一个方向将 YAML 字符串转换成 Ruby 对象:

```
def self.from_yaml(string)
  data = YAML.load string
  p data
  self.new(data[:name], data[:age], data[:gender])
end
```

这里，获取字符串，将其转换成 Ruby 哈希，然后使用我们的哈希内容和构造函数来构造一个新的`Person`实例。

现在，让我们看看 YAML 是如何与来自 Javascript 领域的重量级人物相比较的。

## JSON

在某些方面，JSON 与 YAML 非常相似。它应该是一种人类可读的格式，通常用作配置格式。两者都在 Ruby 社区中被广泛采用。然而，JSON 的不同之处在于它的根源来自 Javascript。事实上，JSON 实际上代表 Javascript 对象符号。JSON 的语法几乎与定义 Javascript 对象的语法相同(有点类似于 Ruby 散列)。让我们看一个例子:

```
{
  "name": "David",
  "height": 124,
  "age": 28,
  "children": {"John": {"age": 1, "height": 10},
             "Adam": {"age": 2, "height": 20},
             "Robert": {"age": 3, "height": 30}},
  "traits": ["smart", "nice", "caring"]
}
```

那看起来真的很像古老的红宝石酱。唯一的区别似乎是在 JSON 中用“:”而不是 Ruby 中的`=>`来表示密钥对关系。

让我们看看这个例子在 Ruby 中到底是什么样子的:

```
require 'json'
JSON.load File.read("test.json")

{"name"=>"David",
 "height"=>124,
 "age"=>28,
 "children"=>{"John"=>{"age"=>1, "height"=>10},
             "Adam"=>{"age"=>2, "height"=>20},
             "Robert"=>{"age"=>3, "height"=>30}},
 "traits"=>["smart", "nice", "caring"]}
```

我们可以向之前开发的`Person`类添加一组方法，使其成为 JSON 可序列化的:

```
require 'json'

class Person
  ...
  def to_json
    JSON.dump ({
      :name => @name,
      :age => @age,
      :gender => @gender
    })
  end

  def self.from_json(string)
    data = JSON.load string
    self.new(data['name'], data['age'], data['gender'])
  end
  ...
end
```

底层代码完全相同，除了方法使用了`JSON`而不是`YAML`！

JSON 与众不同之处在于它与 Ruby 和 Javascript 语法的相似性。写代码时在 YAML 和 Ruby 之间切换需要一些精神能量。JSON 不存在这样的问题，因为它的语法和 Ruby 的几乎一样。此外，许多现代浏览器默认都有 JSON 的 Javascript 实现，这使它成为 AJAX 通信的通用语言。

另一方面，YAML 需要一个额外的库，并且在 Javascript 社区中没有那么多追随者。如果序列化方法的主要目标是与 Javascript 进行通信，那么先看看 JSON。

## 消息包

到目前为止，我们还没有太注意一个序列化的对象消耗了多少空间。事实证明，小的序列化大小是一个非常重要的特性，尤其是对于需要低延迟和高吞吐量的系统。这就是 [MessagePack](http://msgpack.org/) 介入的地方。

不像 JSON 和 YAML，MessagePack 是*而不是*人类可读的！它是一种二进制格式，这意味着它将信息表示为任意字节，而不一定是表示字母表的字节。这样做的好处是，它的序列化通常比 YAML 和 JSON 的序列化占用更少的空间。尽管这确实排除了 MessagePack 作为配置文件格式的可能性，但它对那些构建快速、分布式系统的人来说非常有吸引力。

让我们看看如何与 Ruby 一起使用它。不像 YAML 和 JSON，MessagePack 没有和 Ruby 捆绑在一起(还没有！).所以，让我们给自己弄一份拷贝:

```
gem install msgpack
```

我们可以稍微改变一下:

```
require 'msgpack'
msg = {:height => 47, :width => 32, :depth => 16}.to_msgpack

#prints out mumbo-jumbo
p msg

obj = MessagePack.unpack(msg)
p obj
```

首先，创建一个标准的 Ruby hash 并对其调用`to_msgpack`。这将返回 MessagePack 序列化的哈希版本。然后，用`MessagePack.unpack`去序列化序列化后的 hash(我们应该把原来的 hash 拿回来)。当然，我们可以使用我们良好的旧转换器方法(注意类似的 API):

```
class Person
  ...
  def to_msgpack
    MessagePack.dump ({
      :name => @name,
      :age => @age,
      :gender => @gender
    })
  end

  def self.from_msgpack(string)
    data = MessagePack.load string
    self.new(data['name'], data['age'], data['gender'])
  end
  ...
end
```

好的，所以当我们需要速度的时候应该使用 MessagePack，当我们需要与 Javascript 交流的时候应该使用 JSON，而 YAML 是用于配置文件的。但是，当你开始一个大项目时，你通常不确定该选择哪一个，所以我们如何保持选择的开放性呢？

## 用 Mixins 模块化

Ruby 是一种动态语言，具有一些非常棒的元编程特性。让我们用它们来确保我们不会把自己束缚在一个我们以后可能会后悔的方法中。首先，注意前面创建的`Person`序列化/非序列化方法看起来非常相似。

让我们把它变成混音:

```
require 'json'

#mixin
module BasicSerializable

  #should point to a class; change to a different
  #class (e.g. MessagePack, JSON, YAML) to get a different
  #serialization
  @@serializer = JSON

  def serialize
    obj = {}
    instance_variables.map do |var|
      obj[var] = instance_variable_get(var)
    end

    @@serializer.dump obj
  end

  def unserialize(string)
    obj = @@serializer.parse(string)
    obj.keys.each do |key|
      instance_variable_set(key, obj[key])
    end
  end
end
```

首先，注意`@@serializer`被设置为序列化类。这意味着我们可以立即改变我们的序列化方法，只要我们的可序列化类包含这个模块。

仔细看看代码，基本上就是看看实例变量来序列化和反序列化一个对象/字符串。在`serialize`方法中:

```
def serialize
  obj = {}
  instance_variables.map do |var|
    obj[var] = instance_variable_get(var)
  end

  @@serializer.dump obj
end
```

它循环遍历`instance_variables`并构建变量名和值的 Ruby 散列。然后，简单地使用`@@serializer`来转储对象。如果序列化机制没有一个`dump`方法，我们可以简单地子类化它，给它那个方法！

我们对 unserialize 方法使用类似的方法:

```
def unserialize(string)
  obj = @@serializer.parse(string)
  obj.keys.each do |key|
    instance_variable_set(key, obj[key])
  end
end
```

这里，使用序列化程序从字符串中获取 Ruby 哈希值，并将对象的实例变量设置为哈希值。

这使得我们的`Person`类非常容易实现:

```
class Person
include BasicSerializable

  attr_accessor :name, :age, :gender

  def initialize(name, age, gender)
    @name = name
    @age = age
    @gender = gender
  end
end
```

注意，我们只是添加了`include BasicSerializable`行！让我们来测试一下:

```
p = Person.new "David", 28, "male"
p p.serialize

p.unserialize (p.serialize)
puts "Name #{p.name}"
puts "Age #{p.age}"
puts "Gender #{p.gender}"
```

现在，如果您仔细梳理代码(或者只是理解底层概念)，您可能会注意到`BasicSerializable`方法对于只有可序列化实例变量(即整数、字符串、浮点等)的对象非常有效。或者它们的数组和散列)。但是，对于有其他`BasicSerializable`对象作为实例的对象，它将失败。

解决这个问题最简单的方法是在这样的类中覆盖`serialize`和`unserialize`方法，就像这样:

```
class People
  include BasicSerializable

  attr_accessor :persons

  def initialize
    @persons = []
  end

  def serialize
    obj = @persons.map do |person|
      person.serialize
    end

    @@serializer.dump obj
  end

  def unserialize(string)
    obj = @@serializer.parse string
    @persons = []
    obj.each do |person_string|
      person = Person.new "", 0, ""
      person.unserialize(person_string)
      @persons << person
    end
  end

  def <<(person)
    @persons << person
  end
end
```

## 收尾工作

序列化是一个经常被忽视的非常重要的话题。当优化时间到来时，选择正确的序列化方法可以使您的生活更加轻松。除了我们对序列化方法的介绍，模块化方法(可能需要针对特定的应用程序进行修改)可以帮助您在以后改变您的决定。

## 分享这篇文章