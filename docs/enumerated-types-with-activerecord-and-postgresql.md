# 带有 ActiveRecord 和 PostgreSQL 的枚举类型

> 原文：<https://www.sitepoint.com/enumerated-types-with-activerecord-and-postgresql/>

![](img/b139754747f8da6e66dd15e561fc4557.png)

一个*枚举类型*(也称为“enum”)是一个具有一组命名元素的数据类型，其中每个元素都有一个不同的值，可以用作常量等等。enum 可以在各种语言和框架中实现，在本文中，我们将在一个 [Ruby on Rails](http://rubyonrails.org/) 应用程序中实现 enum。

众所周知，Rails 是一个开源的 MVC T2 web 应用框架。MVC 中的 M 是 [ActiveRecord](http://guides.rubyonrails.org/active_record_basics.html) ，是一个*对象关系映射框架*。ActiveRecord 使我们能够以面向对象的方式表示模型、它们的数据和其他数据库操作。一个枚举在 ActiveRecord 中被特殊处理，使用 [`ActiveRecord::Enum`](http://api.rubyonrails.org/classes/ActiveRecord/Enum.html) 模块实现。

Rails 提供了一个非常好的`ActiveRecord::Enum`用法，为了有一个更好的架构，我们需要将它与 [PostgreSQL](http://www.postgresql.org/) [枚举类型](http://www.postgresql.org/docs/9.4/static/datatype-enum.html)一起使用。不幸的是，很少或根本没有提到如何集成这些技术。甚至有一个[语句](http://edgeguides.rubyonrails.org/active_record_postgresql.html#enumerated-types)说它没有对枚举类型的特殊支持，尽管这是可能的。

在本文中，我们将介绍如何使用 PostgreSQL 枚举类型和`ActiveRecord::Enum`。

本文分为以下三个部分:

*   `ActiveRecord::Enum`简介
*   PostgreSQL 枚举类型简介
*   将`ActiveRecord::Enum`与 PostgreSQL 枚举类型集成

如果你已经熟悉以上任何一个，请随意跳过它，直接跳到你喜欢的那个。

## ActiveRecord::Enum 简介

`ActiveRecord::Enum`是在 Rails 4.1 中引入的，正如[发布说明](http://guides.rubyonrails.org/4_1_release_notes.html#active-record-enums)中所宣布的。它提供了管理枚举、改变枚举值以及根据任何可用的枚举值确定模型范围的能力。在使用`ActiveRecord::Enum`之前，您需要了解 3 个方面:数据库迁移、声明和使用。

本文将使用下面的用例来实现`ActiveRecord::Enum`。我们将向用户模型添加性别属性，可能的值如下:

*   男性的
*   女性的
*   不确定
*   不愿透露

### 数据库迁移

`ActiveRecord::Enum`使用整数数据类型在数据库中存储枚举的值。让我们按如下方式创建数据库迁移:

```
bundle exec rails generate migration AddGenderToUsers gender:integer
```

上面的代码将生成一个新的迁移:

```
# db/migrate/20150619131527_add_gender_to_users.rb
class AddGenderToUsers < ActiveRecord::Migration
  def change
    add_column :users, :gender, :integer
  end
end
```

我们可以为迁移添加一个默认值。为了提高应用程序性能，我们还可以通过添加以下代码来为该列添加索引:

```
# db/migrate/20150619131527_add_gender_to_users.rb
class AddGenderToUsers < ActiveRecord::Migration
  def change
    # add `default: 3`, and `index: true`
    add_column :users, :gender, :integer, default: 0, index: true
  end
end
```

最后，运行迁移:

```
bundle exec rake db:migrate
```

我们已经完成了数据库迁移。接下来，我们需要向模型声明`ActiveRecord::Enum`。

### 申报

`ActiveRecord::Enum`有两种申报方式。使用我们的用例，我们将在用户模型中添加性别。第一个声明是使用*数组*:

```
# app/models/user.rb
class User < ActiveRecord::Base
  enum gender: [ :male, :female, :not_sure, :prefer_not_to_disclose ]
end
```

上面的代码将把`male`值存储为 0，其余的值按照它们在数组中的顺序进行映射。未来的加法必须添加到数组的末尾，并且不能重新排序或删除值。为了以后能够操作值，使用第二种类型的声明，使用`Hash`:

```
# app/models/user.rb
class User < ActiveRecord::Base
  enum gender: { male: 0, female: 1, not_sure: 2, prefer_not_to_disclose: 3 }
end
```

通过这个声明，我们可以重新排序和删除任何值。该值可以从任何数字开始，只要它与数据库迁移中的默认值一致。未来的附加物可以放在任何位置。

### 使用

`ActiveRecord::Enum`的使用有三个方面，分别是:变异、检索和范围。提供了助手方法来做这三件事。对于我们的用例，我们可以如下改变用户的性别:

```
# mutate enum using the exclamation mark (!) method.
user.male!
user.female!
user.not_sure!
user.prefer_not_to_disclose!

# or mutate enum using value assignment
user.gender = nil
user.gender = "male"
user.gender = "female"
user.gender = "not_sure"
user.gender = "prefer_not_to_disclose"
```

要检索枚举值:

```
# retrieve enum value using the question mark (?) method
user.male?                   # => false
user.female?                 # => false
user.not_sure?               # => false
user.prefer_not_to_disclose? # => true

# or retrieve using the enum name
user.gender                  # => "prefer_not_to_disclose"
```

要将用户模型的范围限定为枚举值，请使用以下方法:

```
# scope using enum values
User.male
User.female
User.not_sure
User.prefer_not_to_disclose

# or we can scope it manually using query with provided class methods
User.where("gender <> ?", User.genders[:prefer_not_to_disclose])
```

如前所述，`ActiveRecord::Enum`使用整数将值存储在数据库中。不幸的是，数据库中的 enum 值变得不那么有意义，并且不容易解释。枚举值变得依赖于模型，变得与模型高度耦合。为了将枚举值从模型中分离出来，我们需要使用数据库中的枚举类型来存储它。

## PostgreSQL 枚举类型简介

PostgreSQL 提供了枚举类型来存储一组静态的有序值。让我们看看这是如何在 PostgreSQL 中实现的。使用我们的用例，我们可以创建如下性别类型:

```
CREATE TYPE gender AS ENUM ('male', 'female', 'not_sure', 'prefer_not_to_disclose');
```

创建后，我们可以像使用其他类型一样使用表中的类型。例如，我们可以创建带有性别类型属性的用户表，如下所示。

```
CREATE TABLE users (
    name text,
    gender gender
);
```

通过使用枚举代替整数，我们获得了类型安全。这意味着您不能添加不属于枚举值的值。下面是类型安全功能的一个示例:

```
INSERT INTO users(name, gender) VALUES ('John Doe', 'male');
INSERT INTO users(name, gender) VALUES ('Confused John Doe', 'not_sure');
INSERT INTO users(name, gender) VALUES ('Unknown John Doe', 'unknown');
ERROR: invalid input value for enum gender: "unknown"
```

枚举类型区分大小写，这意味着`male`不同于`MALE`。空白也很重要。

## 将 ActiveRecord::Enum 与 PostgreSQL 枚举类型集成

在对 PostgreSQL 枚举类型使用`ActiveRecord::Enum`之前，我们需要做两件事:数据库迁移和枚举声明。

首先，让我们创建数据库迁移:

```
bundle exec rails generate migration AddGenderToUsers gender:gender
```

接下来，编辑生成的迁移以添加类型:

```
# db/migrate/20150619131527_add_gender_to_users.rb
class AddGenderToUsers < ActiveRecord::Migration
  def up
    execute <<-SQL
      CREATE TYPE gender AS ENUM ('male', 'female', 'not_sure', 'prefer_not_to_disclose');
    SQL

    add_column :users, :gender, :gender, index: true
  end

  def down
    remove_column :users, :gender

    execute <<-SQL
      DROP TYPE gender;
    SQL
  end
end
```

完成后，运行迁移:

```
bundle exec rake db:migrate
```

现在，我们已经完成了数据库迁移。下一步是在用户模型中声明一个 enum。前面，我们使用了数组和哈希形式来声明枚举。为了进行集成，我们需要使用哈希形式声明一个枚举:

```
# app/models/user.rb
class User < ActiveRecord::Base
  enum gender: {
    male:                   'male',
    female:                 'female',
    not_sure:               'not_sure',
    prefer_not_to_disclose: 'prefer_not_to_disclose'
  }
end
```

最后，我们可以使用 PostgreSQL 枚举类型存储`ActiveRecord::Enum`值。作为奖励，所有由`ActiveRecord::Enum`提供的帮助器方法仍然像预期的那样工作。

## 结论

默认情况下，`ActiveRecord::Enum`使用整数存储值。没有模型，值是没有意义的，也不是不言自明的。换句话说，枚举值与模型高度耦合。为了有一个更好的架构，我们需要将枚举值从模型中分离出来。

PostgreSQL 枚举类型为`ActiveRecord::Enum`提供了很好的补充。我们可以有一个有意义的，不言自明的数据，不需要一个模型来破译的价值。利用 PostgreSQL 提供的类型安全特性，我们可以为存储`ActiveRecord::Enum`值打下坚实的基础。

我希望你觉得这很有用。感谢阅读

## 分享这篇文章