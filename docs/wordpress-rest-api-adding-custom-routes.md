# 向 WordPress REST API 添加自定义路线

> 原文：<https://www.sitepoint.com/wordpress-rest-api-adding-custom-routes/>

*这篇关于定制路线的文章是由扭矩杂志原创的[，经允许在此转载。](https://torquemag.io/2015/06/adding-custom-routes-wordpress-rest-api/)*

大多数关于 WordPress REST API 的讨论都是关于查询默认路线的。从这个意义上来说，我们把它当作一个单一的 API——比如 Twitter API。

然而，事实是，WordPress REST API 不是一个 API，而是数百万个高度可定制的 API，它们也可以作为制作 API 的工具。是的，它有默认路线，但是，不可避免的是，这些路线是数千万个网站之间的妥协，包括许多还没有被制作出来的网站。

就像 WordPress 不仅仅是全局的`WP_Query`对象，REST API 也不仅仅是默认的 API。坚持默认就像拥有一个传统的 WordPress 项目，而没有创建自己的`WP_Query`对象，或者在`pre_get_posts`覆盖默认查询。这是可能的，但不是每项工作都可以单独用默认的 WordPress URL 路由来完成。

REST API 也是如此。

在最近与 REST API 的联合首席开发人员瑞安·麦库埃的一次采访中，他谈到了该项目的第二版是如何分成两部分的——默认路由和创建 RESTful APIs 的基础设施。默认路由为如何创建自己的路由提供了很好的示例。

用于添加这些路线和终点的系统做得非常好。在本文中，我将向您展示如何使用它的基本知识；作为一个例子，我将演示如何创建一个带有两个端点的自定义路由，该路由在一个由 Easy Digital Downloads (EDD)支持的电子商务站点中显示产品信息。这个例子基于我为自己的站点构建的一个 API 插件。如果你想在 GitHub 上看到[完整的源代码，或者](https://github.com/CalderaWP/calderawp-api/tree/wordpress)[运行中的 API](https://calderawp.com/wp-json/calderawp_api/v2)，你可以。

尽管 EDD 确实提供了自己的 RESTful API，但我想公开我在自己的网站上使用的特定自定义字段。在我自己的实现中，我还引入了第二个路径“docs”，它包含了我用于文档的自定义 post 类型。

我可能已经能够说服 [EDD API](http://docs.easydigitaldownloads.com/article/544-edd-api-reference) 或者核心 API 的自定义 post 类型和元路由来做我想要做的事情，但是为了简单起见(也为了拥有我所需要的东西),我做了自己的路由和端点。它很快，很有趣，并且在我目前为止已经实现的两个地方都很好。

## 添加路线

### 认识一下我新喜欢的功能

REST API 的第二版引入了一个名为`register_rest_route()`的新函数。这允许您向 REST API 添加一个路由，并传入一个端点数组。对于每个端点，您不仅要提供一个回调函数来响应请求，还可以定义查询中需要的字段，包括默认值、卫生和验证回调，以及一个单独的权限回调。

这里还有一些仍在发展中的附加功能，我推荐阅读默认发布路线的课程。这是一个关于如何使用 REST API 查询文章的很好的资源。

我将集中讨论这三件事:回调、字段参数和权限检查。这三个函数将说明 API 的架构是如何工作的。它们也非常有用，因为一旦你进行回调，你将拥有所有你需要的字段，它们将被清理。您还将知道该请求已被授权。

这种架构加强了关注点的分离，并有助于保持代码的模块化。我不能夸大我有多爱它。

### 设置路线

在定义自定义路由时，在与“rest_api_init”挂钩的函数中使用`register_rest_route()`，这是 REST API 初始化时运行的操作。这是一个重要的动作，可能与“plugin_loaded”和“init”一样有价值

该函数接受四个参数:

第一个是路由的名称空间。所有路由都必须有名称空间，然后在“wp-json”之后用作下一个 URL 段默认路由以 wp 命名。这意味着核心路由具有类似“wp-json/wp/posts”的 url，而命名空间“happy-hats-store”中的自定义路由“size”将具有 URL“WP-JSON/happy-hats-store/size”。

这些名称空间就像 PHP 名称空间，或者插件功能的独特模块。他们避免路线之间的冲突。这样，如果我编写了一个添加名为 menus 的路径的插件，它可以与你编写的添加名为 menus 的路径的插件一起使用——只要我们使用与插件名称对应的不同名称空间。路由的名称空间是一个智能系统，因为很可能两个或更多的开发人员会用相同的名称添加路由。

第二个参数是路由名称空间后面的 URL。在这个例子中，我的第一条路线是“/products”，第二条路线是“/products’。”/(?P <id>[\d]+)。第二个路由允许一个数字，例如最后一个 URL 段中的帖子 ID。这些路由 URL 被加入到名称空间中。因此，如果您的名称空间是“chewbacca-api”，而您的路由是“/products”，那么它的 URL 将是“/WP-JSON/chew bacca-API/products”。"</id>

```
register_rest_route( 'chewbacca-api', '/products', array() ); 
```

在名称空间中包含版本号是一个好习惯。我使用 calderawp_api/v2 作为我的名称空间。

第三个论点是真正的奇迹发生在哪里。这是向路线添加端点的地方。这就是本文的其余部分，所以我们暂时跳过它。

第四个也是最后一个参数是可选的布尔参数，称为“override”它有助于处理与已经定义的路线可能有意或无意发生的冲突。默认情况下，此参数为假，当它为假时，将尝试合并路由。您可以选择将其设置为 true 来替换已经声明的路由。

## 设置您的端点

到目前为止，我们讨论了建立路由，但是路由只有在有端点的情况下才有用。在本文的剩余部分，我们将讨论使用第三个参数`register_rest_route()`向路由添加端点。

### 运输方式

所有端点都需要定义一个或多个 HTTP 传输方法(GET/POST/PUT/DELETE)。通过将一个端点定义为只通过 GET 请求工作，您就告诉了 REST API 从哪里获取正确的数据，以及如何为无效的请求创建错误。

在定义端点的数组中，您在一个名为“methods”的键中定义传输方法类`WP_REST_Server`提供了常量，用于定义要请求的 JSON 主体的传输方法和类型。例如，下面是我们如何定义一个只允许 GET 请求的端点:

```
register_rest_route( 'chewbacca-api', '/products', array(
    'methods'         => WP_REST_Server::READABLE,
) ); 
```

下面是我们如何添加一条接受所有运输方式的路线:

```
register_rest_route( 'chewbacca-api', '/products', array(
    'methods'         => WP_REST_Server::ALLMETHODS,
) ); 
```

使用这些常量，你可以[看到这里的全部](https://github.com/WP-API/WP-API/blob/ab446e34462bfb0bbfb593505962a843ded21d29/lib/infrastructure/class-wp-rest-server.php#L17-L34)，确保随着 REST 服务器的发展，你的路由被正确地设置。

### 定义您的字段

定义端点的一个真正重要的部分是指定您想要的字段:它们的默认值是什么，以及如何对它们进行整理。这允许处理请求的回调函数真正信任它正在检索的数据。

REST API 会为您处理这一切。

下面是我如何设置返回产品集合的字段主端点的示例:

```
register_rest_route( "{$root}/{$version}", '/products', array(
        array(
            'methods'         => \WP_REST_Server::READABLE,
            'callback'        => array( $cb_class, 'get_items' ),
            'args'            => array(
                'per_page' => array(
                    'default' => 10,
                    'sanitize_callback' => 'absint',
                ),
                'page' => array(
                    'default' => 1,
                    'sanitize_callback' => 'absint',
                ),
                'soon' => array(
                    'default' => 0,
                    'sanitize_callback' => 'absint',
                ),
                'slug' => array(
                    'default' => false,
                    'sanitize_callback' => 'sanitize_title',
                )

            ),

            'permission_callback' => array( $this, 'permissions_check' )
        ),
    )

); 
```

您会注意到，其中大多数是数字或布尔字段，所以我使用`absint()`对它们进行了清理。有一个字段可以通过过帐模板进行查询。我对它使用了`sanitize_title`,因为这与它们在被写入数据库之前被清理的方式相同。

我的另一个途径是通过 ID 展示产品。在该路由的端点中，我没有指定任何字段，因为在最后一个 URL 段中传递的 ID 就足够了。

```
register_rest_route( "{$root}/{$version}", '/products' . '/(?P&lt;id&gt;[\d]+)', array(
      array(
         'methods'         =&gt; \WP_REST_Server::READABLE,
         'callback'        =&gt; array( $cb_class, 'get_item' ),
         'args'            =&gt; array(

         ),
         'permission_callback' =&gt; array( $this, 'permissions_check' )
      ),
   )
); 
```

你可以用这些例子来设计你自己的路线。请记住，我的示例是在对象上下文中编写的——也就是说，它们将在类的方法中使用。此外，该方法需要挂钩到“rest_api_init”

### 回调函数

回调函数是您在键“callback”中为每个路由指定的，如果权限回调通过，请求将被分派到该方法。

在我的例子中，我将我的主路由传递给回调类的一个名为“get_items”的方法，并将单个产品路由传递给一个名为“get_item”的方法这遵循核心 post query 类中规定的约定。这很重要，因为我的回调类实际上扩展了核心 API“WP _ REST _ Post _ Controller”中的那个类这允许我在定义自己的路线时吸收它的许多功能。稍后我将在这个函数中讨论处理请求和响应请求。在这个阶段，我们只是定义它是什么。

在上一节中，我向您展示了两个路由注册。两者都有一个“回调”数组，该数组传递用于回调的类的对象和函数的名称。

### 权限回调

像主回调一样，这个方法传递了一个`WP_Request_Class`的对象，它允许您将请求的一部分用于您的身份验证方案。权限回调只需要返回 true 或 false 如何到达那里取决于你自己。

一个策略是使用你在 WordPress 开发中使用的传统的检查当前用户能力类型逻辑。这是可能的，因为权限检查将在设置当前用户后运行，所以如果您使用任何身份验证方法，它们将已经运行。

你根本不需要依赖 WordPress 的当前用户或认证。您可以做的一件事是为您的自定义路由添加特定的授权，并检查正确密钥请求的特定部分。另一种选择是，如果您的站点实现了社交登录，您可以检查 oAuth 密钥，根据该网络对它们进行身份验证，如果通过，则登录与该帐户相关联的用户。

以后我会更多的讨论这些策略。

对于本文中的例子，我展示了如何创建一个公共的只读 API，因此我们可以创建一个总是返回 true 的函数作为我们的权限回调，或者使用 WordPress 的`__return_true`。我选择了第一个选项，所以当我开始添加经过身份验证的 POST 请求时，我已经准备好了。

## 处理和回应请求

每个端点的回调函数将被传递一个`WP_REST_Request`类的对象。有一种方法可以清理和验证请求中的所有数据，并填入默认值。

很多时候，我们只要用`get_params()`这个方法就可以了。这为我们提供了从我们提供的任何传输方法映射的请求的参数。使用这种方法，而不是访问全局的 POST 或 GET 变量，是很重要的，原因有很多。

首先，我们对返回的数组进行了验证和清理。此外，它还处理传输方法之间的切换。这意味着，如果您将端点定义从使用 GET 切换到 PUT(这是一行代码的更改)，回调中的所有代码都会正常工作。

这也导致了更好的抽象。在本文中，我展示了我的 API 附加插件的一个基本版本，但是如果您查看它所基于的插件的源代码，您将会看到对产品和文档端点的所有查询都是由一个抽象类处理的，该类处理创建`WP_Query`参数、循环结果并返回它们。

不管您如何处理您的端点处理，您都希望以一个`WP_REST_Response`类的实例结束。最好的方法是使用函数`ensure_rest_response()`，它返回这个类的一个实例，也可以很好地处理错误。

这个类确保您的响应是正确格式的 JSON，并具有最少的所需头。它还提供了添加额外标头的方法。

在这里，你可以看到我是如何使用它来添加标题的，基于核心发布路线是如何为总结果、页面和上一个/下一个链接添加标题的:

```
/**
 * Create the response.
 *
 * @since 0.0.1
 *
 * @access protected
 *
 * @param \WP_REST_Request $request Full details about the request
 * @param array $args WP_Query Args
 * @param array $data Raw response data
 *
 * @return \WP_Error|\WP_HTTP_ResponseInterface|\WP_REST_Response
 */
protected function create_response( $request, $args, $data ) {
   $response    = rest_ensure_response( $data );
   $count_query = new \WP_Query();
   unset( $args['paged'] );
   $query_result = $count_query-&gt;query( $args );
   $total_posts  = $count_query-&gt;found_posts;
   $response-&gt;header( 'X-WP-Total', (int) $total_posts );
   $max_pages = ceil( $total_posts / $request['per_page'] );
   $response-&gt;header( 'X-WP-TotalPages', (int) $max_pages );

   if ( $request['page'] &gt; 1 ) {
      $prev_page = $request['page'] - 1;
      if ( $prev_page &gt; $max_pages ) {
         $prev_page = $max_pages;
      }
      $prev_link = add_query_arg( 'page', $prev_page, rest_url( $this-&gt;base ) );
      $response-&gt;link_header( 'prev', $prev_link );
   }

   if ( $max_pages &gt; $request['page'] ) {
      $next_page = $request['page'] + 1;
      $next_link = add_query_arg( 'page', $next_page, rest_url( $this-&gt;base ) );
      $response-&gt;link_header( 'next', $next_link );
   }

   return $response;

} 
```

你会注意到我没有讨论如何收集你的数据来回答。如何做到这一点取决于你。

你可以使用`WP_Query`、`wpdb`、`get_post_meta`，或者使用插件的内置功能。这取决于你，这是你的 API。这些已经是你作为 WordPress 开发者的技能了。

在许多情况下，如果您正在将 RESTful API 添加到一个现有的插件或站点中，那么您应该已经拥有了获取所需数据的类。您可以使用 REST API 从 HTTP 请求中获取这些类的参数，然后将结果传递给 REST API 的响应类。

在我的 API 中，我使用`WP_Query`来获取帖子。下面是我用来遍历`WP_Query`对象并获取所需数据的方法:

```
/**
 * Query for products and create response
 *
 * @since 0.0.1
 *
 * @access protected
 *
 * @param \WP_REST_Request $request Full details about the request
 * @param array $args WP_Query args.
 * @param bool $respond. Optional. Whether to create a response, the default, or just return the data.
 *
 * @return \WP_HTTP_Response
 */
protected function do_query( $request, $args, $respond = true) {
   $posts_query  = new \WP_Query();
   $query_result = $posts_query-&gt;query( $args );

   $data = array();
   if ( ! empty( $query_result ) ) {
      foreach ( $query_result as $post ) {
         $image = get_post_thumbnail_id( $post-&gt;ID );
         if ( $image ) {
            $_image = wp_get_attachment_image_src( $image, 'large' );
            if ( is_array( $_image ) ) {
               $image = $_image[0];
            }

         }

         $data[ $post-&gt;ID ] = array(
            'name'         =&gt; $post-&gt;post_title,
            'link'         =&gt; get_the_permalink( $post-&gt;ID ),
            'image_markup' =&gt; get_the_post_thumbnail( $post-&gt;ID, 'large' ),
            'image_src'    =&gt; $image,
            'excerpt'      =&gt; $post-&gt;post_excerpt,
            'tagline'      =&gt; get_post_meta( $post-&gt;ID, 'product_tagline', true ),
            'prices'       =&gt; edd_get_variable_prices( $post-&gt;ID ),
            'slug'         =&gt; $post-&gt;post_name,
         );

         for ( $i = 1; $i &lt;= 3; $i++ ) {
            foreach( array(
               'title',
               'text',
               'image'
            ) as $field ) {
               if ( 'image' != $field ) {
                  $field                       = "benefit_{$i}_{$field}";
                  $data[ $post-&gt;ID ][ $field ] = get_post_meta( $post-&gt;ID, $field, true );
               }else{
                  $field                       = "benefit_{$i}_{$field}";
                  $_field = get_post_meta( $post-&gt;ID, $field, true );
                  $url = false;

                  if ( is_array( $_field ) &amp;&amp; isset( $_field[ 'ID' ] )) {
                     $img = $_field[ 'ID' ];
                     $img = wp_get_attachment_image_src( $img, 'large' );

                     if ( is_array( $img ) ) {

                        $url = $img[0];
                     }

                  }
                  $_field[ 'image_src' ] = $url;
                  $data[ $post-&gt;ID ][ $field ] = $_field;
               }

            }

         }

         return $data;
      }
   }

   if ( $respond ) {
      return $this-&gt;create_response( $request, $args, $data );
   } else {
      return $data;
   }

} 
```

如你所见，它混合了 post 字段、一些元字段和 EDD 定义的函数。同样，如果你想看完整的源代码，包括我是如何添加第二条路线和更多的端点到每条路线的，[去 GitHub 看看](https://github.com/CalderaWP/calderawp-api/tree/wordpress)。

## 自定义 API，比如 Winter，就要来了

WordPress REST API 给你的站点增加了一组有用的默认路线，这一点很棒。这是众所周知的。

更令人兴奋、更棒的是，在创建路线的服务中，我们得到了一个非常棒的 RESTful API 服务器，它为我们创建自己的自定义 API 提供了强大的工具。

所以，下次你不能让默认路线完全按照你想要的那样，就自己做吧。

## 分享这篇文章