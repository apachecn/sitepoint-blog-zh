# 在 Rails 中使用迁移的简单指南

> 原文：<https://www.sitepoint.com/using-migrations/>

Rails 中的迁移真的很棒。

如果您以前没有玩过，迁移允许您以原子步骤修改数据库，使升级(和降级)变得非常非常容易。每次运行`script/generate model [model_name]`，都会创建一个具有唯一编号的相应迁移— Rails 2.0.x 会添加一个增加的整数，Rails 2.1+会添加一个时间戳。

```
 001_create_model_name.rb # Old style
20081016230401_create_model_name.rb # New style 
```

当然，迁移脚本并不新鲜，但是 Rails 的不同之处在于它们是抽象的，所以不涉及 SQL(除非您想使用 SQL)。迁移的锅炉板代码看起来有点像这样:

```
 class CreateModelName < ActiveRecord::Migration
  def self.up
    create_table :model_name do |t|

      t.timestamps
    end
  end

  def self.down
    drop_table :model_name
  end
end 
```

`up`和`down`这两个方法非常简单:升级数据库模式时会调用`up`，降级时会调用`down`，如您所见`up`有一个`create_table`方法，而 down 有一个对应的`drop_table`方法。

如果我们的模型`model_name`需要一个`title`、一个`author_id`和`active`标志，那么我们的迁移应该是这样的:

```
 class CreateModelName < ActiveRecord::Migration
  def self.up
    create_table :model_name do |t|
      t.string :title
      t.integer :author_id
      t.boolean :active
      t.timestamps
    end
  end

  def self.down
    drop_table :model_name
  end
end 
```

像这样抽象东西的好处是你不需要担心数据库之间的细微差别。例如，在使用 MySQL 时，boolean 表示为一个小整数，而 SQLite 有一个原生的 boolean 类型。然而，如果您使用 Rails 迁移，您不需要担心这一点:Rails 会为您处理它。

您可能没有意识到的是，完整的 Rails 环境是在运行迁移时加载的，所以您实际上可以运行代码来执行任务，这对于简单的 SQL 来说是非常困难的(甚至是不可能的)。例如，也许您想创建一个额外的列来保存一个文档版本，去掉所有的`html`标签。在这种情况下，您最终可能会进行如下迁移:

```
 class AddStrippedContent < ActiveRecord::Migration
  def self.up
    add_column :stories, :stripped, :text

    Stories.find(:all).each do |s|
      s.stripped = s.content.strip_tags
    end
  end

  def self.down
    remove_column :stories, :stripped
  end
end 
```

我发现这个特性对于设置默认值非常有用，比如默认管理员用户或者默认类别。

最后，要运行迁移，只需键入:

 `rake db:migrate` 

要迁移到特定版本，只需添加一个版本环境变量:

 `VERSION=4 rake db:migrate` 

如您所见，这比记住 SQL 中的等效命令要容易得多！

## 分享这篇文章