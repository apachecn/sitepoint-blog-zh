# 用 PHP、MongoDB 和 jQuery 发布社交网络风格——第 1 部分

> 原文：<https://www.sitepoint.com/social-network-style-posting-php-mongodb-jquery-part-1/>

如今，类似于脸书的 Post 机制在任何应用程序中都很常见。每个使用过社交网络的人都很熟悉发表类似评论的概念。在这篇文章中，我们将学习如何创建一个类似的工作模型，其中用户将能够张贴他的状态，如职位和评论。更有趣的是，从这篇文章中学到一些东西后，今后您将能够自己实现许多其他特性。

我将使用 PHP 作为编码语言，MongoDB 作为数据库，jQuery 用于 JavaScript 操作。假设读者熟悉 PHP 和 MongoDB 的基础知识，能够彻底理解本文。但是，请注意，这篇文章是以一种非常普通和新手友好的方式写的，有很多东西可以从中学习。因此，即使您没有使用 Mongo DB 或 jQuery，也可以很容易地适应这些技术，或者用您喜欢的技术替换它们。

这个系列有两篇文章。在第一篇文章中，我们将学习数据库架构，设计流，并想出我们将要采用的应用程序流结构。在本系列的下一篇文章中，我们将编写代码，最终允许我们创建一个新帖子，喜欢/不喜欢一个帖子并对其进行评论。请理解，第一篇文章更像是我们将在下一部分实现的功能的基础。因此，在第二篇文章中，您可能会发现，在了解更多信息之前，您不会理解某些事情的实际实现逻辑。文章附有[可下载代码](https://github.com/phpmasterdotcom/AshishTrivedi-SocialNetworkPost)供大家参考。请注意，该代码是以过程形式编写的，因此肯定不是最佳实践。选择这种简化的形式是为了使实现本身对新手更友好，这种 post 机制将在以后更复杂的文章中以面向对象的方式重新实现。

在开始之前，让我们看看我们将开发的最终作品(*图 1* )。

![](img/e87b72b9609a755fa0d6e2cedd00bdce.png)

## 下载代码:

从文章的 [Github repo](https://github.com/phpmasterdotcom/AshishTrivedi-SocialNetworkPost) 下载文章附带的代码。您将在其中找到以下文件:

index.php:这是一个主文件，显示了所有的帖子，用户可以在这里进行互动。

*mongo_connection.php* :这个文件包含了常见的 MongoDB 连接代码。如果您对 MongoDB 连接凭证进行了任何更改，您应该在这里指定它们。在所有其他 PHP 文件中，我们将包含这个文件以使用常见的 MongoDB 连接代码。按照[安装说明](http://www.mongodb.org/downloads)安装 [PHP 驱动](http://docs.mongodb.org/ecosystem/drivers/php/)，确保安装好 MongoDB。

*session_variables.php* :这篇文章期望我们在 session 变量中存储一些公共字段，比如用户 id、用户名和用户的个人资料图片，因为它们在整个应用程序中非常频繁地使用。通常，这样的信息是在用户登录时设置的，但是出于本文的目的，我们将在这个文件中设置它。

*script.js* 和 *style.css* 包含了所有的 Javascript 和 css 代码。 *jquery.js* 是标准的 jquery 库文件。

*php_scripts* :这个文件夹包含了所有的 php 文件，我们将通过 AJAX 调用这些文件来执行插入文章、对文章进行喜欢/不喜欢以及插入评论的 PHP 操作。

*图片*:该文件夹包含所有用户的个人资料图片。

## 数据库结构:

*用户*集合将是一个简单的集合，包含用户 id、用户名和用户的个人资料图片，如下所示:

```
{
   "_id": ObjectId("5222f8e0d85242c01100000d"),
   "name": "Richard Garry",
   "profile_pic": "profile_pic_3.jpg" 
}
```

另一个集合 *posts_collection* 将维护与帖子相关的所有信息，如下面的示例条目所示:

```
{
   "_id": ObjectId("5222f885d85242c011000009"),
   "post_author_id": ObjectId("5146bb52d8524270060001f4"),
   "post_text": "Let's get started !!!\t\t\t",
   "timestamp": "Sun, 01-Sep-2013",
   "total_comments": NumberInt(3),
   "total_likes": NumberInt(2) ,
   "likes_user_ids": {
     "0": ObjectId("52147f70d85242cc12000010"),
     "1": ObjectId("5222f8e0d85242c01100000d") 
  },
   "comments": {
     "0": {
       "comment_id": ObjectId("5222f897d85242c01100000a"),
       "comment_user_id": ObjectId("52147f70d85242cc12000010"),
       "comment_text": "This is comment 1 !!" 
    },
     "1": {
       "comment_id": ObjectId("5222f8a7d85242c01100000b"),
       "comment_user_id": ObjectId("5222f8e0d85242c01100000d"),
       "comment_text": "This is comment 2 !!" 
    } 
  }
}
```

文档中的大多数字段都是不言自明的。 *likes_user_ids* 包含了所有喜欢过该帖子的用户
的用户 id。 *comments* 是一个包含子文档的数组，用来存储评论信息。

## 了解流设计:

让我们首先放置一个用户将输入文章文本的文本区域和一个使用以下代码创建新文章的按钮(如图*图 2* 所示):

![](img/6c4f5e7945de51ee7963b745bb5274b3.png)

```
<div id="div_post_content">
    <textarea id="post_textarea">
    </textarea>
</div>
<div class="div_post_submit">
    <input type="button" value="Create New Post" id="btn_new_post" class="button_style"/>
</div>
```

我们的*索引*页面以流的形式显示了所有的文章。查看 index.php 的*中的代码，我们将一步一步地解释如何获取和显示这些数据:*

我们首先从数据库中获取所有的文章，并按照降序对它们进行排序，以便首先获得最后插入的文章。

```
$posts_cursor=$collection->find()->sort(array('_id'=>-1));
```

现在，我们使用 foreach 遍历所有获取的帖子，并获得帖子 id、帖子文本和帖子作者的用户 id:

```
foreach($posts_cursor as $post)
{
    $post_id=$post['_id'];
    $post_text=$post['post_text'];
    $post_author_id=$post['post_author_id'];
    …
```

使用用户 id，我们现在可以从 *users* 集合中获取用户名和个人资料图片链接。

```
$collection = $database->selectCollection('users');
$post_author_details = $collection->findOne(array('_id' =>$post_author_id));
$post_author = $post_author_details['name'];
$post_author_profile_pic = $post_author_details['profile_pic'];
```

请理解，在我们的页面上，我们将有许多文章一起显示。每篇文章都有自己的*像*按钮，像计数，评论和评论计数。我们需要为每个帖子唯一地标识这些东西，以便当用户对任何帖子进行任何操作(喜欢、评论)时，我们知道要更新哪个帖子。为此，我们将初始化一些变量，稍后我们将使用这些变量作为相应 HTML 元素的 *id* :

```
$post_like_unlike_id=$post_id.'_like_unlike';
$post_like_count_id=$post_id.'_like_count';
$post_comment_count_id = $post_id.'_comment_count';
$post_self_comment_id=$post_id.'_self_comment';
$post_comment_text_box_id=$post_id.'_comment_text_box';
```

我们现在检查当前用户是否已经喜欢这个帖子。为此，我们检查当前用户的用户 id 是否存在于从数据库获取的 *likes_user_ids* 数组中。如果用户已经喜欢这个帖子，我们设置 *$like_or_unlike* 变量来显示 *Unlike* 。但是如果用户仍然不喜欢这个帖子，我们需要显示 *Like* 选项。稍后我们将在 HTML 中使用 *$like_or_unlike* 来显示 *Like* 或 *Unlike* 选项。

```
if(in_array($_SESSION['user_id'],$post['likes_user_ids']))
{
   $like_or_unlike='Unlike';
}
else
{
 $like_or_unlike='Like';
}

// you can also write this in short ternary form:

$like_or_unlike = (in_array($_SESSION['user_id'],$post['likes_user_ids'])) ? 'Unlike' : 'Like';
```

我们现在将使用上面初始化的变量，并创建 HTML 结构来显示文章。每个`post_wrap` div 将显示一篇文章，这个`post_wrap`的`id`将是从数据库中提取的文章 id。

```
<div class="post_wrap" id="<?php echo $post['_id'];?>">
<div class="post_wrap_author_profile_picture">
    <img src="images/<?php echo $post_author_profile_pic;?>" />
</div>  
<div class="post_details">  
          <div class="post_author">
        <?php echo $post_author ?> 
    </div>
    <div class="post_text">
        <?php echo $post_text; ?>
    </div>
</div>
```

现在我们来展示一下点赞数和评论数。注意，我们已经在前面的步骤中初始化了一些变量。这里会用到这些变量。`$like_or_unlike`显示之前设置的*喜欢的*或*不喜欢的*文本，而`$post_like_count_id`和`$post_comment_count_id`用于给出显示喜欢和评论数的*id*。

在下一篇文章中，您将会理解分配这些*id*的用途，在下一篇文章中，我们将不得不获取数据并为每篇文章更新数据。

```
<div class="comments_wrap">
    <span>
        <span><img src="images/like.png" /></span>     
        <span class="post_feedback_like_unlike" id="<?php echo $post_like_unlike_id;?>"><?php                  echo $like_or_unlike; ?></span>                    
        <span class="post_feedback_count" id="<?php echo $post_like_count_id; ?>"><?php echo                    $post_like_count;?></span>
    </span>
    <span>
        <span class="post_feedback_comment"> <img src="images/comment.png" /> Comment</span>               <span class="post_feedback_count" id="<?php echo $post_comment_count_id; ?>"><?php echo                     $post_comment_count;?></span>
    </span>
</div>
```

上面的代码会产生类似这样的结果(图 3):

![](img/7a9c2ff85f432710d5260362c35e636f.png)

现在，为了显示与每篇文章相关的评论，我们迭代从数据库中检索的*评论*数组，并初始化要在 HTML 中使用的变量。对于每个评论，我们在*用户*集合上执行*查找*查询，以获取评论作者的姓名和个人资料图片链接。

```
for($i=0;$i<$post_comment_count;$i++)
{                                           
    $comment_id=$post['comments'][$i]['comment_id'];                           
    $comment_text=$post['comments'][$i]['comment_text'];                   
    $comment_author_id=$post['comments'][$i]['comment_user_id'];
    $collection = $database->selectCollection('users');
    $comment_author_details = $collection->findOne(
    array('_id' => new MongoId($comment_author_id))
    );                             
    $comment_author = $comment_author_details['name'];
    $comment_author_profile_pic = $comment_author_details['profile_pic'];
```

下面是使用上面初始化的变量显示单个注释的 HTML 代码:(Output:*figure 4*)

```
<div class="comment" id="<?php echo $comment_id; ?>">                 
    <div class="comment_author_profile_picture">
        <img src="images/<?php echo $comment_author_profile_pic; ?>"/>
    </div>
    <div class="comment_details">
        <div class="comment_author" >
            <?php echo $comment_author; ?>
        </div>
        <div class="comment_text" >
            <?php echo $comment_text; ?>
        </div>
    </div>
</div>
```

![](img/85d95cb43f7ea35bfcf65c2a66f9a6d5.png)

在显示了从数据库中获取的所有评论后，我们希望显示一个空白的评论框，当前用户可以在其中进行评论。

```
<div class="comment" id="<?php echo $post_self_comment_id; ?>">
    <div class="comment_author_profile_picture">
        <img src="images/<?php echo $_SESSION['user_profile_pic']; ?>" />
    </div>
    <div class="comment_text">
        <textarea placeholder="Write a comment..." id="<?php echo $post_comment_text_box_id;?>" >
        </textarea>
    </div>
</div>
```

![](img/e2f25bf5ef508ad349cdd677a719260f.png)

## 应用程序流结构:

![](img/1247cce8ea334c678880bc0dd8f2d483.png)

现在让我们通过插入一个新帖子的例子来理解应用程序流是如何工作的。现在不要担心编码部分，因为我们将在下一篇文章中看到。

*   第一个 PHP 文件(`index.php`)是主页面，用户将从这里输入文章文本并点击*创建新文章*按钮。为了简单起见，我们将 JavaScript 函数`new_post`作为该按钮的点击处理程序，并将`post_text`和`user_id`作为其参数传递。

*   JavaScript 函数将接收这些参数，并向 PHP 文件(`insert_new_post.php`)发送一个 AJAX POST 请求，传递它接收到的相同参数。

*   这个 PHP 文件负责将收到的文章文本插入到数据库中。完成数据库操作后，它准备包含新的
    post 的 HTML 内容，该内容将作为 JavaScript 函数的输出发送。该输出稍后将呈现在主页上。实际上，PHP 函数应该返回 JSON 以减少带宽，视图文件应该将数据包含到一个准备好的模板中，但鉴于我们的应用程序只是一个演示，这样就可以了。

*   JavaScript 函数接收 PHP 文件发送的输出，并将其插入到主页 HTML 中。

所有其他操作(如评论、喜欢/不喜欢帖子)的流程将类似于我们刚才看到的插入帖子的示例。

## 结论:

在本文中，我们解释了数据库架构、后期流设计和应用程序流程。在本系列的下一部分，我们将使用这些结构并开发插入帖子、喜欢/不喜欢和评论的功能。

## 分享这篇文章