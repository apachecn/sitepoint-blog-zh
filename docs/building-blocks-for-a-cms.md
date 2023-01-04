# CMS 的构建模块

> 原文：<https://www.sitepoint.com/building-blocks-for-a-cms/>

尽管已经有一些尝试在 Rails 中创建高级的可插拔组件，但似乎还没有什么真正流行起来。有 [Rails 引擎](http://rails-engines.org/)和一些[成熟的](http://www.radiantcms.com/) [CMS 软件包](http://rubyforge.org/projects/muravey-tools/)，但是仍然有从头开始构建东西的趋势。这是为什么呢？

我将列出一些低级工具，您可以将它们集成到任何项目中，这些工具不会影响 Rails 的内部功能，并且非常容易设置和使用。

*   **acts_as_tree** or **acts_as_nested_set**

    内置于 Rails 中，这将为您提供任何模型对象的树模型。点击 [LiveTree](http://www.epiphyte.ca/code/live_tree.html) 获得基于 Javascript 的用户界面。

*   **[acts_as_versioned](http://ar-versioned.rubyforge.org/)**

    存储以前的页面，资产或任何你喜欢的危险模型的副本。

*   **[acts_as_versioned_association](http://livsey.org/articles/2006/06/08/acts_as_versioned_association-plugin)**

    在 acts_as_versioned 的基础上，这为您的关联提供了版本控制支持，因此如果您有一个关联了`ImageAssets`的`Page`，您就可以检索您的页面的精确副本以及该时间点的关联图像资产。

*   **Polymorphic associations**

    内置于 Rails 1.1 中的多态关联允许您通过多态连接关联将一个模型与任何其他类型的模型相关联。这意味着您可以在任何模型对象上添加注释，如下例所示:

    ```
     class Comment < ActiveRecord::Base
      belongs_to :commentable, :polymorphic => true
    end 
    ```

    向模型添加注释支持只是简单地插入一个`has_many`一行程序:

    ```
     class Post < ActiveRecord::Base
      has_many :comments, :as => :commentable
    end 
    ```

*   **[acts_as_attachment](http://techno-weenie.net/docs/plugins/acts_as_attachment/)**

    提供文件和基于数据库的上传存储，并支持 ImageMagick 自动调整图像大小。如果这还不够灵活，还有更基于图像操作的 [ImageMagick for Rails 插件](http://vantulder.net/rails/magick/)。

在向 Rails 应用程序中添加类似 CMS 的特性时，您还使用过哪些构建模块？

## 分享这篇文章