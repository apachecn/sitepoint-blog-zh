# 使用 ActiveRecord 掌握多对多关联

> 原文：<https://www.sitepoint.com/master-many-to-many-associations-with-activerecord/>

![](img/0e245b880236ff7f731e1094e6002c79.png)

在 ActiveRecord 世界中，对数据实体之间的多对多关系进行建模并不总是一项简单的任务。即使我们有一个定义良好的 ER 图，也并不总是清楚我们应该使用哪个 ActiveRecord 关联，以及我们的决策会有什么影响。多对多关系有两种类型:及物和不及物。在数学方面，

> 二元关系 R 是传递的，只要元素 A 与元素 B 相关，而元素 B 又与元素 c 相关。

在数据建模环境中，如果两个实体之间的关系可以通过引入一个或多个其他实体得到最好的表达，那么它就是可传递的。因此，举例来说，很容易看出一个买方从许多卖方购买，而一个卖方卖给许多买方。然而，直到我们开始添加产品、支付、市场等实体时，这种关系才被完全表达出来。这样的关系可以被称为*可传递的多对多*，因为我们依赖于其他实体的存在来完全捕获关系的语义。幸运的是，ActiveRecord 允许我们轻松地对这种关系进行建模。让我们从查看最简单的活动记录多对多关联开始，并逐步深入。

## 不及物联想

这是最简单的多对多关联。两个模型仅仅因为它们的存在而联系在一起。一本书可以由很多作者写，一个作者也可以写很多本书。这是一个直接的关联，两个模型之间有直接的依赖关系。我们真的不能缺一不可。在 ActiveRecord 中，这可以很容易地用*has _ and _ owners _ to _ many*(HABTM)关联来建模。我们可以通过运行以下命令在 Rails 中为这种关系创建模型和迁移:

```
rails g model Author name:string
rails g model Book title:string
rails g migration CreateJoinTableAuthorsBooks authors books 
```

我们需要在模型中定义 HABTM 关联，如下所示:

```
class Book < ApplicationRecord
  has_and_belongs_to_many :authors
end
class Author < ApplicationRecord
  has_and_belongs_to_many :books
end 
```

然后，我们可以通过运行以下命令来创建数据库表:

```
rails db:migrate 
```

最后，我们可以填充我们的数据库:

```
herman = Author.create name: 'Herman Melville'
moby = Book.create title: 'Moby Dick'
herman.books << moby 
```

除了别的以外，我们现在可以访问:一本书的作者，一个作者写的所有书，以及写了一本特定书的所有作者:

```
moby.authors
herman.books
herman.books.where(title: 'Moby Dick') 
```

简单明了。

## 单传递关联

增加一个额外的模型就可以很好地描述这种传递关联。举一个学生的例子。一个学生可以由很多导师来教，一个导师可以教很多学生，但是我们不能完全表达这种关系，除非我们包括另一个实体:Class(为了避免 Ruby 保留名称冲突，我们将其命名为 Klass)

```
rails g model Student name:string
rails g model Tutor name:string
rails g model Klass subject:string student:references tutor:references 
```

我们可以说，学生是通过听课来学习的，而老师是通过上课来教学生的。从*到*这个词在这里很重要，因为我们在 ActiveRecord 中使用相同的术语来定义关联:

```
class Student < ApplicationRecord
  has_many :klasses
  has_many :tutors, through: :klasses
end
class Tutor < ApplicationRecord
  has_many :klasses
  has_many :students, through: :klasses
end
class Klass < ApplicationRecord
  belongs_to :student
  belongs_to :tutor
end 
```

现在，我们可以通过运行以下命令来创建数据库表:

```
rails db:migrate 
```

然后，我们可以填充数据库:

```
bart = Student.create name: 'Bart Simpson'
edna = Tutor.create name: 'Mrs Krabapple'
Klass.create subject: 'Maths', student: bart, tutor: edna 
```

除了常见的简单*查找*之外，我们还可以创建一些更复杂的查询:

```
Student.find_by(name: 'Bart Simpson').tutors  # find all Bart's tutors
Student.joins(:klasses).where(klasses: {subject: 'Maths'}).distinct.pluck(:name) # get all students who attend the Maths class
Student.joins(:tutors).joins(:klasses).where(klasses: {subject: 'Maths'}, tutors: {name: 'Mrs Krabapple'}).distinct.map {|x| puts x.name} # get all students who attend Maths taught by Mrs Krabapple 
```

正如在大多数单传递关联的情况下，现有的模型名称隐式地反映了关联(即 X `has_many Z through Y`)，我们不需要再做任何事情，ActiveRecord 将完美地模拟我们的关联。

## 多传递关联

多传递关联是一种可以通过许多其他模型来最好地表达的关联。例如，作为开发人员，我们与许多软件社区有联系。然而，我们的联系有多种形式:我们可能贡献代码、在论坛上发帖、参加活动等等。每个开发人员通过特定的操作以他们自己的方式与社区相关联。让我们从这些动作中挑选三个作为例子:

*   贡献代码
*   在论坛上发帖
*   出席活动

建模过程的下一步是定义帮助实现这些动作(关联)的数据实体(模型)。对于我们的例子，我们可以有把握地得出:

| 联合 | 贯穿模型 |
| --- | --- |
| 贡献代码 | 贮藏室ˌ仓库 |
| 在论坛上发帖 | 论坛 |
| 出席活动 | 事件 |

现在，让我们继续创建我们需要的模型:

```
rails g model Community name:string
rails g model Developer name:string
rails g model Repo url:string comment:string developer:references community:references
rails g model Forum url:string post:text developer:references community:references
rails g model Event location:string name:string developer:references community:references
rails db:migrate 
```

让我们也创建一些开发人员和社区:

```
devs = %w(joe sue fred mary).map {|dev| Developer.create name: dev}
comms = %w(rails nosql javascript postgres).map {|comm| Community.create name: comm} 
```

然后，我们可以定义模型之间的关联。此时，我们可能会尝试使用在单传递示例中使用的相同技术，并为每个关联重复`has_many..through`调用:

```
class Developer < ApplicationRecord
  has_many :events
  has_many :forums
  has_many :repos
  has_many :appearances, through: :events  #FAIL
  has_many :postings, through: :forums #FAIL
  has_many :contributions, through: :repos #FAIL
end 
```

然而，这是行不通的，因为 ActiveRecord 会试图从关联名称(如外观)推断出关联的源模型的名称，并且会失败。因此，我们需要使用`:source`选项指定源模型名称:

```
class Developer < ApplicationRecord
  has_many :events
  has_many :forums
  has_many :repos
  has_many :appearances, through: :events, source: :community
  has_many :postings, through: :forums, source: :community
  has_many :contributions, through: :repos, source: :community
end 
```

同样，我们也为社区做同样的事情:

```
class Community < ApplicationRecord
  has_many :events
  has_many :forums
  has_many :repos
  has_many :hostings, through: :events, source: :developer
  has_many :discussions, through: :forums, source: :developer
  has_many :contributions, through: :repos, source: :developer
end 
```

您可能已经注意到，在社区模型中，我们正在更改一些协会的名称，以反映它们在关系这一方的性质。例如，一个开发者在活动中亮相，而一个社区举办活动。一个开发者在论坛上发帖，而一个社区在论坛上促进讨论。这样，我们可以确保我们的方法名(AR 将根据我们的关联动态创建)是有意义和清晰的。

我们现在可以创建一些活动、论坛和回复:

```
Repo.create url: 'www.gitlab.com/342', comment: 'ruby code', developer: devs[0], community: comms[0]
Repo.create url: 'www.gitlab.com/662', comment: 'callbacks sample', developer: devs[0], community: comms[2]
Repo.create url: 'www.jsfiddle.com/abcg3', comment: 'reactive sample', developer: devs[1], community: comms[3]
Repo.create url: 'www.jsfiddle.com/563', comment: 'promises sample', developer: devs[2], community: comms[3]
Forum.create url: 'www.stackoverflow.com/mongodb', post: 'this is what I think...', developer: devs[2], community: comms[1]
Forum.create url: 'www.redis.com/563', post: 'my opinion is...', developer: devs[3], community: comms[1]
Event.create location: 'Bath, UK', name: 'Bath Ruby', developer: devs[2], community: comms[0]
Event.create location: 'Tech Institute', name: 'London NoSQL Meetup', developer: devs[2], community: comms[1] 
```

然后，我们可以开始从模型中提取有用的信息:

```
devs.find_by(name: 'fred').appearances # events a developer has appeared at
Event.find_by(community: comms[0]) # all events for the Rails community
Forum.where(developer: Developer.find_by(name: 'fred') # all forums where a specific developer has posted
Community.find_by(name: 'rails').hostings + Community.find_by(name: 'rails').discussions + Community.find_by(name: 'rails').contributions # get all events, forums and repositories for a specific community
Developer.select('distinct developers.name').joins(:repos).joins(:events).joins(:forums) # find developers who have appeared in Events, contributed to Repos and chatted on Forums, for any Community 
```

我们可以直接使用关联和/或加入`through`模型进行各种各样的排列来检索我们需要的数据。

## 迷雾

*   如果你在两个模型之间有一个直接的、多对多的关系，其中不需要进一步的语义澄清来描述这个关系，使用一个`has_and_belongs_to_many`关联。
*   如果多对多关系是间接的，或者需要一个额外的实体来完整地描述，并且关系名称可以通过额外的模型名称来获取，那么使用`has_many :through`关联。
*   如果多对多关系的细微差别需要多个其他实体来描述，那么就使用一个`has_many :through :source`关联。

使用 ActiveRecord 对多对多关系进行建模可能很难做到正确。一旦您理解了每个关联的性质和 ActiveRecord 提供的选项，这个任务就容易多了。

## 分享这篇文章