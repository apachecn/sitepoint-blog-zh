# 用 Toro 模仿您的传统应用

> 原文：<https://www.sitepoint.com/apify-legacy-app-toro/>

在 2014 年的谷歌代码之夏，我被选中参与一个为 ATutor 创建 REST API 的项目。ATutor 有几十万行代码，却是用核心 PHP 编写的。为 API 引入一个 PHP 路由器类是必要的，但是我们需要一些非侵入性的东西。在本帖中，我们将讨论该项目的主要部分。在这篇文章中，所有代码示例都对应于 ATutor 知识库的 [my fork](https://github.com/sdaityari/ATutor/) (必要时会提供文件链接)。

*注意-[谷歌代码之夏](http://www.google-melange.com/gsoc/homepage/google/gsoc2014)是一个项目，全世界的学生都可以参与到指导组织的开源项目中。谷歌组织该项目并支付津贴，但学生在项目期间的任何时候都不会受雇于谷歌。*

## 使用 Toro 的 Web 路由

这个过程的第一步是创建或编写一个 PHP 类来执行路由。在考虑了几个选项之后，我们决定使用轻量级 PHP 路由器 [Toro](http://toroweb.org/) 。它只执行路由，不多也不少。语法非常直观，您可以在几分钟内开始使用。

Toro 是 RESTful 的——它支持标准的 HTTP 方法——`GET`、`POST`、`PUT`和`DELETE`。也支持基于 JSON 的请求。所有这些都打包在一个 120 多行的文件中。

在我们继续之前，还有一步是配置服务器将所有请求重定向到路由器。这可以通过添加一个。htaccess 文件，或者更改 Nginx 的配置文件。服务器配置的这一步在 Toro 的 GitHub 库的 [README 中有解释。](https://github.com/anandkunal/ToroPHP#server-configuration)

### 你好世界节目

在您成功地配置了您的 web 服务器之后，您只需要包含`Toro.php`文件来执行路由。Toro 匹配传入的 URI 模式，并将请求发送给处理程序进行处理。让我们看看最简单的“Hello World”程序(来自 [Toro 官方网站](http://toroweb.org/))。

```
class MainHandler {
    function get() {
        echo "Hello, world";
    }
}

Toro::serve(array(
    "/" => "MainHandler",
));
```

有两个代码块，看起来非常直观。您向 Toro 提供一个关联数组，键匹配 URL 模式，值包含处理程序类。Toro 尝试将请求 URI 与该数组匹配，并根据 HTTP 方法执行相应的处理程序。在我们的示例中，如果您向“/”(或根目录)发送 get 请求，您将获得所需的输出“Hello，World”。对于`POST`、`PUT`和`DELETE`请求，我们在处理程序类中分别定义了函数`post()`、`put()`和`delete()`。

Toro 给你自由，让你按照自己喜欢的方式编码。您可以在同一个`index.php`文件中编写所有的处理程序，并在关联数组中为 Toro 提供一长串条目。然而，理想的做法是将逻辑分成半项目，并拥有自己的目录、处理程序文件和 URL。在`index.php`中，您可以包含所有这些文件，并使用`array_merge($urls1, $urls2, ...)`向 Toro 提供最终的 URL 数组。

## 将 URL 与逻辑的其余部分分开

理论上，你可以把所有的代码(处理程序、路径、助手类和函数)放在同一个页面上，但是那样会降低可读性。为了简单起见，我们必须将处理程序、路由和助手类和函数分开。事实上，一个好的做法是为您将在项目中创建的不同应用程序创建单独的目录，每个目录都有`urls.php`和`router_classes.php`。视您的项目而定，可能会创建类似`tests.php`或`helper_functions.php`的附加文件。

在`index.php`文件中，我们包含了包含路由的文件，并使用`array_merge`将所有的 URL 合并到 Toro 的一个数组中。下面是[的样子](https://github.com/sdaityari/ATutor/blob/api/api/index.php#L86)。

```
Toro::serve(array_merge(
    $base_urls,
    $course_urls,
    $student_urls,
    $instructor_urls,

    // Include the url array of your app
    $boilerplate_urls
));
```

### URL 前缀的需要

我们已经看到创建单独的`urls.php`文件是有用的，但是这样一个文件应该如何构造呢？

你可能会注意到的一个小事实是，每个应用程序的 URL 都以应用程序的名称开头。因此，一个前缀对于一个应用程序的所有 URL 都是通用的。你应该为一个应用程序定义一个前缀，并将其附加到 URL，这样你就不必重复它了。这是我的项目中一个典型的`urls.php`的样子。

```
$instructor_url_prefix = "/instructors";

$instructor_base_urls = array(
    "/" => "Instructors",
    "/:number/" => "Instructors",
    "/:number/courses/" => "InstructorCourses",
    "/:number/courses/:number" => "InstructorCourses",
    "/:number/courses/:number/instructors" => "CourseInstructorList",
    "/:number/courses/:number/students" => "CourseEnrolledList"
);

$instructor_urls = generate_urls($instructor_base_urls, $instructor_url_prefix);
```

您应该注意到，我偶尔会在 URL 模式中添加一个`:number`。一个`:number`、`:string`或`:alpha`告诉 Toro 将模式与一个数字、字符串或字母数字字符匹配到传入的请求。例如，`/instructors/5/`将匹配第二个 URL，而`/instructors/5/courses/6/`将匹配第四个 URL。

为了给所有的 URL 添加前缀，我创建了一个自定义函数`generate_urls`。你可以在我的核心助手功能下看一下[。](https://github.com/sdaityari/ATutor/blob/api/api/core/api_functions.php#L14)

## 用户认证

您开发的任何 API 都必须能够验证用户。一种方法是使用令牌。以下是关于用户身份验证令牌的一些注意事项。

*   成功登录时生成令牌——在这种情况下，我通过获取用户名、时间戳和随机数的组合散列来生成令牌。
*   通过定义过期时间戳来设置令牌的有效性，过期时间戳之后令牌将无法使用。
*   令牌出现在每个请求的报头中。
*   在每个成功的请求上修改令牌的有效性，并更新到期时间戳。
*   如果令牌已过期，请删除该条目或执行等效任务(如在数据库中设置一个选项，将令牌设为过期)。

## 发展骨干功能

当您开发一个 API 时，您会注意到在实现了前几个函数之后，每个 API 调用的过程都非常相似。用最简单的术语来说，每个 API 调用检查一个令牌是否有效，检查用户是否有权访问该资源，执行一个 SQL 查询(或更多)并打印结果。因此，可以编写主干函数来执行所有这样的查询。以下是这种函数的特征。

*   检查令牌的有效性，并确定哪个用户对应于该令牌
*   检查用户是否可以使用正在访问的资源；在所有其他情况下引发错误。
*   在创建对象的情况下，返回新创建的对象的 ID。
*   如果请求修改或删除对象，请在删除或修改之前检查对象是否存在。
*   打印响应并记录请求。

我为我的项目开发的功能可以在 GitHub 上找到[。](https://github.com/sdaityari/ATutor/blob/api/api/shared/shared_functions.php)

## 重用类来检索相似的对象

假设您正在开发一个 API，您需要在系统中检索用户。看看下面的 URL 类型。

```
"/members/" => "Class1",
    "/members/:number/" => "Class2",
```

第一个 URL 给你一个成员列表，而第二个给你某个成员的详细信息。在这两种情况下执行的 SQL 查询非常相似——在后一种情况下有一个简单的`WHERE`子句。我们可以为这两个 URL 重用同一个类`MySameClass`,定义如下

```
class MyClass {
    function get($member_id) {

        ...

        if ($member_id) {
            $query = $connection->prepare("SELECT col1, col2, col3 FROM table_name WHERE id = :member_id");
            $query->bindParam(':member_id', $member_id, PDO::PARAM_INT);
        }

        // Execute the query or do something else
        $query->execute();
        $result = $query->fetchAll();

        ...
    }
}
```

这里的简单逻辑是，在第一个 URL 的情况下，我的函数中的`$member_id`将为空，而在后一种情况下，将追加`WHERE`子句。

## 最后的想法

在这篇文章中，我讨论了使用 Toro 作为路由器在 PHP 中创建 API 的基本思想。我将重点放在了几个可能会挑战您编写的代码的效率和可读性的地方。我所说的可能不是做某些事情的唯一方法，你可能有更好的方法来做同样的事情。

如果你对我们在这里讨论的内容有更好的想法——从更短到更有效地完成某些任务，请在下面随意评论。

## 分享这篇文章