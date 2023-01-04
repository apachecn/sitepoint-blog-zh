# WordPress 的高级 OOP:定制 REST API 端点

> 原文：<https://www.sitepoint.com/wordpress-advanced-oop-customizing-rest-api-endpoints/>

*这篇关于 WordPress 的高级 OOP 的文章最初是由扭矩杂志发布的[，经允许在此转载。](https://torquemag.io/2018/03/advanced-oop-wordpress-customizing-rest-api-endpoints-improve-wordpress-search/)*

在过去的几年里，我写了很多关于面向对象 PHP 和 Torque 的 WordPress REST API 的文章。我还提到了使用 Composer 进行依赖管理，提供自动加载器，以及覆盖单元测试。我所写的一切的基本信息是，通过将软件开发的最佳实践应用到我们如何为 WordPress 开发，我们可以创建更好的插件。

这是一系列文章的第一篇，这些文章将把这些概念放在一个实用的、功能性的例子中。我将创建一个 WordPress 插件来修改 WordPress REST API 端点的功能，这样它们可以更好地为搜索进行优化。该插件在 GitHub 上[可用。你可能想要](https://github.com/caldera-learn/rest-api-search)[浏览提交日志](https://github.com/caldera-learn/rest-api-search/commits/master)来看看我是如何把它放在一起的。

在这个系列中，我将介绍使用现代面向对象 PHP 构建插件和类，不仅介绍如何使其可测试，还介绍如何为其编写自动化测试。我将介绍单元测试、集成测试和验收测试之间的区别，并向您展示如何编写和自动运行每种类型的测试。本文以展示如何使用面向对象的方法使用过滤器来修改 WordPress REST API 开始了这个系列。

## 使用 REST API 改进 WordPress 搜索

像 [SearchWP](https://searchwp.com/) 或 [Relevansi](https://wordpress.org/plugins/relevanssi/) 这样的插件，或者与 elastic search——一种使用与 WordPress 完全不同的堆栈的技术——使用 Jetpack 或 ElasticPress 的集成，经常被用来改进 WordPress 搜索。这些类型的插件提供了更好的搜索结果，并经常与分面搜索界面很好地匹配，这对于电子商务应用程序来说非常好。

通过 WordPress REST API 的搜索继承了所有这些相同的问题和相同的解决方案。在这篇文章中，我将从默认情况下搜索是如何工作的开始，以及它的局限性。然后我们将看看如何使用两种不同的方法修改搜索，并与 SearchWP 集成。

WordPress 的内置搜索功能经常需要使用外部服务来改进。虽然这篇文章是关于一种面向对象的方法来修改 WordPress REST API 路由的工作方式，但实际的例子是改进搜索。

当 WordPress 被用作解耦前端的后端时，例如本地移动应用或 web 应用，可能使用 Vue 或 React 或 Angular 构建，通过 REST API 进行高质量搜索是很重要的。如果你的应用程序的用户需要通过基于多种分类法的复杂算法来找到正确的产品变化或搜索内容，本文涵盖的代码将会帮助你，并且你正在编写定制代码，而不仅仅是安装一个插件。

### 用 WordPress REST API 搜索文章

如果您想要搜索一个站点上所有帖子类型为“产品”的帖子，使用搜索词“Taco Shirts ”,您可以向`/wp/v2/product?s=Taco+Shirt`端点发出请求。如果你想提高结果的质量，我上面列出的解决方案会有所帮助。

正如我们上面讨论的，`WP_Query`，WordPress REST API 的 post 端点所使用的，并不是一个很好的搜索工具。更具体地说，`WP_Query`可能是由于它对 MySQL 的依赖，不如使用 NoSQL 数据库构建的专业搜索工具。

首先，让我们看看如果发出 REST API 请求，我们如何绕过`WP_Query`与 WordPress 数据库的交互。

这是许多搜索插件采取的策略，用他们自己的搜索系统的结果来替代`WP_Query`默认生成的结果。搜索系统可以使用相同的数据库。它还可能通过 API 请求连接到其他数据库，例如连接到 ElasticSearch 或 Apache Solr 服务器。

如果[你在 WordPress core](https://github.com/WordPress/WordPress/blob/35ad9e9efcdbbbf234012215a09d82371778dd2f/wp-includes/class-wp-query.php#L2877) 中查找，你会发现过滤器“posts_pre_query”正好在`WP_Query`查询数据库之前运行，但是在 SQL 查询准备好之后。默认情况下，此过滤器返回 null。如果该值为 null，WordPress 将继续其默认行为:查询 WordPress 数据库，并将结果作为一个简单的`WP_Post`对象数组返回。

另一方面，如果这个过滤器的返回值是一个数组——希望包含`WP_Post`对象——那么就不使用 WordPress 的默认行为。

让我们看看如何使用 posts_pre_query 返回一个模拟的`WP_Post`。这种策略对于测试非常有用，但是同样模式的一个更复杂的版本可以用来集成一个单独的数据库和你的 WordPress 站点:

```
/**
 * Replace all WP_Query results with mock posts
 */
add_filter('posts_pre_query',
    function ($postsOrNull, \WP_Query $query) {
        //Don't run if posts are already sent
        if (is_null($postsOrNull)) {
            //Create 4 mock posts with different titles
            $mockPosts = [];
            for ($i = 0; $i <= 3; $i++) {
                $mockPosts[$i] = (new \WP_Post((new \stdClass())));  //Fake post for demonstration, could be any WP_Post
                $mockPosts[$i]->post_title = "Mock Post $i"; //Fake title will be different for each post, useful for testing.
                $mockPosts[$i]->filter = "raw"; //Bypass sanitzation in get_post, to prevent our mock data from being sanitized out.
            }
            //Return a mock array of mock posts
            return $mockPosts;
        }
        //Always return something, even if its unchanged
        return $postsOrNull;
    },
    //Default priority, 2 arguments
    10, 2
); 
```

在这个例子中，我们使用了模拟数据，但是我们也可以使用 SearchWP 的查询类，或者其他任何东西。关于这段代码要记住的另一件事是它将在任何`WP_Query`上运行，而不仅仅是由 WordPress REST API 创建的`WP_Query`对象。让我们通过添加条件逻辑来修改它，这样我们就不使用过滤器，除非它是一个 WordPress REST API 请求:

```
<?php
/**
 * Replace all WP_Query results with mock posts, for WordPress REST API requests
 */
add_filter('posts_pre_query',
    function ($postsOrNull, \WP_Query $query) {
        //Only run during WordPress REST API requests
        if (defined('REST_REQUEST') && REST_REQUEST) {
            //Don't run if posts are already sent
            if (is_null($postsOrNull)) {
                //Create 4 mock posts with different titles
                $mockPosts = [];
                for ($i = 0; $i <= 3; $i++) {
                    $mockPosts[$i] = (new \WP_Post((new \stdClass())));  //Fake post for demonstration, could be any WP_Post
                    $mockPosts[$i]->post_title = "Mock Post $i"; //Fake title will be different for each post, useful for testing.
                    $mockPosts[$i]->filter = "raw"; //Bypass sanitzation in get_post, to prevent our mock data from being sanitized out.
                }
                //Return a mock array of mock posts
                return $mockPosts;
            }
        }
        //Always return something, even if its unchanged
        return $postsOrNull;
    },
    //Default priority, 2 arguments
    10, 2
); 
```

## 修改 WordPress REST API 端点参数

我们刚刚看了如何改变为 WordPress REST API 请求生成搜索结果的方式。这允许我们优化我们的查询以获得更好的搜索，但是它可能会暴露出对端点的不同模式的需求。

例如，如果您希望允许在您的产品端点上进行搜索，以选择性地允许在搜索中包含其他帖子类型，该怎么办？去年，我介绍了解决同一问题的不同方法。

### 贯穿各领域的问题

我们将了解如何修改允许的端点参数，以及如何使用它们来创建`WP_Query`参数。这是两个独立的关注点，单一责任原则说我们需要为每个关注点创建一个类。但是这两个阶层都有共同的担忧。

例如，如果我们希望允许通过不同的帖子类型进行查询，我们需要知道什么是公共帖子类型，以及它们的 slugs 和 rest_base 参数是什么。这是我们可以从函数`get_post_types`中获得的所有信息。

这个函数的输出并不是我们所需要的。因此，让我们设计一个类来根据我刚才列出的需求格式化数据，并为我们提供访问它的帮助方法。

可以把它想象成我们需要的所有 post 类型数据在一个可用容器中的一个公共形状:

```
<?php
/**
 * Class PreparedPostTypes
 *
 * Prepares post types in the format we need for the UsesPreparedPostTypes trait
 * @package ExamplePlugin
 */
class  PreparedPostTypes
{
    /**
     * Prepared post types
     *
     * @var array
     */
    protected $postTypes;
    /**
     * PreparedPostTypes constructor.
     * @param array $postTypes Array of post type objects `get_post_types([], 'objects')`
     */
    public function __construct(array $postTypes)
    {
        $this->setPostTypes($postTypes);
    }
    /**
     * Get an array of "rest_base" values for all public post types
     *
     * @return array
     */
    public function getPostTypeRestBases(): array
    {
        return !empty($this->postTypes) ? array_keys($this->postTypes) : [];
    }
    /**
     * Prepare the post types
     *
     * @param array $postTypes
     */
    protected function setPostTypes(array $postTypes)
    {
        $this->postTypes = [];
        /** @var \WP_Post_Type $postType */
        foreach ($postTypes as $postType) {
            if ($postType->show_in_rest) {
                $this->postTypes[$postType->rest_base] = $postType->name;
            }
        }
    }
    /**
     * Convert REST API base to post type slug
     *
     * @param string $restBase
     * @return string|null
     */
    public function restBaseToSlug(string $restBase)
    {
        if (in_array($restBase, $this->getPostTypeRestBases())) {
            return $this->postTypes[$restBase];
        }
        return null;
    }
} 
```

注意，我们没有在类中调用`get_post_types()`，相反，我们将它作为一个依赖项，通过构造函数注入。因此，这个类可以在不加载 WordPress 的情况下进行测试。

这就是为什么我将这个类描述为“单元可测试的”。它不依赖其他 API，我们也不担心副作用。我们可以把它作为一个独立的单元来测试。一旦我们有了单元测试覆盖率，将关注点和功能分离成小的部分使得代码可维护。我将在下一篇文章中讨论如何测试这种类。

记住这个类确实依赖于`WP_Post_Type`。我的单元测试不会定义这个类，因为只有集成测试才会有 WordPress 或任何其他可用的外部依赖。那个类只用来表示数据，不执行任何操作。因此，我们可以说它的使用不会产生副作用。因此，在单元测试中，我很乐意使用模拟来代替真实的`WP_Post_Type`。

说到依赖注入，需要这个新类的对象的类，我们希望遵循相同的模式。我们将传入一个实例，而不是在需要它们的类中实例化`PreparedPostTypes`。这意味着使用`PreparedPostTypes`和`PreparedPostType`的类保持隔离，可以单独测试。

这也可能导致代码重用，因为我们必须使依赖注入成为可能，并拥有该对象的属性。我们可以使用剪切和粘贴，或者我们可以使用 PHP Trait，这是一种在类之间复制方法和属性的更好的可伸缩方式。

这里有一个特征，它为我们如何将`PreparedPostTypes`对象注入其他类建立了一个模式

```
<?php
/**
 * Trait UsesPreparedPostTypes
 * @package ExamplePlugin
 */
trait UsesPreparedPostTypes
{
    /**
     * Prepared post types
     *
     * @var PreparedPostTypes
     */
    protected $preparedPostTypes;
    /**
     * UsesPreparedPostTypes constructor.
     * @param PreparedPostTypes $preparedPostTypes
     */
    public function __construct(PreparedPostTypes $preparedPostTypes)
    {
        $this->preparedPostTypes = $preparedPostTypes;
    }
} 
```

我们的另一个关注点是，我们需要了解多个地方的帖子类型的一些情况。例如 post 类型的 slug。这是一个与前一个不同的跨领域问题。我们解决的最后一个问题涉及动态数据。现在我们只需要一个地方来改变我们在多个地方使用的一两个字符串。

有类常数的类简单地解决了这个问题:

```
<?php
/**
 * Class PostType
 *
 * Post type whose POST wp/v2/<post-type-rest_base> we are hijacking
 *
 */
class PostType
{
    /**
     * Post type slug
     *
     * @TODO Change this to your post type's slug
     */
    const SLUG = 'post';
    /**
     * Post type rest_base
     *
     * @TODO Change this to your post type's rest_base
     */
    const RESTBASE = 'posts';
} 
```

现在我们可以在整个代码中保持这些字符串的一致性。这似乎是一个不必要的步骤。但是我的示例代码适用于 posts post 类型。如果你想改变文章的类型，这个类需要改变，其他都不需要改变。这遵循了 Tom McFarlin 对单一责任原则的定义，他写道:“[一个类应该只有一个改变的理由。](https://tommcfarlin.com/single-responsibility-principle-wordpress/)

### 修改 REST API 端点模式

现在我们需要修改 post 类型端点的模式。通过这样做，WordPress 将通知 REST API 端点允许使用`post_type`参数，并且当请求被解析时，允许使用新的端点参数。

下面是我们添加了`post_type`属性的类。注意，它使用了我们刚刚讨论过的特征`UsesPreparedPostTypes`:

```
<?php
/**
 * Class ModifySchema
 *
 * Modifies the REST API route schema so it has an argument "post_type"
 *
 *
 * @package ExamplePlugin
 */
class ModifySchema
{
    use UsesPreparedPostTypes;
    /**
     * The name of the extra argument we are adding to post type routes
     */
    const ARGNAME = 'post_type';
    /**
     * Add post_type to schema
     *
     * @uses ""rest_{$postType}_collection_params" action
     *
     * @param array $query_params JSON Schema-formatted collection parameters.
     * @param \WP_Post_Type $post_type Post type object.
     *
     * @return array
     */
    public function filterSchema($query_params, $post_type)
    {
        if ($this->shouldFilter($post_type)) {
            $query_params[self::ARGNAME] = [
                [
                    'default' => PostType::RESTBASE,
                    'description' => __('Post type(s) for search query'),
                    'type' => 'array',
                    //Limit to public post types and allow query by rest base
                    'items' =>
                        [
                            'enum' => $this->preparedPostTypes->getPostTypeRestBases(),
                            'type' => 'string',
                        ],
                ]
            ];
        }
        return $query_params;
    }
    /**
     * Check if this post type's schema should be filtered
     *
     * @param \WP_Post_Type $WP_Post_Type
     * @return bool
     */
    public function shouldFilter(\WP_Post_Type $WP_Post_Type): bool
    {
        return PostType::SLUG === $WP_Post_Type->name;
    }
} 
```

在这个属性的设置中，我们告诉 WordPress 这个属性是一个数组属性，我们使用数组的“枚举”索引来指定允许的值。

在“枚举”中，我们列举了允许的值。在这种情况下，`PreparedPostTypes`类提供了允许值的数组，因为这是一个之前已经解决的横切关注点。

请注意，这个类不与任何 post 类型耦合，甚至不与这个特定的用例耦合。我们将讨论如何根据我们的实际需求来实现这一点。将这个类从 WordPress 插件 API 中分离出来，使得它可以重用和单元测试。我们确实需要测试与 WordPress 的交互，但是我们会在集成测试中涵盖这一点。我将在以后的文章中告诉你如何做。

我们将很快回到使用什么钩子来使这个类为特定的文章类型工作。

### 修改 REST API `WP_Query`参数

上一节展示了如何使一个新的端点属性`post_type`可用。这实际上并没有改变 WordPress REST API 生成的`WP_Query`参数。除了最后一个过滤器，我们什么都有了。

Post 类型是核心[明确禁止通过 REST API 请求更改](https://github.com/WordPress/WordPress/blob/35ad9e9efcdbbbf234012215a09d82371778dd2f/wp-includes/rest-api/endpoints/class-wp-rest-posts-controller.php#L251)的一个`WP_Query`参数。我们有一个动态命名的过滤器——`rest_{$post_type}_query`——可以覆盖任何`WP_Query`参数。

下面是我们的类，它注入了我们的`post_type`参数，这在以前是不允许的:

```
<?php
/**
 * Class ModifyQuery
 *
 * Modify WP_Query Args
 *
 * @package ExamplePlugin
 */
class  ModifyQueryArgs
{
    use UsesPreparedPostTypes;
    /**
     * Filter query args if needed
     *
     * @param array $args Key value array of query var to query value.
     * @param \WP_REST_Request $request The request used.
     *
     * @return array
     */
    public function filterQueryArgs($args, $request)
    {
        if ($this->shouldFilter($request)) {
            add_filter('posts_pre_query', [FilterWPQuery::class, 'posts_pre_query'], 10, 2);
            $args['post_type'] = $this->restBasesToPostTypeSlugs($request[ModifySchema::ARGNAME]);
        }
        return $args;
    }
    /**
     * Check if we should filter request args
     *
     * @param \WP_REST_Request $request
     * @return bool
     */
    public function shouldFilter(\WP_REST_Request $request): bool
    {
        $attributes = $request->get_attributes();
        if (isset($attributes['args'][ModifySchema::ARGNAME])) {
            if ($request->get_param(ModifySchema::ARGNAME)) {
                return true;
            }
        }
        return false;
    }
    /**
     * Convert an array of rest bases to post type slugs
     *
     * @param array $postTypes
     * @return array
     */
    public function restBasesToPostTypeSlugs(array $postTypes): array
    {
        $postTypeSlugs = [];
        foreach ($postTypes as $postTypeRestBase) {
            if ($this->preparedPostTypes->restBaseToSlug($postTypeRestBase)) {
                $postTypeSlugs[] = $this->preparedPostTypes->restBaseToSlug($postTypeRestBase);
            }
        }
        return $postTypeSlugs;
    }
} 
```

大部分只是验证我们是否应该进行更改，然后使用`WP_Rest_Request`的 `get_param`方法从请求中获取值。大部分都是自动的，因为我们首先修改模式进行匹配。

### 为 WordPress REST API 请求修改`WP_Query`对象

我已经在这篇文章的第一部分展示了如何做到这一点。下面是一个实现相同模式的类:

```
<?php
/**
 * Class FilterWPQuery
 *
 * Changes WP_Query object
 *
 * @package ExamplePlugin
 */
class FilterWPQuery
{
    /**
     * Demonstrates how to use a different way to set the posts that WP_Query returns
     * @uses "posts_pre_query"
     *
     * @param $postsOrNull
     * @param \WP_Query $query
     * @return mixed
     */
    public static function posts_pre_query($postsOrNull, $query)
    {
        //Only run during WordPress API requests
        if (defined('REST_REQUEST') && REST_REQUEST) {

            //Prevent recursions
            remove_filter('posts_pre_query', [FilterWPQuery::class, 'posts_pre_query'], 10);
            //Don't run if posts are already sent
            if (is_null($postsOrNull)) {
                 //Create 4 mock posts with different titles
                $mockPosts = [];
                for ($i = 0; $i <= 3; $i++) {
                    $mockPosts[$i] = (new \WP_Post((new \stdClass())));
                    $mockPosts[$i]->post_title = "Mock Post $i";
                    $mockPosts[$i]->filter = "raw";
                }
                //Return a mock array of mock posts
                return $mockPosts;
            }
            //Always return something, even if its unchanged
            return $postsOrNull;
        }
    }
}
 init.php
<?php
/**
 * Make this all work
 */
add_action('init', function () {
    $postType = PostType::SLUG;
    $preparedPostType = new PreparedPostTypes(get_post_types([], 'objects'));
    $modifySchema = new ModifySchema($preparedPostType);
    add_filter("rest_{$postType}_collection_params", [$modifySchema, 'filterSchema'], 25, 2);
    $modifyQuery = new ModifyQueryArgs($preparedPostType);
    add_filter("rest_{$postType}_query", [$modifyQuery, 'filterQueryArgs'], 25, 2);
}); 
```

我希望你注意到这段代码与 WordPress 紧密相连，并且不可测试。它使用来自 WordPress 的`WP_Post`,检查来自 WordPress 的常量，并与 WordPress 的插件 API 交互。我们可以模仿`WP_Post`，我们可以自己设置常量。但是插件的 API——这是一个需要测试的基本特性。在我接下来的几篇文章中，我将介绍如何重构这个类，这样我们就可以使用单元测试来涵盖除了移除过滤器的影响之外的所有内容，并使用集成测试来检查这种影响。

我选择使用静态方法有两个原因。首先，它使得在多个位置添加和删除它变得容易。例如，在类`ModifyQuery`中，我只在需要的时候挂钩这个过滤器:

```
add_filter('posts_pre_query', [FilterWPQuery::class, 'posts_pre_query'], 10, 2);
            $args['post_type'] = $this->restBasesToPostTypeSlugs($request[ModifySchema::ARGNAME]);
        }
        return $args;
    }
    /**
     * Check if we should filter request args
     *
     * @param \WP_REST_Request $request
     * @return bool
     */
    public function shouldFilter(\WP_REST_Request $request): bool
    {
        $attributes = $request->get_attributes();
        if (isset($attributes['args'][ModifySchema::ARGNAME])) {
            if ($request->get_param(ModifySchema::ARGNAME)) {
                return true;
            }
        }
        return false;
    }
    /**
     * Convert an array of rest bases to post type slugs
     *
     * @param array $postTypes
     * @return array
     */
    public function restBasesToPostTypeSlugs(array $postTypes): array
    {
        $postTypeSlugs = [];
        foreach ($postTypes as $postTypeRestBase) {
            if ($this->preparedPostTypes->restBaseToSlug($postTypeRestBase)) {
                $postTypeSlugs[] = $this->preparedPostTypes->restBaseToSlug($postTypeRestBase);
            }
        }
        return $postTypeSlugs;
    }
}
 ModifySchema.php
<?php
/**
 * Class ModifySchema
 *
 * Modifies the REST API route schema so it has an argument "post_type"
 *
 *
 * @package ExamplePlugin
 */
class ModifySchema
{
    use UsesPreparedPostTypes;
    /**
     * The name of the extra argument we are adding to post type routes
     */
    const ARGNAME = 'post_type';
    /**
     * Add post_type to schema
     *
     * @uses ""rest_{$postType}_collection_params" action
     *
     * @param array $query_params JSON Schema-formatted collection parameters.
     * @param \WP_Post_Type $post_type Post type object.
     *
     * @return array
     */
    public function filterSchema($query_params, $post_type)
    {
        if ($this->shouldFilter($post_type)) {
            $query_params[self::ARGNAME] = [
                [
                    'default' => PostType::RESTBASE,
                    'description' => __('Post type(s) for search query'),
                    'type' => 'array',
                    //Limit to public post types and allow query by rest base
                    'items' =>
                        [
                            'enum' => $this->preparedPostTypes->getPostTypeRestBases(),
                            'type' => 'string',
                        ],
                ]
            ];
        }
        return $query_params;
    }
    /**
     * Check if this post type's schema should be filtered
     *
     * @param \WP_Post_Type $WP_Post_Type
     * @return bool
     */
    public function shouldFilter(\WP_Post_Type $WP_Post_Type): bool
    {
        return PostType::SLUG === $WP_Post_Type->name;
    }
} 
```

此外，在使用这个过滤器时，很容易产生递归循环。能够很容易地删除它，就像这个例子中的代码是非常好的。

我选择使用静态方法的另一个原因是函数与其他 API 交互。它永远不会是真正的单元可测试的。这种模式，一个具有静态方法的类，使得在集成测试中模仿该类变得非常容易，从而最小化了在系统的这一部分中没有强隔离的影响。

## 让这一切一起工作

到目前为止，我们看到的代码与 WordPress 是完全分离的。那有很多好处。但这意味着它不做任何事情。那很好。到目前为止，我们只处理了业务逻辑需求。现在我们需要看看整合。

那也不算太难，加几个钩子就行了。哪些钩子？和我们设计的`ModifyQuery`和`ModifySchema`类完全一样的两个钩子。对分离业务逻辑的渴望并不意味着我们在设计它的公共接口时不能想到我们编写代码的真正原因。否则，我们只会毫无理由地增加代码的复杂性。

总的来说，我试图只增加软件的复杂性，当它使生活变得更容易的时候。我过去曾偏离这条道路。我们都有，没关系，练习宽恕。

我们将要挂钩的类中的方法使用与挂钩完全相同的参数和返回类型。他们的工作是将这些值发送给其他组件。

```
<?php
/**
 * Make this all work
 */
add_action('init', function () {
    $postType = PostType::SLUG;
    $preparedPostType = new PreparedPostTypes(get_post_types([], 'objects'));
    $modifySchema = new ModifySchema($preparedPostType);
    add_filter("rest_{$postType}_collection_params", [$modifySchema, 'filterSchema'], 25, 2);
    $modifyQuery = new ModifyQueryArgs($preparedPostType);
    add_filter("rest_{$postType}_query", [$modifyQuery, 'filterQueryArgs'], 25, 2);
}); 
```

## 接下来:测试

这对于爵士乐来说已经够接近了。会有用的。由于缺少一个添加钩子的正式系统，这是我们能做的最好的初始化。那很好。我将在以后关于 WordPress 集成测试的文章中介绍如何创建一个更复杂和可扩展的引导过程。

在这篇文章中，我们已经看到了创建代码来修改模式、`WP_Query`参数生成和文章类型的底层`WP_Query`。我鼓励你把这段代码变成一个插件，使用 Composer 作为自动加载器。在我的[的下一篇文章](https://torquemag.io/2018/04/advanced-oop-for-wordpress-part-2-setting-up-automated-testing-of-wordpress-rest-api-plugins/)中，我们将看看涵盖这些类的单元测试。

## 分享这篇文章