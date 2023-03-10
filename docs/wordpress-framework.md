# 作为框架的 WordPress

> 原文：<https://www.sitepoint.com/wordpress-framework/>

就用户群和访问的便利性而言，WordPress 是最受欢迎的内容管理系统之一。许多著名的内容丰富的网站使用 WordPress 作为他们的后端——包括 SitePoint 本身。定期更新、更大的社区、强大的支持论坛、丰富的文档和许多其他因素使 WP 遥遥领先于其竞争对手。

当想到 WordPress 时，大多数人首先想到的是博客或内容管理平台，但它还有另一个角度。您是否考虑过它也可以作为开发 web 应用程序的完整框架？

在选择 web 开发框架之前，有许多方面需要考虑。在这篇文章中，我们将研究 WP 提供的特性，如果我们把它当作一个 web 开发框架的话。

### 社区、支持和文档

WordPress 有一个很棒的社区和一个活跃的支持论坛。无论是安装、设置、开发还是其他问题，一切都由[支持](http://WordPress.org/support/)社区及时解决。WordPress [Codex](http://codex.WordPress.org/) 是一个定期更新的社区管理维基。当学习任何新的语言或框架时，文档是你需要的第一件事，而 codex 正好提供了这一点。虽然大多数(如果不是全部的话)其他框架也有这些特性，但值得注意的是 WP 也有，因此不会落后。

### 用户管理

WordPress 有一个优秀的用户管理模块，它负责所有的功能，比如用户注册和登录，用户角色管理，为不同的角色分配能力，以及创建新的角色。通过使用类似于[功能管理器](http://WordPress.org/plugins/capability-manager-enhanced/)的插件可以进一步增强这一功能，它也提供了上述所有功能，而您不必担心会话管理或安全问题。

如果我们谈论其他框架，大多数都提供了某种用户管理解决方案——例如，Yii 提供了一个名为 [Yii-User](http://www.yiiframework.com/extension/yii-user/) 的用户管理扩展，类似地，对于 CodeIgniter 框架，您会发现一个名为 [BackendPro](http://www.kaydoo.co.uk/projects/backendpro) 的用户管理控制面板，您可以轻松地将它集成到您的 CI 项目中。

### 管理仪表板

谁不喜欢他们的应用程序有一个易于使用的管理面板，通过它可以管理前端内容？WP 提供了一个现成的后端面板，里面有你可能需要的大部分选项。除此之外，您还可以创建自己的自定义设置页面，以满足各种需求。另一个好处是你完全不用担心后端的样式——WP 为后端面板提供了自己的一套样式。

管理仪表板是其他框架中缺少的部分。Symfony 和 Yii 为它们各自的框架提供了一些扩展，通过这些扩展，你可以生成管理用户界面，但是功能性部分就留给你了。这是拥有 CMS 的一个优势，但是如果它缺少你需要的特性，并且有你不需要的特性，就会导致很大的开销。

### CRUD(创建读取更新删除)操作

大多数人欣赏框架在执行这些基本操作时的帮助，所以选择一个简单的框架通常是许多开发人员的决定因素。

让我们试着把这和现实世界联系起来。就 WordPress 而言，一切都是帖子。书是帖子，人是帖子，任何可区分的实体都是帖子。WordPress 称之为[自定义文章类型](http://codex.WordPress.org/Post_Types)。

现在让我们以书为例。如果你告诉 WordPress，你想为你的应用程序定义一个名为“Book”的文章类型，你将自动获得与之捆绑的所有 CRUD。

*   添加/更新书籍——这将是添加/更新书籍的默认编辑界面。
*   查看/删除书籍–这也将是带有删除选项的书籍列表页面。

```
 add_action(  'init',  'create_post_type'  );  function create_post_type()  { register_post_type(  'book', array(  'labels'  => array(  'name'  => __(  'Books'  ),  'singular_name'  => __(  'Book'  )  ),  'public'  =>  true,  'has_archive'  =>  true,  )  );  }
```

上面的代码片段完成了所有的幕后工作，CRUD 操作是为 book post 类型生成的。您不必为此编写 SQL 查询，而是将重点放在业务逻辑上，使开发真正快速。

考虑到其他框架，Yii 以其非常容易使用的 GUI [CRUD 生成器](http://www.yiiframework.com/doc/guide/1.1/en/quickstart.first-app)占据主导地位，其次是用于 [CodeIgniter](http://www.grocerycrud.com/examples/the-simplest-example) 的 GroceryCrud 模块，然后是 [CakePHP](http://book.cakephp.org/2.0/en/controllers/scaffolding.html) 中的脚手架。的确，CRUD 操作和数据库关系的细微调整仍然取决于您，但是当想要快速起步并在以后改进时，这些解决方案是首屈一指的。

### 上传和媒体管理

文件上传和媒体管理是所有现代框架的必备功能，因为你想不出如今还有什么应用程序不支持媒体——无论是图像、音乐还是视频。WordPress 有一个集成的媒体库(这些媒体文件不过是我们上面讨论过的*附件*文章类型),通过它你可以上传媒体并执行各种操作，如裁剪、翻转、调整大小等。

WordPress 图片功能允许用户在前端的任何地方使用上传的图片，并且你可以灵活地进行修改。您甚至可以在后端创建一个图像库，并在前端显示它。

如果我们看看 WordPress 之外， [Symfony](http://www.symfony-project.org/plugins/lyMediaManagerPlugin) 和 [Yii](http://www.yiiframework.com/extension/ddmedia) 也为媒体和文件上传管理提供了很酷的扩展，但客观地说，没有一个像 WordPress 一样丰富。这占用了您大量的手工开发时间，使您可以首先关注业务关键部分。

### 扩展性和可伸缩性

从开发人员的角度来看，可扩展性和可伸缩性是每个框架非常重要的一部分。钩子和 T2 过滤器使得 WordPress 成为一个强大的开发框架。

#### 过滤

如果您想要更改某些默认功能的输出，只需将它传递到您的自定义过滤器并输出您想要的任何内容。假设你正在管理一个目录网站(有各种类型书籍的列表),上面有它们各自的价格，你想把每本书的价格提高 10 美元。这将是你要写的过滤器。

```
 add_filter('book_price','custom_book_price');  function custom_book_price($book_price){ $book_price = $book_price +  10;  return $book_price;  }
```

上面的代码将每本书的价格增加 10 美元，并将显示在视图中。过滤器存在于每一个高质量的现代框架和模板引擎中。

#### 钩住

钩子只不过是 WordPress 代码执行链中特定点的自定义函数调用。你甚至可以把它们想象成触发器。假设您想在应用程序中创建新条目时通过电子邮件得到通知。为此，你将把一个自定义函数与 WordPress 的默认`publish_post`挂钩。

`add_action('publish_post','custom_mail_admin');`

现在你需要做的就是实现函数`custom_mail_admin`,每当有新帖子发布时，它就会自动执行。

钩子和过滤器是一对强大的搭档，这也是 WordPress 成功发展成为众多公司的 web 开发框架的主要原因之一。

在某种程度上，所有主流框架都支持钩子，但是触发时间不同。显然，像 Kohana 这样的框架没有“publish_post”钩子，但是可能有一个“post-controller”钩子(它有——以`system.post-controller`的形式),在控制器完成执行它的动作之后执行。

### 网址路由和搜索引擎友好的网址

如果你想建立一个新闻/内容应用或网站，这是一个非常重要的参数。众所周知，URL 结构在 SEO 中起着非常重要的作用，WordPress 有一个强大的[永久链接](http://codex.WordPress.org/Using_Permalinks)功能，通过它你可以根据你的需求为内容或页面设置不同类型的永久链接。

URL 重写在复杂的 web 应用程序中也起着至关重要的作用，在复杂的 web 应用程序中，您的 URL 包含许多 GET 参数，并且 URL 变得太长太复杂。像许多框架一样，WordPress 提供了一个[重写 API](http://codex.WordPress.org/Rewrite_API) ,通过它你可以根据你的需求来处理 URL 结构和定制它，尽管不可否认的是，WP 以一种令人难以置信的用户友好的方式做到了这一点。

几乎所有的框架都支持 URL 路由，其中 [Laravel](http://laravel.com/docs/routing) 提供了一个体面的用户友好的路由 API，其次是 [Yii](http://www.yiiframework.com/doc/guide/1.1/en/topics.url) 、 [Zend](http://framework.zend.com/manual/2.0/en/modules/zend.mvc.routing.html) 、 [CakePHP](http://book.cakephp.org/2.0/en/development/routing.html) 等等。

### 贮藏

如果您正在寻找一个服务于数百万用户的高性能应用程序，您需要有一个合适的缓存机制。WordPress 有一个内置的[瞬态 API](http://codex.WordPress.org/Transients_API) ，它提供数据库级别的缓存功能，可以在你的应用程序中使用。如果你正在寻找一种更高级的缓存机制，将所有的控制都掌握在你的手中，那么像 [W3 Total Cache](http://WordPress.org/plugins/w3-total-cache/) 这样的插件将会服务于这个目的。使用这种方式管理缓存没有太大的开销。

同样，大多数框架都支持这一点。一些例子包括 [Yii](http://www.yiiframework.com/doc/guide/1.1/en/caching.overview) 和 [Laravel](http://laravel.com/docs/cache) ，两者都提供了一个非常简单的缓存机制，可以说比 WordPress’更好，通过它你可以提升你的应用程序的性能。

### 模板

WordPress 模板处理展示你的内容。如果您熟悉 MVC 架构，您可以将模板与视图相关联。一个页面可以分配多个模板。因此，即使数据相同，样式也会不同。

考虑到其他框架， [Laravel](http://laravel.com/docs/templates) 和 [Phalcon](http://docs.phalconphp.com/en/latest/reference/volt.html) 内置了令人惊叹的模板引擎，而其他框架也有自己的解决方案。

### MVC 呢？

如果我们将 WordPress 与市场上的其他框架相比，这些都是使它成为一个完整的 web 应用程序开发框架的主要特征，但是 MVC 呢？如今几乎每个 web 框架都遵循 MVC 架构。事实上，WordPress 并不遵循这种架构，因为它专门支持与旧部署的向后兼容。尽管如此，人们正在努力让 WP 遵循 MVC 模式——你可以看看 [WP MVC](http://wpmvc.org/) 和[蒂娜 MVC](http://WordPress.org/plugins/tina-mvc/) 。

## 结论

WordPress 绝不是其他框架的替代品，而是一个不错的选择，提供了已经流行的框架所包含的大部分(如果不是全部)功能。需要担心的一件事是它的性能和开销，因为你可能并不完全需要你的特定应用程序的功能，但就开发功能而言，WordPress 确实可以与一些最好的框架相媲美，即使它“只是”一个 CMS。

希望你喜欢这篇文章。欢迎评论！

## 分享这篇文章