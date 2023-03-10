# 用 Redis 和 Ohm 进行半关系数据建模

> 原文：<https://www.sitepoint.com/semi-relational-data-modeling-redis-ohm/>

![ohm](img/b27a49c0cdd877a7dc52e3b737dc7e4b.png)

你可能知道 [Redis](http://http://redis.io/) 。如果您从未使用过它，您可能会认为它是另一个内存中的数据存储。但是如果你这样想，你就有失公允了:Redis 不仅仅是一个键值存储。它是一个数据结构服务器*，包括*一个内存数据集。Redis 超越了仅仅是键值存储的功能，它支持:

*   列表、集合和散列等数据结构
*   持久性，在*快照*和*基于日志的*模式下
*   发布/订阅机制和扩展消息队列实现
*   集群、分区和高可用性(通过[Sentinel](http://redis . io/topics/Sentinel))

在本教程中，我们将创建一个围绕 Redis 的数据结构自我包装的对象模型，为我们提供了一种在 Ruby 代码中与 Redis 无缝交互的方法。这是优秀的[欧姆](https://github.com/soveran/ohm)宝石提供的。

此外，我们将看到如何使用这个对象模型来模拟一些类似 ActiveRecord 的功能。在我们开始之前，让我们弄清楚一件事:Redis *不是关系数据库，Ohm *不是 ORM。如果您希望将 Redis/Ohm 用于事务和级联引用完整性，您会非常失望。**

但是，如果您希望以一种令人眼花缭乱的快速方式存储和检索松散相关的实体(想想聚合，而不是复合)，并获得数据分区、高可用性和发布/订阅功能的额外好处，那么 Redis 和 Ohm 就是为您量身定做的。

## 基本规则

本教程不打算涵盖欧姆或 Redis 特性的全部范围。它将涵盖理解和实现最常见的数据建模用例所需的一小部分。欧姆在两个方面起作用:

*   管理 Ruby 内存空间中的对象
*   管理 Redis 散列、索引和对应于这些 Ruby 对象的其他数据结构

两者之间的同步并不总是显而易见的，但是 Ohm 提供了多种方法来隐式或显式地管理同步。在本教程中，我们更喜欢隐式方法，这给了我们一个选择，即假设对 Ohm 对象的任何操作都会对相应的 Redis 数据结构产生同等的影响(除非另有说明)。例如，当我们更新一个对象的属性时，我们希望相应地更新相应的 Redis 条目。

## 安装

我们显然需要安装 [Redis](http://redis.io) 并确保它正常运行。按照这里[的说明](http://redis.io/topics/quickstart)让 Redis 在你的机器上工作。

此外，如前所述，我们将利用 [Ohm](https://github.com/soveran/ohm) gem，因此请确保它已安装:

```
$ gem install ohm
```

## 定义类别

出于本教程的目的，我们将建模一个创作系统。定义两个类:一个 Author 类和一个 Book 类，都是从`Ohm::Model`派生的。一个作者可以创作 0 到许多本书。假设一本书只能属于一个作者。

该系统的一个非常基本的实现如下所示:

```
require 'ohm'

class Author < Ohm::Model
  attribute :f_name
  attribute :l_name
  attribute :email
  attribute :age
end

class Book < Ohm::Model
  attribute :title
end
```

### 属性

我们使用`attribute`类宏定义了我们的核心类属性。属性值被定义为字符串，但是我们也可以将 lambda 作为第二个参数传递给宏，这可以作为一种简单的类型转换方式:

```
attribute :age, lambda { |x| x.to_i }
```

以上确保了`age`属性总是作为一个*整数*返回，即使我们将其设置为一个*字符串*。

要设置属性的值，请使用`update`实例方法:

```
irb> author.update(email: 'fred@gmail.com')
#=> <Author>
```

要获取属性的值，通常使用点符号:

```
irb> author.email
#=> fred@gmail.com
```

点符号将从本地缓存的 Author 对象中获取属性值。要直接从 Redis 获取值，使用`get`实例方法:

```
irb> author.get(:email)
#=> fred@gmail.com
```

### 索引

我们可以用`index`类宏索引属性。这使得属性可以通过使用`find`类方法进行搜索。让我们确保可以通过作者的`last_name`来搜索他们:

```
class Author < Ohm::Model
  index :l_name
end
```

这现在是可能的:

```
irb> author = Author.create(f_name: "Joe", l_name: "Bloggs", age: 34, email: "jbloggs@gmail.com")
irb> Author.find(l_name: "Bloggs").size
#=> 1
```

`find`方法返回一个`Ohm::Set`对象。一个`Ohm::Set`是一个无序列表，其外部行为类似于 Ruby 数组。就像数组一样，可以使用类似`first`的方法提取信息:

```
irb> Author.find(l_name: "Bloggs").first.email
#=> "jbloggs@gmail.com"
```

### 独一无二

如果我们想让一个属性有一个唯一的值，就把它设置为`unique`。在我们的作者示例中，`email`属性可能是标记为 unique 的一个很好的候选:

```
class Author < Ohm::Model
  unique :email
end
```

现在，使用与现有作者相同的电子邮件值创建一个作者将导致一个`Ohm::UniqueIndexViolation`错误。

## 模型创建

就像在 ActiveRecord 中一样，我们使用`create`类方法来创建一个新的模型对象:

```
irb> author = Author.create(f_name: "John", l_name: "Smith", age: 34, email: "jsmith@gmail.com")
```

它返回:

```
#=> #<Author:0x000000023c8118 @attributes={:f_name=>"John", :l_name=>"Smith", :age=>34, :email=>"jsmith@gmail.com"}, @_memo={}, @id="1">
```

一件立即显而易见的事情是 Ohm 为我们的新对象添加了一个`id`属性。这相当于关系数据库的主键:当前 Redis 数据库中该类型对象的唯一标识符。我们现在可以使用`id`属性值和`[]`类方法来创建一个具有相同属性的实例，只要我们需要:

```
irb> a = Author[1]
#=> #<Author:0x000000021db990 @attributes={:f_name=>"John", :l_name=>"Smith", :age=>34, :email=>"jsmith@gmail.com"}, @_memo={}, @id="1">
```

请注意，每次我们基于作者对象的 id 检索它时，都会创建一个新的作者对象，它具有与我们的目标对象相同的属性。因此，我们可以让许多 Ruby `Ohm::Model`对象都引用同一个 Redis-stored 实体。

## 删除密钥并使其过期

通过调用对象的`delete`方法删除对象，即`object.delete`。使用`#delete`时请记住两件事:

1.  它将从 Redis 中删除对象键，包括唯一性、索引和对象可能包含的任何集合。然而，它将**而不是**删除对象集合引用的对象。这些将继续快乐地生活在 Redis 的内存中，直到它们过期，被 Redis 的[驱逐策略](http://redis.io/topics/lru-cache)强制逐出，或者被调用它们的`#delete`方法删除。
2.  它将从 Redis 中删除对象键，但不会从应用程序的内存空间中删除 Ruby 对象。此外，剩余的 Ruby 对象可用于“重新激活”已删除的 Redis 对象键，产生以下现象:

    ```
    irb> author = Author.create(f_name: "John", l_name: "Smith", age: 34, email: "abc@gmail.com")
    => #<Author:0x00000001e48e68 @attributes={:f_name=>"John", :l_name=>"Smith", :age=>34, :email=>"abc@gmail.com"}, @_memo={}, @id="13">

    irb> Author[13] #check that new author is stored in Redis
    => #<Author:0x00000001e2f238 @attributes={:f_name=>"John", :l_name=>"Smith", :email=>"abc@gmail.com", :age=>"34"}, @_memo={}, @id=13>
    irb> author.delete #delete author from Redis
    => #<Author:0x00000001e48e68 @attributes={:f_name=>"John", :l_name=>"Smith", :age=>34, :email=>"abc@gmail.com"}, @_memo={}, @id="13">
    irb> Author[13] #author not present in Redis anymore
    => nil
    irb> author.update(f_name: 'Jack') #we use the author object to update an attribute
    => #<Author:0x00000001e48e68 @attributes={:f_name=>"Jack", :l_name=>"Smith", :age=>34, :email=>"abc@gmail.com"}, @_memo={}, @id="13">
    irb> Author[13] #surprise! the author has been re-created in Redis
    => #<Author:0x00000001cffe58 @attributes={:f_name=>"Jack", :l_name=>"Smith", :email=>"abc@gmail.com", :age=>"34"}, @_memo={}, @id=13>
    ```

只要您意识到这种行为，您就可以根据需要使用`delete`。然而，在 **redis.conf** 文件中设置一个好的驱逐策略是安全的。一个好的选择是:

```
maxmemory 512mb
maxmemory-policy volatile-lru
```

这将导致 Redis 在达到 512 兆字节内存限制时立即开始删除键。只有在关键字有一个*过期集*的情况下，才会在最近最少使用的基础上删除关键字，这在 Redis 数据库保存混合数据(即，不仅 Ohm 数据，还有来自其他应用程序的某种缓存数据)的情况下很有用。如果 Redis 数据库的唯一目的是保存关系数据，并且过期的时间很关键，那么建议使用`noeviction`策略设置。

## 联合

`Ohm::Model`允许我们使用`#reference`和`#collection`宏来定义对象之间的关系。`collection`宏相当于 ActiveRecord 的`has_many`方法，而`reference`大致模仿 ActiveRecord 的`has_a`或`belongs_to`方法。将这些宏添加到现有类中:

```
require 'ohm'

class Author < Ohm::Model
  attribute :f_name
  attribute :l_name
  attribute :email
  attribute :age, lambda { |x| x.to_i }
  index :l_name
  unique :email

  collection :books, :Book
end

class Book < Ohm::Model
  attribute :title
  reference :author, :Author
end
```

创建一个作者和几本书:

```
irb> author = Author.create(f_name: "John", l_name: "Smith", age: 34, email: "jsmith@gmail.com")
=> #<Author:0x0000000279a480 @attributes={:f_name=>"John", :l_name=>"Smith", :age=>34, :email=>"jsmith@gmail.com"}, @_memo={}, @id="1">

irb> book1 = Book.create(title: 'Moby Dick')
=> #<Book:0x00000002776fa8 @attributes={:title=>"Moby Dick"}, @_memo={}, @id="1">

irb> book2 = Book.create(title: 'The Hobbit')
=> #<Book:0x000000026348c0 @attributes={:title=>"The Hobbit"}, @_memo={}, @id="2">
```

`reference`宏给了我们的 book 对象一个`author`属性，我们可以用它来设置书籍的作者:

```
irb> book1.update(author: author)
=> #<Book:0x00000002776fa8 @attributes={:title=>"Moby Dick", :author_id=>"1"}, @_memo={}, @id="1">
irb> book2.update(author: author)
=> #<Book:0x000000026348c0 @attributes={:title=>"The Hobbit", :author_id=>"1"}, @_memo={}, @id="2">
```

Ohm 为图书对象添加了一个新的`author_id`属性。这是一个外键属性，引用与这本书相关联的作者。Author 类中使用的`collection`宏确保作者可以跟踪他们所有的书:

```
irb> author.books.each {|b| puts b.title}
=> Moby Dick
=> The Hobbit
```

此外，我们可以使用一些`Ohm::Set`的排序和查找方法，如`#include?`、`#find`、`#sort`、`#sort_by`来对图书集合进行筛选和排序。

## 直接和 Redis 对话

如果您需要运行不是由 Ohm 方法抽象的 Redis 命令，请使用`redis.call`方法:

```
irb> Ohm.redis.call "FLUSHDB"
=> "OK"
```

在这里，我们告诉 Redis 清除当前数据库。我们可以从 Ruby 代码中以这种方式运行任何 Redis-cli 命令。

## 摘要

Ohm–Redis 组合提供了巨大的潜力，这里只描述其中的一小部分。只需很少的努力，我们就可以利用大量强大的 NoSQL 功能和一点良好的关系，这是许多现代应用程序的完美组合。

## 分享这篇文章