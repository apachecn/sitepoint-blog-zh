# 你可以用脸书图形 API 做两件很酷的事情

> 原文：<https://www.sitepoint.com/2-cool-things-can-facebook-graph-api/>

2011 年 12 月，[脸书正式弃用其 REST API](https://developers.facebook.com/blog/post/616/) ，并推出了 [Graph API](https://developers.facebook.com/docs/graph-api) 供公众使用。从那以后，所有的新特性都被添加到 Graph API 中，脸书使用户能够执行许多新的事情，其中一些我们今天将会讨论。在这篇文章中，我们将向 Graph API 发出大量请求，接收 JSON 响应，从而操纵它们以获得我们想要的结果。

## 访问令牌

大多数对 Graph API 的请求需要一个访问令牌作为参数。对于登录用户或页面和发出请求的脸书应用程序的组合，访问令牌是唯一的。

令牌与脸书应用程序相关联，用于处理用户授予该应用程序的权限。这定义了可通过访问令牌访问的资源。因此，令牌提供了对脸书的临时且安全的访问。您可以从图形浏览器的[中获得一个访问令牌。令牌可能有也可能没有退出时间，具体取决于它们是短期令牌还是长期令牌。短期令牌到期后，您需要重新对用户进行身份认证，并获取新令牌。](https://developers.facebook.com/tools/explorer/)

## 通过脸书网页的竞赛

近来，小型和新兴的组织已经有效地使用脸书网页来推广他们的内容。然而，获得“喜欢”,从而增加你的影响力，是一个缓慢而稳定的过程。许多人为此求助于脸书的广告。然而，许多其他人选择了一种更便宜的方式——通过他们的网页组织竞赛。

通常的竞赛包括在网页上张贴一张照片或一段经历。比赛是根据帖子或照片的“赞”数来评判的。参加比赛还有一个额外的条件。为了让“赞”有效，用户也需要喜欢这个页面。脸书没有任何内置功能来告诉你有多少人喜欢你的帖子和页面。这使得评判比赛变得困难。

非程序员会觉得评判比赛的唯一方法是手动交叉检查喜欢的数量。幸运的是，Graph API 可以帮助我们毫不费力地执行这个操作。

虽然我将通过 Python 来执行这个操作，但是这个过程对于其他语言来说是一样的。重要的部分是我们向其发送请求的目标 URL 以及我们从接收到的 JSON 中获得的数据。

从概念上讲，一个简单的方法是获取帖子上的喜欢列表和页面上的喜欢列表，然后对它们进行比较。然而，[到目前为止，脸书还没有在页面](https://www.facebook.com/help/community/question/?id=10152948879270451)上获取喜欢列表的功能。我们将使用相反的过程来检查帖子上的每个赞是否也喜欢该页面。

下面的调用检查用户是否喜欢某个页面。详细文档可在[这里](https://developers.facebook.com/docs/graph-api/reference/user/likes/#readmodifiers)获得。

```
GET /{user-id}/likes/{page-id}
```

如果用户喜欢这个页面，JSON 响应包含关于这个页面的`data`，但是如果用户不喜欢这个页面，就会收到一个空的`data`。我们使用下面的函数来确定一个用户是否喜欢一个页面。

```
def user_likes_page(user_id, page_id):
    """
        Returns whether a user likes a page
    """
    url = 'https://graph.facebook.com/%d/likes/%d/' % (user_id, page_id)
    parameters = {'access_token': TOKEN}
    r = requests.get(url, params = parameters)
    result = json.loads(r.text)
    if result['data']:
        return True
    else:
        return False
```

下一步是获取特定帖子的点赞列表，并找出用户是否也喜欢该页面。以下调用为我们提供了一个帖子的点赞列表，前提是我们有适当的访问权限。

```
GET /{post-id}/likes/
```

结合这两个想法，我们制作了下面的函数来检查一篇文章中有多少赞是该页面共有的。

```
def get_common_likes(post_id, page_id):
    """
        Returns the number of likes common to a post and the page
    """
    count_likes = 0
    url = 'https://graph.facebook.com/%d/likes/' % post_id
    parameters = {'access_token': TOKEN}
    r = requests.get(url, params = parameters)
    result = json.loads(r.text)
    for like in result['data']:
        if user_likes_page(int(like['id']), page_id):
            count_likes += 1
            print 1
    return count_likes
```

## 大量回复您时间线上的帖子

在你生日那天，我肯定你会收到数百(如果不是数千)条帖子。回复每一个都很繁琐！很多人放了一个状态感谢每个人的祝福，而其他人更喜欢亲自感谢每个人。让我们看看如何选择个人选项，并在短时间内完成。

获取用户或页面的[提要](https://developers.facebook.com/docs/graph-api/common-scenarios/#statusupdates)的调用如下。

```
GET /{user-id}/feed
```

如果你想在你的时间线上获得帖子，你可以用“我”代替`{user-id}`，这样会让这个过程看起来更容易。要处理成百上千的帖子，你不可能在一个页面上就能看到。您需要进一步检查 JSON 响应中的`next` url。

获取时间轴上所有文章的函数如下。

```
def get_posts():
    """
        Returns the list of posts on my timeline
    """

    parameters = {'access_token': TOKEN}
    r = requests.get('https://graph.facebook.com/me/feed', params=parameters)
    result = json.loads(r.text)
    return result['data']
```

下一步是在您的时间线上发布评论。用于执行该动作的调用如下。

```
POST /{object-id}/comments
```

该评论应在上面的`POST`请求中作为`message`发送。所以，我们用来评论文章的函数如下。

```
def comment_on_posts(posts):
    """Comments on all posts"""
    for post in posts:
        url = 'https://graph.facebook.com/%s/comments' % post['post_id']
        message = 'Commenting through the Graph API'
        parameters = {'access_token': TOKEN, 'message': message}
        s = requests.post(url, data = parameters)
```

我为这两者使用的脚本可以在 GitHub 上找到[。此外，您可以更进一步，同时发出多个 API 请求](https://github.com/sdaityari/facebook-graph-api)[。](https://developers.facebook.com/docs/graph-api/making-multiple-requests/)

### 另一种方法

Quora 上的 Akshit Khurana，[通过使用](https://www.quora.com/Python-programming-language-1/What-are-the-best-Python-scripts-youve-ever-written/answer/Akshit-Khurana)[脸书查询语言(FQL)](https://developers.facebook.com/docs/technical-guides/fql/) 讨论了另一种方法。FQL 是一种类似 SQL 的语言，允许您查询通过 Graph API 接收的数据。有一个[表列表](https://developers.facebook.com/docs/reference/fql/)，每个表都有自己可以查询的列列表，从而帮助你过滤数据。

## 结论

自从 Graph API 发布以来，脸书一直在努力工作，新的特性也在不断地加入其中。如果您计划使用与脸书相关联的移动或 web 应用程序，那么 Graph API 的使用是必不可少的。脸书还维护了大量的文档，详细解释了 Graph API 的特性和各种用途。

## 分享这篇文章