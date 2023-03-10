# 使用 neo4j 的 php 应用程序中的高效用户时间线

> 原文：<https://www.sitepoint.com/efficient-user-timelines-php-application-neo4j/>

如今你遇到的任何社交应用都有一个时间轴，通常按照时间降序显示你的朋友或关注者的状态。对于普通的 SQL 或 NoSQL 数据库来说，实现这样一个特性从来都不容易。

查询的复杂性、随着朋友/关注者数量的增加而增加的性能影响以及发展你的社交模型的困难是图形数据库正在消除的点。

在本教程中，我们将分别扩展两篇关于 Neo4j 和 PHP 的介绍文章所使用的演示应用程序:

*   [用 Neo4j 和 PHP 发现图形数据库](https://www.sitepoint.com/discover-graph-databases-neo4j-php/)

*   [使用 Neo4j 为 PHP 应用添加社交网络功能](https://www.sitepoint.com/adding-social-network-features-php-app-neo4j/)

该应用程序建立在 Silex 之上，用户可以跟踪其他用户。本文的目标是高效地对提要的特性进行建模，以便检索您所关注的人的最后两篇帖子，并按时间对它们进行排序。

您将发现一种称为**链表**的特殊建模技术和一些使用 Cypher 的高级查询。

本文的源代码可以在[自己的 Github 库](https://github.com/ikwattro/social-timeline)中找到。

## 在图形数据库中建模时间线

习惯于其他数据库建模技术的人倾向于将每个帖子与用户联系起来。一篇文章会有一个时间戳属性，文章的顺序会根据这个属性来决定。

下面是一个简单的表示:

![Basic user-/>post relationship” title=””></p>
<p>While such a model will work without any problems, there are some downsides to it :</p>
<ul>
<li>For each user, you’ll need to order his posts by time to get the last one</li>
<li>The order operation will grow linearly with the amount of posts and users you follow</li>
<li>It forces the database to execute operations for the ordering</li>
</ul>
<h3 id=](img/8f885ba10bf7e25b9c8c67e676158474.png)利用图形数据库的力量

图数据库中的一个节点持有对他所拥有的连接的引用，为图遍历提供了快速性能。

一种常见的用户提要建模技术叫做**链表**。在我们的应用程序中，用户节点将与用户创建的最后一篇文章有一个名为 *LAST_POST* 的关系。这篇文章将与前一篇文章有一个*前一篇文章*的关系，前一篇文章还与第二篇前一篇文章有一个*前一篇文章*的关系，等等

![Linked list](img/022c8250d7ed5a4e3e61b90c1c644dac.png)

通过这种模式，您可以立即访问用户的最新帖子。事实上，你甚至不需要一个时间戳来检索它的时间线(我们将保留它，以便对不同用户的帖子进行排序)。

更重要的是，用户正在做的事情在图形数据库中以自然的方式建模。能够以与数据在数据库之外的存在方式相对应的方式存储数据，对于分析、查找和理解您的数据是一个真正的好处。

## 初始设置

我建议您下载用于介绍文章的存储库，并将其重命名为 **social-timeline** 例如:

```
git clone git@github.com:sitepoint-editors/social-network
mv social-network social-timeline

cd social-timeline
rm -rf .git
composer install
bower install
```

与前几篇文章一样，我们将在 [Graphgen](http://graphgen.neoxygen.io) 的帮助下使用生成的虚拟数据集加载数据库。

你需要有一个正在运行的数据库(本地或远程)，转到这个[链接](http://graphgen.neoxygen.io/?graph=xyiag)，点击生成，然后点击“填充你的数据库”。

如果您使用 Neo4j 2.2，您需要在 graphgen populator 框中提供`neo4j`用户名和密码:

![Graphgen population](img/81c2f351b8914ab4588a77724965c78a.png)

这将导入 50 个用户，包括登录名、名字和姓氏。每个用户将有两篇博客文章，一篇与用户有 **LAST_POST** 关系，另一篇与另一个提要有 **PREVIOUS_POST** 关系。

如果您现在打开 Neo4j 浏览器，您可以看到用户和帖子是如何建模的:

![Neo4j users and posts relationships](img/c4b384fe9823d1962c1cc00f398cfc13.png)

## 显示用户订阅源

应用程序已经有了一组控制器和模板。你可以选择一个用户点击他们，它会显示他们的追随者和一些建议的人关注。

### 用户反馈路线

首先，我们将添加一个路由来显示特定用户的提要。将这部分代码添加到`web/index.php`文件的末尾

```
$app->get('/users/{user_login}/posts', 'Ikwattro\\SocialNetwork\\Controller\\WebController::showUserPosts')
    ->bind('user_post');
```

### 用户反馈控制器和密码查询

我们将把路线映射到`src/Controller/WebController.php`文件中的一个动作。

在这个操作中，我们将从 Neo4j 数据库中获取给定用户的提要，并将它们与用户节点一起传递给模板。

```
public function showUserPosts(Application $application, Request $request)
    {
        $login = $request->get('user_login');
        $neo = $application['neo'];
        $query = 'MATCH (user:User) WHERE user.login = {login}
        MATCH (user)-[:LAST_POST]->(latest_post)-[PREVIOUS_POST*0..2]->(post)
        RETURN user, collect(post) as posts';
        $params = ['login' => $login];
        $result = $neo->sendCypherQuery($query, $params)->getResult();

        if (null === $result->get('user')) {
            $application->abort(404, 'The user $login was not found');
        }

        $posts = $result->get('posts');

        return $application['twig']->render('show_user_posts.html.twig', array(
            'user' => $result->getSingle('user'),
            'posts' => $posts,
        ));
    }
```

一些解释:

*   我们首先通过用户的登录名来`MATCH`用户。
*   然后我们`MATCH`用户的最后一个提要，并扩展到 PREVIOUS_FEED(使用`*0..2`关系深度将有效地将 latest_post 节点嵌入到 post 节点集合中)，我们将最大深度限制为 2。
*   我们在集合中返回找到的提要。

### 在模板中显示提要

我们将首先在用户配置文件中添加一个链接来访问他们的提要，只需在用户信息块的末尾添加这一行:

```
<p><a href="{{ path('user_post', {user_login: user.property('login') }) }}">Show posts</a></p>
```

我们现在将创建显示用户时间表(帖子)的模板。我们设置了一个标题和一个循环来迭代我们的提要集合，以便在一个专用的 html div 中显示它们:

```
{% extends "layout.html.twig" %}

{% block content %}
    <h1>Posts for {{ user.property('login') }}</h1>

    {% for post in posts %}
        <div class="row">
        <h4>{{ post.properties.title }}</h4>
        <div>{{ post.properties.body }}</div>
        </div>
        <hr/>
    {% endfor %}

{% endblock %}
```

如果您现在选择一个用户并点击 *show user posts* 链接，您可以看到我们的帖子显示得很好，并按时间降序排列，没有指定日期属性。

![A user's feed](img/3197c6cacea13009582a6f1040627428.png)

## 显示时间线

如果您使用 Graphgen 导入了样本数据集，那么您的每个用户都会关注大约 40 个其他用户。

要显示用户时间表，您需要获取他关注的所有用户，并将查询扩展到每个用户的`LAST_POST`关系。

当你得到所有这些帖子时，你需要按时间过滤，以便在用户之间排序。

### 用户时间线路线

流程与上一个相同——我们将路线添加到`index.php`,创建控制器动作，在用户配置文件模板中添加时间线链接，并创建用户时间线模板。

将路线添加到`web/index.php`文件中

```
$app->get('/user_timeline/{user_login}', 'Ikwattro\\SocialNetwork\\Controller\\WebController::showUserTimeline')
    ->bind('user_timeline');
```

控制器动作:

```
public function showUserTimeline(Application $application, Request $request)
    {
        $login = $request->get('user_login');
        $neo = $application['neo'];
        $query = 'MATCH (user:User) WHERE user.login = {user_login}
        MATCH (user)-[:FOLLOWS]->(friend)-[:LAST_POST]->(latest_post)-[:PREVIOUS_POST*0..2]->(post)
        WITH user, friend, post
        ORDER BY post.timestamp DESC
        SKIP 0
        LIMIT 20
        RETURN user, collect({friend: friend, post: post}) as timeline';
        $params = ['user_login' => $login];
        $result = $neo->sendCypherQuery($query, $params)->getResult();

        if (null === $result->get('user')) {
            $application->abort(404, 'The user $login was not found');
        }

        $user = $result->getSingle('user');
        $timeline = $result->get('timeline');

        return $application['twig']->render('show_timeline.html.twig', array(
            'user' => $result->get('user'),
            'timeline' => $timeline,
        ));
    }
```

关于查询的解释:

*   首先我们匹配我们的用户。
*   然后，我们匹配这个用户、他关注的其他用户和他们的最后一个提要之间的路径(在这里可以看到 Cypher 是如何真正表达您想要检索的内容的)。
*   我们根据时间戳对提要进行排序。
*   我们在包含作者和提要的集合中返回提要。
*   我们将结果限制为 20 个提要。

将链接添加到用户配置文件模板，就在用户订阅源链接之后:

```
<p><a href="{{ path('user_timeline', {user_login: user.property('login') }) }}">Show timeline</a></p>
```

并创建时间线模板:

```
% extends "layout.html.twig" %}

{% block content %}
    <h1>Timeline for {{ user.property('login') }}</h1>

    {% for friendFeed in timeline %}
        <div class="row">
        <h4>{{ friendFeed.post.title }}</h4>
        <div>{{ friendFeed.post.body }}</div>
        <p>Written by: {{ friendFeed.friend.login }} on {{ friendFeed.post.timestamp | date('Y-m-d H:i:s') }}</p>
        </div>
        <hr/>
    {% endfor %}

{% endblock %}
```

我们现在有一个非常酷的时间表，可以显示你关注的人的最后 20 个订阅源，这对数据库来说非常有效。

![Timeline implemented](img/01b98a0e058cf49677e1fa570a7ca89d.png)

## 将文章添加到时间线

为了将文章添加到链表中，Cypher 查询要稍微复杂一些。您需要创建帖子节点，删除用户与旧的 latest_post 的 **LAST_POST** 关系，创建最后一个帖子节点与用户之间的新关系，最后创建新旧最后一个帖子节点之间的 **PREVIOUS_POST** 关系。

很简单，不是吗？我们走吧！

像往常一样，我们将为指向 WebController 操作的表单创建 POST 路由:

```
$app->post('/new_post', 'Ikwattro\\SocialNetwork\\Controller\\WebController::newPost')
    ->bind('new_post');
```

接下来，我们将添加一个基本的 HTML 表单，用于在用户模板中插入文章标题和文本:

```
#show_user.html.twig
<div class="row">
        <div class="col-sm-6">
            <h5>Add a user status</h5>
            <form id="new_post" method="POST" action="{{ path('new_post') }}">
            <div class="form-group">
                <label for="form_post_title">Post title:</label>
                <input type="text" minLength="3" name="post_title" id="form_post_title" class="form-control"/>
            </div>
            <div class="form-group">
                <label for="form_post_body">Post text:</label>
                <textarea name="post_body" class="form-control"></textarea>
            </div>
            <input type="hidden" name="user_login" value="{{ user.property('login') }}"/>
            <button type="submit" class="btn btn-success">Submit</button>
            </form>
        </div>
    </div>
```

最后，我们创建了我们的 **newPost** 动作:

```
public function newPost(Application $application, Request $request)
    {
        $title = $request->get('post_title');
        $body = $request->get('post_body');
        $login = $request->get('user_login');
        $query = 'MATCH (user:User) WHERE user.login = {user_login}
        OPTIONAL MATCH (user)-[r:LAST_POST]->(oldPost)
        DELETE r
        CREATE (p:Post)
        SET p.title = {post_title}, p.body = {post_body}
        CREATE (user)-[:LAST_POST]->(p)
        WITH p, collect(oldPost) as oldLatestPosts
        FOREACH (x in oldLatestPosts|CREATE (p)-[:PREVIOUS_POST]->(x))
        RETURN p';
        $params = [
            'user_login' => $login,
            'post_title' => $title,
            'post_body' => $body
            ];
        $result = $application['neo']->sendCypherQuery($query, $params)->getResult();
        if (null !== $result->getSingle('p')) {
            $redirectRoute = $application['url_generator']->generate('user_post', array('user_login' => $login));

            return $application->redirect($redirectRoute);

        }

        $application->abort(500, sprintf('There was a problem inserting a new post for user "%s"', $login));

    }
```

一些解释:

*   我们首先匹配用户，然后可选地匹配他的 **LAST_POST** 节点。
*   我们删除用户和他最近的最后一篇文章之间的关系。
*   我们创建了我们的新帖子(事实上，这是他在现实生活中的最后一个帖子)。
*   我们创建了用户和他的“新”最后一篇文章之间的关系。
*   我们中断查询并传递给用户、最后一篇文章和他的旧的 latest_posts 的集合。
*   然后我们遍历集合，并在新的最后一篇文章和下一篇文章之间创建一个 **PREVIOUS_POST** 关系。

这里棘手的部分是， **oldLatestPosts** 集合将总是包含 0 或 1 个元素，这对于我们的查询来说是理想的。

![New post form](img/707481ab788c9053892111cf1957e562.png)

## 结论

在本文中，我们发现了一种称为链表的建模技术，了解了如何在社交应用程序中实现这一技术，以及如何以有效的方式检索节点和关系。我们还学习了一些新的 Cypher 子句，如 SKIP 和 LIMIT，对分页很有用。

虽然现实世界的时间线比我们在这里看到的要复杂得多，但我希望像 Neo4j 这样的图形数据库显然是这类应用程序的最佳选择。

## 分享这篇文章