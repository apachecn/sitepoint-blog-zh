# 建立 Twitter 标签竞赛——创建和统计推文

> 原文：<https://www.sitepoint.com/building-twitter-hashtag-contest-creating-counting-tweets/>

在这个系列的第一部分中，我们研究了 Twitter 上各种类型的竞赛，并选择开发一个标签竞赛，因为它是最受欢迎的竞赛类型之一，并且不依赖于运气。到目前为止，我们已经使用 Twitter 应用程序验证了我们的用户。今天，我们将继续为我们的用户实现 tweet 功能。

让我们开始吧。

### 初始化 Tweet 屏幕

我们的首要任务是将用户的详细信息分配到一个浏览器会话中，允许用户多次发推，而无需为每次发推授权应用程序。下面的代码将详细信息添加到会话中，并初始化 tweet 屏幕。

```
public function createUserSession($credentials){

  $_SESSION['user_id'] = $credentials["user_id"];
  $_SESSION['user_token'] = $credentials["oauth_token"];
  $_SESSION['user_secret'] = $credentials["oauth_token_secret"];

  $this->initializeTweetScreen();
} 
```

让我们来看看用 HTML 表单实现 tweet 屏幕。

```
public function initializeTweetScreen($message = ''){

  $html = "<div>".$message."</div>
           <form action='' method='POST' >
             <textarea name='post_tweet' id='post_tweet'></textarea>
             <input name='submit_tweet' type='submit' value='Tweet' />
            </form>";

  echo $html;
} 
```

现在，用户可以使用简单的 HTML 表单提交推文。

### 创建推文

一旦用户提交表单，我们需要使用这些提交的内容在他们的 Twitter 个人资料上自动创建推文。我们需要用以下代码更新我们的控制器代码:

```
if (isset($_SESSION['user_id'])) {
    if (isset($_POST['submit_tweet'])) {
        $tweet_text = isset($_POST['post_tweet']) ? $_POST['post_tweet'] : '';
        $message = $contest->createTweet($tweet_text);
        $contest->initializeTweetScreen($message);
    } else {
        $contest->initializeTweetScreen();
    }
}
```

这会拦截 POST 请求并添加 tweet 内容。我们将使用一个名为`createTweet`的函数来访问 Twitter API，如下所示。

```
public function createTweet($tweet_text) {
    $error = "";
    $hash_tags = " #SPC #twcontesttutorial ";
    if (empty($tweet_text) || strlen($tweet_text . $hash_tags) > 140) {
        $error .= "Tweet text should contain 1-140 characters.";
    }
    if (empty($error)) {

        $twitter_auth = $this->initializeTwitterObject();
        $code = $twitter_auth->request('POST', $twitter_auth->url('1.1/statuses/update'), array('status' => $tweet_text . $hash_tags));
        if ($code == 200) {
            $response = json_decode($twitter_auth->response["response"]);
            return "Tweet Created Successfully";
        }
    } else {
        return $error;
    }
}
```

正如我们在规划部分所讨论的，每条推文将使用两个标签`#SPC`和`#twcontesttutorial`。接下来，我们将检查 tweet 内容中的错误。一旦成功验证，我们使用`initializeTwitterObject`函数创建 Twitter 库对象，如下面的代码所示。

```
public function initializeTwitterObject() {

    $this->app_details = array(
        'consumer_key' => $this->config['consumer_key'],
        'consumer_secret' => $this->config['consumer_secret'],
        'user_token' => $_SESSION['user_token'],
        'user_secret' => $_SESSION['user_secret']
    );

    $twitter_auth = new tmhOAuth($this->app_details);
    return $twitter_auth;
}
```

在这种情况下，对象将用`user_token`和`user_secret`初始化，而不是仅仅使用`consumer_key`和`consumer_secret`。

接下来，我们使用`1.1/statuses/update` API URL 执行 tweet 请求。我们使用 status 参数传递用户提交的文本以及我们的 hashtags。一旦请求成功，我们向用户显示消息以完成该过程。

现在，用户可以授权该应用程序，并创建他们想要的任何数量的推文。所有的推文都会有预定义的标签。

### 显示竞赛结果

比赛结果将根据转发次数计算。我们必须使用 Twitter 搜索 API 函数来获得想要的结果。让我们来看看更新后的代码。

```
if(isset($_GET['action']) && $_GET['action'] == 'results'){    
  $contest->getContestResults();
} 
```

现在我们来看看`getContestResults`函数。

```
public function getContestResults() {

    $twitter_auth = $this->initializeTwitterObject();
    $search_params = array('q' => '#SPC #twcontesttutorial -RT', 'count' => 100, 'result_type' => 'recent');
    $this->retrieveTweets($twitter_auth, $search_params);

    $contest_results = array();
    $html = "<table>";
    $html .= "<tr><td>Username</td><td>Retweets</td></tr>";

    foreach ($this->result_tweets as $key => $value) {
        $username = $value->user->screen_name;
        $contest_results[$username] = isset($contest_results[$username]) ? ($contest_results[$username] + $value->retweet_count) : $value->retweet_count;
    }

    foreach ($contest_results as $key => $res) {
        $html .= "<tr><td>" . $key . "</td><td>" . $res . "</td></tr>";
    }

    $html .= "</table>";
    echo $html;
}
```

首先，我们定义搜索所需的参数。搜索查询将是两个标签的组合。我们还使用了`-RT`来防止对转发的计数，因为转发不应该被计为应用程序生成的 tweet。

`count`参数定义了每页的记录数。

`result_type`参数定义了结果集中 tweets 的类型。它的值可以是“混合”、“最近”或“流行”。我们使用“最近”作为`result_type`来获取最近的推文。

接下来，我们调用`retrieveTweets`函数来生成结果。下面的代码预览了`retrieveTweets`函数的实现。

```
public function retrieveTweets($twitter_auth, $search_params) {

    $code = $twitter_auth->request('GET', $twitter_auth->url('1.1/search/tweets'), $search_params);
    $response = json_decode($twitter_auth->response["response"]);

    foreach ($response->statuses as $value) {
        array_push($this->result_tweets, $value);
    }

    if (isset($response->search_metadata->next_results) && count($this->result_tweets) < 500) {

        $search_meta = substr($response->search_metadata->next_results, 1);
        $search_meta = explode("&", $search_meta);
        $max_id = 0;
        foreach ($search_meta as $sm) {
            $max_id_res = explode("=", $sm);
            if ($max_id_res[0] == 'max_id') {
                $max_id = $max_id_res[1];
            }
        }

        $search_params['max_id'] = $max_id;
        $this->retrieveTweets($twitter_auth, $search_params);
    }
}
```

这是一个递归函数，用于 tweets 的分页搜索。我们使用默认参数调用`1.1/search/tweets` API URL，并将所有结果 tweets 分配到一个数组中。我们必须递归地调用这个函数来生成剩余页面的结果。因此，我们检查响应中的`next_results`的可用性，以及我们的结果集是否超出了给定的限制。

搜索所有产生的推文并分页几乎是不可能的任务。所以我们必须将结果集缩小到一定的范围。

然后，我们提取`max_id`参数，并将其分配给搜索参数，以生成下一个结果集。最后，我们递归调用该函数，直到我们到达结果或 500 条 tweets 的末尾，并将其分配给`result_tweets`数组。现在我们可以回到`getContestResults`函数的剩余部分。

接下来，我们遍历`result_tweets`数组，并通过计算每个用户名的总 tweets 数来创建一个新数组。最后，我们显示用户名的转发计数。我们可以通过排序这个数组来自动选出获胜者。

我们将结果集限制为 500 条推文。限制结果集中的 tweets 数量对我们的应用程序来说也是一个优势。因为它只会使用比赛的最新 500 条推文。因此，用户不能只是创建推文，然后坐以待毙。他们必须不断发微博，才能在比赛结束前获得最新的 500 条微博。

我们已经完成了 Twitter 标签竞赛的实现，现在是时候让你来看看了。

你可以在这里访问演示[，在这里](http://www.innovativephp.com/demo/twitter_hashtag_contest)下载源代码[。](http://www.innovativephp.com/demo/twitter_hashtag_contest.zip)

创建一些推文，并尝试获得尽可能多的转发。确保使用与本教程相关的内容进行推文。然后访问[此处](http://www.innovativephp.com/demo/twitter_hashtag_contest?action=results)查看结果。

### 限制

开发这个应用程序是为了向您介绍使用 Twitter API 的竞赛，但是当然它也有一些限制。要进行现场比赛，你需要仔细考虑并解决这些限制。限制包括:

*   用户需要保存在数据库中，而不是仅仅使用浏览器会话来存储值。

*   我们需要实现垃圾邮件过滤器来删除任何使用我们的标签的垃圾信息。

*   目前，我们正在考虑所有带有给定标签的推文。理想情况下，我们应该只考虑那些由注册用户创建的推文。

*   需要实现异常处理来向用户提供错误消息。

*   PHP 不应该输出这么多 HTML 代码——除非竞赛应用是一次性应用，否则你应该使用一个框架，依靠视图和模板来保持代码的结构化

一旦您解决了上述限制，这个应用程序就可以用来创建真正的 Twitter 竞赛。你也可以通过考虑其他 Twitter API 特性来改变规则和获胜标准。

## 结论

一旦您习惯了 Twitter 竞赛开发，使用各种 Twitter API 函数生成独特类型的竞赛就变得非常容易。我建议尝试以下竞赛，以掌握这个主题，并开始产生更高的流量和促进您的应用程序。

*   建立一个关注并赢得比赛——首先，用户需要关注一个给定的 Twitter 个人资料来参加比赛。然后你可以创建一条推文，邀请其他人关注。可以基于用户的追随者列表中追随者的最高数量来选择获胜者。

*   建立一个列表竞赛——首先要求用户验证自己，并创建一个关于给定主题的列表。然后让用户通过推文推广自己的榜单。可以基于列表中最大数量的订户来选择获胜者。

现在是时候使用演示了，并开始发布关于本教程的推文。欢迎您的所有建议和意见。我期待着阅读你的推文！

## 分享这篇文章