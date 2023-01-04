# 阵列接口的乐趣

> 原文：<https://www.sitepoint.com/fun-array-interfaces/>

作为一名每天与不同语言打交道的程序员，我发现在学习其他语言中事情是如何以不同的方式完成的，并看看我是否能在 PHP 中做同样的事情时，我感到非常高兴。Python 中我特别喜欢的一点是如何在自定义类中模拟原生数据类型的特性。

以这个成员列表类为例:

```
class Members:
    def __init__(self, members):
        self.members = members

    // other methods
```

通过实现`__iter__`方法，你可以迭代这个类的实例中的数据，就像你迭代一个列表(PHP 中的数组)一样:

```
class Members:
    def __iter__(self):
        return self.members

ls = Members(["You", "Me"])

for member in members:
    print members
```

测试成员资格只需要实现`__contains__`方法:

```
class Members:
    def __contains__(self, member):
        return member in self.members
```

从那里你可以做:

```
"Me" in members:
    print "I am a member!"
```

我想，如果您能在 PHP 中对自定义类的实例而不仅仅是数组做同样的事情，那该多好啊:

```
isset($myObject["test"]);
```

PHP 允许我们用数组接口来做这件事。

## 简而言之，接口

可以把接口想象成指定一个类必须包含的方法的契约。

```
interface Multiplier {
    public function multiply($num1, $num2);
}
```

任何使用这个接口**的类必须**有这样一个乘法方法。有一个关键字表示一个类履行了这个契约:implements。

```
class SmartMultiplier implements Multiplier {
    public function multiply($num1, $num2) {
        return $num1*$num2;
    }
}
```

只要是，合同怎么履行都无所谓。实现乘法方法的另一种方式是这样的:

```
class NotSoSmartMultiplier implements Multiplier {
    public function multiply($num1, $num2) {
        $product = $num1;
        foreach(range(1,$num2-1)) {
            $product = $num + $num2;
        }
        return $product;
    }
}
```

## SPL 和 PHP 接口

PHP 提供了一个预定义接口的库，通过简单地在类中实现它们，可以使我们的对象像数组一样。

这些接口中有些包含在[预定义接口和类](http://www.php.net/manual/en/reserved.interfaces.php)的列表中，有些包含在[标准 PHP 库](http://www.php.net/manual/en/intro.spl.php) (SPL)中。

如果这些条款听起来令人生畏，不要让他们得逞。你们以前都用过`$_GET`。`$_GET`是一个语言构造，据说是 ***预定义的*** 。

另一方面，从文献来看，SPL 只是

> 旨在解决常见问题的接口和类的集合。

现在要做的就是查看这些接口的运行情况。所以让我们开始吧！

我们将创建一个 Twitter 时间轴类，

```
$tweets = new Timeline("jeunito");
```

能够统计它的推文，

```
count($tweets);
```

循环通过它们，

```
foreach($tweets as $tweet) {
    echo $tweet; 
}
```

通过一个微博 id 来获取一条微博，

```
// get 
if (isset($tweets["some tweet id"])) {
    echo $tweets["some tweet id"];
}
```

就像我们在普通数组中一样！

不过，我们得把一些事情处理掉。如果你还没有 Twitter 账户，先创建一个。现在[注册一个开发者账户](https://dev.twitter.com/)并生成一个访问令牌和密码。

接下来，从 [Github](https://github.com/phpmasterdotcom/array_interfaces) 下载或克隆代码，并在源文件夹中运行`composer install`。如果你对 Composer 不熟悉，可以看看 [SitePoint 以前的文章](https://www.sitepoint.com/php-dependency-management-with-composer/)。打开 index.php 文件，添加必要的 Oauth 数据。

## 可数界面

可数接口可能是最不言自明的了。它让我们简单地通过实现 count 方法将对象传递给`count()`方法。

我们可以通过在“/users/show”上执行 get 请求来获取用户的 tweets 数量。

```
 public function count()
 {   
     $result = $this->api->get("users/show", array(
         'screen_name' => $this->username
     )); 

     return $result['statuses_count'];
}
```

## ArrayAccess 接口

我们现在将通过学习一个更有趣的界面来增加赌注。

当实现时，`ArrayAccess`将使我们的对象能够像地图一样被访问，这就是它们真正的样子。要实现的方法有

```
ArrayAccess {
    abstract public boolean offsetExists ( mixed $offset )
    abstract public mixed offsetGet ( mixed $offset )
    abstract public void offsetSet ( mixed $offset , mixed $value )
    abstract public void offsetUnset ( mixed $offset )
}
```

这在我们的 Twitter 时间线对象中非常方便。测试一条 tweet 是否存在于时间轴中可以通过将我们的对象传递给`isset`来完成，如下所示:

```
isset($tweets['some tweet id']);
```

为此，我们只需对 tweet id 执行 GET 请求。

```
public function offsetExists($offset) {
    $tweet = $this->api->get("statuses/show", array("id" => $offset));    
    if ($tweet) {
        return $tweet["text"];
    }   

    return false;
}
```

更好的是，我们也可以使用上面的`offsetGet`来代替，让`offsetExists`依次调用 offset。

```
public function offsetGet($offset) {
    $tweet = $this->api->get("statuses/show", array("id" => $offset));    
    if ($tweet) {
        return $tweet["text"];
    }   

    return false;
}

public function offsetExists($offset) {
    return $this->offsetGet($offset) !== false;
}
```

使用`offsetUnset`,我们也可以通过 tweet id 进行删除，但是我将把它留给你来实现。

不幸的是，实现 offsetSet 没有多大意义。对于这样的事情，最简单的方法就是抛出一个像`UnsupportedOperationException`这样的自定义异常。但另一方面，它也可能取决于您的应用程序的特定业务规则。

## 迭代器接口

我把最喜欢的界面留到了最后！迭代器接口在这里非常有用，因为我认为没有比循环遍历时间轴对象(就像它是一个普通数组一样)更好的方法来封装通过远程集合分页的血淋淋的细节。

首先，需要实现以下方法:

```
Iterator extends Traversable {
    /* Methods */
    abstract public mixed current ( void )
    abstract public scalar key ( void )
    abstract public void next ( void )
    abstract public void rewind ( void )
    abstract public boolean valid ( void ) 
}
```

我们可以显式地使用上面的方法来遍历我们的时间线，如下所示:

```
$it->rewind();

while ($it->valid())
{
    $key = $it->key();
    $value = $it->current();

    // do something

    $it->next();
}
?>
```

但是当你可以这样做的时候，为什么要这样做呢？

```
foreach($tweets as $id => $tweet) {
    echo $tweet;
}
```

在我们的示例中，我们将通过按时间顺序检索 tweet 块，然后将它们存储在缓冲区中，来循环遍历时间轴中的 tweet。我们将遍历这个缓冲区，直到它用完，然后我们得到另一批 tweet，使用最后一条 tweet 的 id 作为偏移量。

最初我们什么都没有，这就是 rewind 方法的用武之地:获取最新的 10 条 tweetss，这样就有了一个偏移量，我们可以从这里获取下 10 条 tweet。

```
public function rewind() 
{   
    $this->tweets = $this->api->get('statuses/user_timeline', array('count' => 20));
}
```

`valid()`方法只是用来指示是否继续循环。这可以通过检查我们的推文缓冲区是否足够大来实现:

```
public function valid()
{
    return !empty($this->tweets);
}
```

在我们的迭代中，`key()`和`current()`方法只是返回当前 tweet 的键和值。出于我们的目的，我们将简单地从缓冲区中获取 tweet id 和最新 tweet 的文本。

```
public function key() 
{
    $latest = reset($this->tweets);
    return $latest['id_str'];
}

public function current() 
{
     $latest = reset($this->tweets);
     return $latest['text'];
}
```

最后还有下一个方法。这里，我们将缓冲区的头部出队，以获取下一个要迭代的元素。然后，如果我们在缓冲区的最后一个元素，我们确保我们得到下一组 tweets。

```
public function next()
{
    $head = array_shift($this->tweets);
    if (!is_null($head) && empty($this->tweets))
    {
        $this->tweets = $this->api->get('statuses/user_timeline', array('count' => 800, 'max_id' => $head['id_str']));
    }
}
```

我们完了！这是循环浏览用户推文的一个非常基本的实现。还有很多事情可以做，比如本地缓存以节省 api 调用，但这就是使用接口的好处:它们允许我们在幕后改变我们的策略，只要我们的实现仍然正确，我们就可以期待它仍然有效。

但是现在，您可以通过在命令行运行`php index.php`来观察我们的时间轴对象的工作情况。

## 结论

接口的好处是双重的。它们让我们封装实现细节，并为我们提供语法上的好处，这两者在任何要求互操作性的应用程序中都是一种真正的魅力。如果你有任何问题或意见，请在下面的评论区留下！

## 分享这篇文章