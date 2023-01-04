# 擦干你的迁徙

> 原文：<https://www.sitepoint.com/dry-your-migrations/>

Rails 1.1 引入了`Object#with_options`，它允许您使用公共选项来消除方法调用的重复，但是如果我们也能在迁移中使用它不是很好吗？

例如，以下路由代码:

```
 map.connect 'atom.xml', :controller => 'feed', :action => 'everything_atom'
map.connect 'comments-atom.xml', :controller => 'feed', :action => 'comments_atom' 
```

可以使用`Object#with_options`魔法读取:

```
 map.with_options(:controller => 'feed') do |feed|
  feed.connect 'atom.xml', :action => 'everything_atom'
  feed.connect 'comments-atom.xml', :action => 'comments_atom'
end 
```

不错吧？

让我们来看看我最近写的一个迁移:

```
 class AddExcerptSlugAndImageToArticles < ActiveRecord::Migration
  def self.up
    add_column :articles, :excerpt, :text
    add_column :articles, :slug, :string
    add_column :articles, :image, :string
  end

  def self.down
    remove_column :articles, :excerpt
    remove_column :articles, :slug
    remove_column :articles, :image
  end
end 
```

啊…文章，到处都是文章！首先，让我们想出我们希望 API 是什么样子的:

```
 class AddExcerptSlugAndImageToArticles < ActiveRecord::Migration
  def self.up
    with_table :articles do |t|
      t.add_column :excerpt, :text
      t.add_column :slug, :string
      t.add_column :image, :string
    end
  end

  def self.down
    with_table :articles do |t|
      t.remove_column :excerpt
      t.remove_column :slug
      t.remove_column :image
    end
  end
end 
```

啊…好多了。我们如何着手添加这个`with_table`方法呢？首先，我们需要将`with_table`方法添加到`ActiveRecord::Migration`类中:

```
 class ActiveRecord::Migration
  def self.with_table(table_name)
    # Funky magic
  end
end 
```

`with_table`方法将需要`yield`一个对象，它的行为就像迁移一样，但是将表名添加到参数列表的前面。最简单的方法是创建一个新的`Object`，我们将使用它作为代理。我们将定义代理的`method_missing`方法，我们将从该方法调用原始迁移，但是在参数列表的前面添加了表名。如果我们用`add_column :blurb, :string`调用代理，它会用`add_column :articles, :blurb, :string`调用原始迁移。

综上所述，我们最终得出:

```
 class ActiveRecord::Migration
  def self.with_table(table_name)
    proxy = Object.new
    proxy.instance_variable_set(:@migration, self)
    proxy.instance_variable_set(:@table_name, table_name)
    def proxy.method_missing(method_name, *args)
      @migration.send(method_name, *(args.to_a.insert(0, @table_name)))
    end
    yield proxy
  end
end 
```

不错吧？试着把它放在你的一个迁移文件的顶部并进行测试，如果你想阅读更多关于这种方法的内容，请查看《为什么是 Ruby 的痛苦指南》的第 6 章。

## 分享这篇文章