# 使用 UserSnap 实现客户端错误报告

> 原文：<https://www.sitepoint.com/implement-client-side-bug-reporting-usersnap/>

想象一下下面的场景:你的客户访问这个网站(让我们想象一下[这个](http://talkweb.eu/labs/fr/request.php))却没有看到预期的结果。正常的反应是(在最不合适的时候)给你打电话，让你尽快修好，因为他们在赔钱。

我们如何帮助用户尽可能准确地报告错误？

![](img/98f0a13d44146edb2accddef291ad61c.png)

## 虫子

让我们用一个非常简单的 JSON 请求和一个错误来重现我们的案例:

```
$json_data = '{"value":1,"apples":2,"name":3,"oranges":4,"last one":5}';

//we will simulate the json data, but imagine that this is the normal data exchanged daily between your client’s website and a 3rd party API

$ch = curl_init('http://talkweb.eu/labs/fr/json_callback.php');                                                                 
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");                                                                
curl_setopt($ch, CURLOPT_POSTFIELDS, $json_data);                                  curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);                                    curl_setopt($ch, CURLOPT_HTTPHEADER, array(                                                'Content-Type: application/json',                      
        'Content-Length: ' . strlen($json_data)));

//the normal CURL request, nothing strange here:
$result = curl_exec($ch);

//receiving the data back
$f_data =  json_decode($result,true);

//showing a greeting with the output
echo  “Welcome”. $f_data['username'];
```

如果你现在访问测试网站，你会发现有一个问题。

问题是——客户如何更快地报告它，并提供您需要的所有数据来解决问题:

*   Json 数据，
*   服务器端 Javascript 和 XmlHttpsRequest 错误，
*   一些核心 PHP 错误
*   …和元数据。

收集这些信息的一个有趣的解决方案是 UserSnap。一个小部件，允许你的用户标记他们所在站点的屏幕截图，并向你发送 JavaScript 控制台中的所有内容。PHP 错误不会就此结束，不是吗？让我们来做吧。首先，我们将收集服务器端的错误。

## 收集错误

让我们向示例中添加更多的代码，以便了解如何收集我们需要的数据。引入一个非常简单的类来帮助我们:

```
<?
class SendToUsersnap
{
    var $m;
    //Save all logs in an array. You can use an even more elegant approach but right now I need it to be simple for the sake of this tutorial.
    function log ( $data, $type ) {

        if( is_array( $data ) || is_object( $data ) ) {
            $this->m[]= "console.".$type."('PHP: ".json_encode($data)."');";
        } else {
            $this->m[] = "console.".$type."('PHP: ".$data."');";
        }
    }
  // Print all logs that have been set from the previous function. Let’s keep it simple.
    function  out (){
         for ($i=0;$i<count($this->m);$i++)
          {
              echo $this->m[$i]."\n";
          }

        }
}
```

现在让我们使用这个类来记录我们可能需要的所有错误和日志。

```
require_once('Usersnap.class.php'); 
    $s = new SendToUsersnap;

    $json_data = '{"value":1,"apples":2,"name":3,"oranges":4,"last one":5}';
    $s->log('Initializing the JSON request',"info");
    $s->log($json_data,"log");

    $ch = curl_init('http://talkweb.eu/labs/fr/json_callback.php');             
    curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
    curl_setopt($ch, CURLOPT_POSTFIELDS, $json_data);                           
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);                         
    curl_setopt($ch, CURLOPT_HTTPHEADER, array(                                 
        'Content-Type: application/json',                           
        'Content-Length: ' . strlen($json_data)));                                                                                                                   

    $result = curl_exec($ch);
    $f_data =  json_decode($result,true);

    echo  'Welcome'. $f_data['usersname'];

    $s->log($f_data,"log");
    $s->log(error_get_last(),"error");
```

## 将其传递给用户快照

让我们在页面末尾添加 UserSnap 代码片段，看看会发生什么。(您可能需要一个帐户来使用此 widget。Usersnap 为开源项目提供[免费许可](http://usersnap.com/blog/free-open-source-project-license-request/)，为商业项目提供[免费测试期](http://usersnap.com/features/console-recorder)。

```
<script type="text/javascript"> (function() {
  var s = document.createElement("script");
    s.type = "text/javascript";
    s.async = true;
    s.src = '//api.usersnap.com/load/'+
            'your-api-key-here.js';
    var x = document.getElementsByTagName('script')[0];
    x.parentNode.insertBefore(s, x);
  })();

 var _usersnapconfig = {
   loadHandler: function() {
        <?php
    //just print all errors collected from the buffer.
 $s->out(); ?>
     }
 }; </script>
```

注意:您可以在真实框架的视图模板中这样做，但是由于我们在这里没有使用真实的 MVC 应用程序，我们将跳过这一部分。

用户会在页面上看到一个“报告错误”按钮，并会给你写一条消息，比如“它不起作用了，尽快修复它”。通常，这是无用的信息，但是这一次，我们还附加了这个华丽的错误日志:

![A beatiful error log attached to your bug](img/05b3464fe0071540fd55eb2d7baca757.png)

现在您可以看到初始化已经就绪:

```
$s->log('Initializing the JSON request',"info");
```

您可以看到我们将发送的数据，与往常一样

```
$s->log($json_data,"log");
```

你可以看到输出。是的，问题就在那里。显然有一个认证问题。

```
$s->log($f_data,"log");
```

您甚至可以获得核心 PHP 错误来帮助您进行调试。

```
$s->log(error_get_last(),"error");
```

您的客户只需点击一次按钮，您就可以获得更快解决问题所需的一切:

1.  错误和日志(如上所示)
2.  带注释的屏幕截图–如果问题比这个简单的例子更复杂
3.  屏幕大小、浏览器版本、操作系统和浏览器中安装的插件

当然，只有当您的客户需要时，您才可以打开这个特性。要限制小部件的可用性，请添加 IP 过滤器或查询参数屏障，打开 dev。*子域等。

## 结论

这不是一个脚本监控工具，当问题出现时，它不会自动提供信息。这种方法只有在用户或客户报告了一个 bug，而你作为开发人员或 QA 需要更多关于如何对抗这个 bug 的信息时才有效。把它想象成一个远程调试器，但是用于客户端错误+事件和你从 PHP 发送到 JavaScript 的数据。

我很乐意回答你所有的问题！请在下面留下您的反馈！

## 分享这篇文章