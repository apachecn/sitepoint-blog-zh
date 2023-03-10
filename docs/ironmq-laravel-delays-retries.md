# IronMQ 和 Laravel:延迟和重试

> 原文：<https://www.sitepoint.com/ironmq-laravel-delays-retries/>

[在之前的](https://www.sitepoint.com/ironmq-laravel-implementation/)中，我们看到了如何通过 Laravel 使用铁推队列。我们需要做的只是建立一个 Iron 帐户，添加一个订阅者 URL，将一条消息推入队列，然后接收这条消息。

![](img/bfaf1c964c4b7c188b8b18c5712709e2.png)

Laravel 支持铁推队列开箱即用的方式令人惊叹，但总有局限性。在本文中，我们将重点关注这些限制，并学习如何解决它们。

## 情节

有三种可能的情况:

1.  您收到了消息，并成功完成了任务。

    *   您需要设置来告诉 Iron 作业已经成功完成，它需要将它从队列中删除。
2.  你收到了消息，但出了问题。基本上，你的工作并不成功。

    *   在这种情况下，我们应该告诉 Iron 我们失败了，它应该重试，或者如果你不能告诉 Iron 任何事情，它应该足够聪明地重试该作业，即，在一段时间后将相同的消息再次推送给你的订户。
3.  您收到了消息，但是您的工作是一个长时间运行的过程(需要一分钟以上的时间)

    *   Iron 在重新发送消息之前应该等待更长时间。

这些都是实际情况，你每天都会遇到。是的，你需要在你的申请中涵盖这些情况。

如果我们使用 Iron MQ PHP 库，就有可能充分利用 Iron。在设置过程中，我们将`"iron-io/iron_mq": "1.4.6"`包含在我们的 composer 文件中。相同的代码托管在`https://github.com/iron-io/iron_mq_php`。让我们看一下文档，并试着理解如何使用这个库。

我们按如下方式启动队列:

```
$ironmq = new IronMQ(array(
        "token" => 'XXXXXXXXX',
        "project_id" => 'XXXXXXXXX'
    ));
```

这是我们发布到队列的方式:

```
$ironmq->postMessage($queue_name, "Test Message", array(
        'timeout' => 120,
        'delay' => 2,
        'expires_in' => 2*24*3600 # 2 days
    ));
```

要将一个队列转换成一个推送队列(或者创建一个),我们所要做的就是向队列发送订阅者列表、push_type 等选项。使用该库，您可以执行以下操作:

> 注意:如果一个队列没有任何订户，它就是一个拉队列。

```
$params = array(
        "push_type" => "multicast",
        "retries" => 5,
        "subscribers" => array(
            array("url" => "http://your.first.cool.endpoint.com/push"),
            array("url" => "http://your.second.cool.endpoint.com/push")
        )
    );

    $ironmq->updateQueue($queue_name, $params);
```

就是这样！

这正是 Laravel 在运行`artisan queue:subscribe`命令时所做的。概括地说，在对`queue.php`文件*(用 project_id、token 和 queue_name 填充它)*进行必要的设置之后，运行一个命令来注册您的订户:

```
php artisan queue:subscribe queue_name http://foo.com/queue/receive
```

该命令运行以下代码:

*/vendor/laravel/framework/src/Illuminate/Queue/Console/subscribe command . PHP*

```
public function fire()
    {
		$iron->getIron()->updateQueue($this->argument('queue'), $this->getQueueOptions());
	}
```

为了简洁起见，文件中排除了一些内容。

这就是 Laravel 如何将我们的队列转换成推送队列。这与使用 Iron MQ 库的方式相同。

## 使用 Iron MQ PHP 库

1.  在*应用/控制器*中创建一个新的控制器 IronController

    ```
    <?php

            class IronController extends Controller {

                protected $ironmq;

            	public function __construct()
            	{
            		$this->ironmq = new IronMQ(array(
            					    "token" => 'XXXXXXXXXXXXXXXXXXX', //Your token from dashboard
            					    "project_id" => 'XXXXXXXXXXXXXXX' //Your project ID from dashboard
            					));
            	}

            }
    ```

    *app/控制器/IronController.php*

2.  在我们创建我们的方法之前，让我们创建路线来理解我们的控制器将做什么:

    ```
    Route::get('iron/create','IronController@create'); //Create a Queue and add subscribers
            Route::get('iron/push','IronController@push'); //Push a message to Queue
            Route::get('iron/status/{id}','IronController@status'); //Know the status of the message we pushed
            Route::post('iron/receive','IronController@receive'); //Receive the message and process it
    ```

    *app/routes.php*

3.  现在，让我们编写一个方法来创建带有订阅者的队列

    ```
    public function create()
        	{
        		$params = array(
        		    "subscribers" => array(
        		        array("url" => url('iron/receive'))
        		    )
        		);

        		$this->ironmq->updateQueue('testing', $params);

        		return 'Push Queue Created';
        	}
    ```

    *app/控制器/IronController.php*

4.  您需要访问`http://953ffbb.ngrok.com/iron/create`，即使用您的 [ngrok](https://www.sitepoint.com/use-ngrok-test-local-site/) url 加上`/iron/create`。注意:您的 ngrok url 会有所不同。

    此时，您可以转到 Iron DashBoard-> MQ-> Queues-> Find a queue with name ' testing '-> Push Queues，并确保您的订户列在那里，类似于`http://953ffbb.ngrok.com/iron/receive`。

5.  让我们添加一些方法来将消息推入队列并检查状态。

    推送消息的方法:

    ```
    public function push()
            {
            	//Just some data you want to pass
            	$data = array(
            		'first_name' => 'Rajiv', 
            		'last_name'  => 'Seelam'
            	);

            	//Convert data into a string so that we can pass it
            	$data = serialize($data);

            	//Post the message
            	$job = $this->ironmq->postMessage('testing', $data);

            	Log::info(__METHOD__.' - Message pushed with job_id : '.$job->id);

                return Redirect::to('iron/status/'.$job->id);
            }
    ```

    检查消息状态的方法:

    ```
    public function status($id)
    	{
    	$status = $this->ironmq->getMessagePushStatuses('testing',$id);
    	dd($status);
    	}
    ```

    *app/控制器/IronController.php*

6.  我们需要一种方法在推送时接收消息。

    ```
    public function receive()
            {
            	$req = Request::instance();

        		$jobId = $req->header('iron-message-id'); //Get job id 

        		$data = unserialize($req->getContent()); //Get content and unserialize it

        		Log::info(__METHOD__.' - Job Received From Iron with Job ID : '.$jobId);

        		return Response::json(array(),200);
        	}
    ```

    *app/控制器/IronController.php*

此时，您应该明白这是将接收消息的方法，因为在`create`方法中，我们将`url('iron/receive')`列为订户(检查 routes 文件以确认该 url 路由到该方法)。

保持你的*app/storage/logs/laravel . log*打开，看看发生了什么。

现在，如果您访问`http://953ffbb.ngrok.com/iron/push`，您应该会在浏览器中看到类似如下的消息:

```
array (size=1)
          0 => 
            object(stdClass)[184]
              public 'retries_delay' => int 60
              public 'retries_remaining' => int 3
              public 'retries_total' => int 3
              public 'status_code' => int 200
              public 'status' => string 'deleted' (length=7)
              public 'url' => string 'http://953ffbb.ngrok.com/iron/receive' (length=38)
              public 'id' => string '6019569765635787179' (length=19)
```

您的日志应该类似于:

```
[2014-05-31 12:45:04] production.INFO: IronController::push - Message pushed with job_id : 6019569765635787179 [] []
[2014-05-31 12:45:05] production.INFO: IronController::receive - Job Received From Iron with Job ID : 6019569765635787179 [] []
```

*app/storage/logs/laravel . log*

这些日志证实了我们的活动。让我们总结一下我们在这里做了什么:

*   我们将消息推送到一个名为“测试”的队列中
*   然后，我们检查队列中消息的状态
*   我们收到了消息并记录了一些东西。

## 延迟和重试

现在，让我们深入一点。打开你的铁仪表板，进入队列页面(点击 MQ) ->打开*测试*队列，检查“推送队列”，你会看到用户列表，在“推送信息”下，你会看到“重试”和“重试延迟”。

这些是什么意思？

**重试次数**:默认值为 3，表示 Iron 将重试该消息 3 次。

**重试延迟**:默认值为 60，这意味着默认情况下，如果 Iron 认为消息没有被成功处理，它会在 60 秒后再次向用户推送消息。

**铁什么时候再发消息？**:

1.  当它没有得到应用程序的响应时:

    *   由于某些错误，您的应用程序未能给出响应。
    *   您的应用程序仍在处理作业，这是一个长时间运行的过程。
2.  当它得到错误响应时:

    *   你的应用程序响应 4xx 或 5xx 错误。
3.  当您发送 202 响应时:

    *   您要求 Iron 在延迟后重新发送消息。

如果您检查状态*(见上文)*的输出，您会注意到以下行:

```
public 'status' => string 'deleted'
```

这是我们了解我们推送的消息的状态的方式。状态(当前)的可能值为:

1.  **重试**–作业将被发送到端点，即订户(*与设置的“重试次数”*一样多)。
2.  **已删除**–任务被删除。
3.  **保留**–超时后将重试作业。
4.  **错误**–出现了一个错误，将不再执行任何操作。

> 您必须发送 200 回复才能删除邮件。

必须记住，如果超时后没有得到响应，Iron 将重试消息(超时后，它将重试您在“retries_total”中指定的次数)。

我们能改变这些参数吗？当然啦！

发布消息时，您可以提及超时(长时间运行的作业可能需要一分钟以上)

```
$this->ironmq->postMessages('testing', "data", array(
        "timeout" => 300 // Wait for 300 seconds = 5 minutes
    ));
```

如果希望减少或增加重试次数，必须调用 updateQueue 方法。

```
$params = array(
	    "retries" => 5
	);

	$this->ironmq->updateQueue('testing', $params);
```

我们可以改变队列中消息的状态吗？当然啦！这就是你应该如何使用队列，事实上你已经这样做了。

让我们看看如何改变消息的状态:

1.  **重试**–推送消息时设置此状态。
2.  **已删除**–当用户回复 200 时，设置该状态。
3.  **保留**–当用户以 202 响应时，设置该状态。
4.  **错误**–如果 Iron 用尽了重试次数，并且仍然没有得到 200 响应，则状态设置为错误。

注意:当您的应用程序响应 4xx 或 5xx 错误(这通常意味着服务器出现了问题)时，Iron 等待的时间比提到的超时时间更长。

## 结论

当你一步一步看的时候，推送队列并不是火箭科学。自己试试，在下面的评论里告诉我们你的想法吧！我真诚地希望这能帮助你理解这个经常令人生畏的话题。我强烈推荐你去读:[http://dev.iron.io/mq/reference/push_queues/](http://dev.iron.io/mq/reference/push_queues/)。请注意，您可以在 https://github.com/rajivseelam/laravel-queues 的[找到本文的源代码。](https://github.com/rajivseelam/laravel-queues)

感谢阅读！

## 分享这篇文章