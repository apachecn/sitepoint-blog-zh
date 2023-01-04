# PHP 和 RabbitMQ:高级示例

> 原文：<https://www.sitepoint.com/php-rabbitmq-advanced-examples/>

在第 1 部分中，我们介绍了 PHP 中 AMQP 协议的理论和一个简单用例，其中 RabbitMQ 作为代理。现在，让我们深入一些更高级的例子。

## 示例 1:在几个工作者之间异步发送处理数据的请求

在前一部分的例子中，我们有一个生产者，一个消费者。如果消费者死亡，消息将继续在队列中堆积，直到消费者重新开始。然后它会一个接一个地处理所有的消息。

在每分钟有大量请求的并发用户环境中，这可能不太理想。幸运的是，扩展消费者非常容易，但是让我们实现另一个例子。

假设我们有一个发票生成服务，用户只需要提供发票号码，系统就会自动生成一个 pdf 文件并通过电子邮件发送给用户。如果运行生成过程的服务器资源有限，则生成和发送电子邮件甚至可能需要几秒钟。现在假设我们被要求每秒支持几个事务，我们如何在不使服务器不堪重负的情况下做到这一点？

我们需要实现以下模式:

让我们看看我们的生产者类:

```
<?php
namespace Acme\AmqpWrapper;

use PhpAmqpLib\Connection\AMQPConnection;
use PhpAmqpLib\Message\AMQPMessage;

class WorkerSender
{
    /* ... SOME OTHER CODE HERE ... */

    /**
     * Sends an invoice generation task to the workers
     * 
     * @param int $invoiceNum
     */ 
    public function execute($invoiceNum)
    {
        $connection = new AMQPConnection('localhost', 5672, 'guest', 'guest');
        $channel = $connection->channel();

        $channel->queue_declare(
            'invoice_queue',    #queue - Queue names may be up to 255 bytes of UTF-8 characters
            false,              #passive - can use this to check whether an exchange exists without modifying the server state
            true,               #durable, make sure that RabbitMQ will never lose our queue if a crash occurs - the queue will survive a broker restart
            false,              #exclusive - used by only one connection and the queue will be deleted when that connection closes
            false               #auto delete - queue is deleted when last consumer unsubscribes
            );

        $msg = new AMQPMessage(
            $invoiceNum,
            array('delivery_mode' => 2) # make message persistent, so it is not lost if server crashes or quits
            );

        $channel->basic_publish(
            $msg,               #message 
            '',                 #exchange
            'invoice_queue'     #routing key (queue)
            );

        $channel->close();
        $connection->close();
    }
}
```

`WorkerSender::execute()`方法将接收一个发票号。接下来，我们像往常一样创建一个连接、通道和队列。

```
<?php
/* ... SOME CODE HERE ... */

        $msg = new AMQPMessage(
            $invoiceNum,
            array('delivery_mode' => 2) # make message persistent, so it is not lost if server crashes or quits
            );

/* ... SOME CODE HERE ... */
```

请注意，这一次，在创建消息对象时，构造函数接收第二个参数:`array('delivery_mode' => 2)`。在这种情况下，我们希望声明，如果 RabbitMQ 服务器崩溃，消息不应丢失。请注意，为了实现这一点，队列也必须被声明为持久的。

以下代码可用于接收表单数据并执行生成器:

```
<?php
chdir(dirname(__DIR__));
require_once('vendor/autoload.php');

use Acme\AmqpWrapper\WorkerSender;

$inputFilters = array(
    'invoiceNo' => FILTER_SANITIZE_NUMBER_INT,
);
$input = filter_input_array(INPUT_POST, $inputFilters);
$sender = new WorkerSender();
$sender->execute($input['invoiceNo']);
```

请使用您认为合适的任何输入净化/验证。

消费者方面的事情变得有点有趣了:

```
<?php
namespace Acme\AmqpWrapper;

use PhpAmqpLib\Connection\AMQPConnection;
use PhpAmqpLib\Message\AMQPMessage;

class WorkerReceiver
{
    /* ... SOME OTHER CODE HERE ... */

    /**
     * Process incoming request to generate pdf invoices and send them through 
     * email.
     */ 
    public function listen()
    {
        $connection = new AMQPConnection('localhost', 5672, 'guest', 'guest');
        $channel = $connection->channel();

        $channel->queue_declare(
            'invoice_queue',    #queue
            false,              #passive
            true,               #durable, make sure that RabbitMQ will never lose our queue if a crash occurs
            false,              #exclusive - queues may only be accessed by the current connection
            false               #auto delete - the queue is deleted when all consumers have finished using it
            );

        /**
         * don't dispatch a new message to a worker until it has processed and 
         * acknowledged the previous one. Instead, it will dispatch it to the 
         * next worker that is not still busy.
         */
        $channel->basic_qos(
            null,   #prefetch size - prefetch window size in octets, null meaning "no specific limit"
            1,      #prefetch count - prefetch window in terms of whole messages
            null    #global - global=null to mean that the QoS settings should apply per-consumer, global=true to mean that the QoS settings should apply per-channel
            );

        /**
         * indicate interest in consuming messages from a particular queue. When they do 
         * so, we say that they register a consumer or, simply put, subscribe to a queue.
         * Each consumer (subscription) has an identifier called a consumer tag
         */ 
        $channel->basic_consume(
            'invoice_queue',        #queue
            '',                     #consumer tag - Identifier for the consumer, valid within the current channel. just string
            false,                  #no local - TRUE: the server will not send messages to the connection that published them
            false,                  #no ack, false - acks turned on, true - off.  send a proper acknowledgment from the worker, once we're done with a task
            false,                  #exclusive - queues may only be accessed by the current connection
            false,                  #no wait - TRUE: the server will not respond to the method. The client should not wait for a reply method
            array($this, 'process') #callback
            );

        while(count($channel->callbacks)) {
            $this->log->addInfo('Waiting for incoming messages');
            $channel->wait();
        }

        $channel->close();
        $connection->close();
    }

    /**
     * process received request
     * 
     * @param AMQPMessage $msg
     */ 
    public function process(AMQPMessage $msg)
    {
        $this->generatePdf()->sendEmail();

        /**
         * If a consumer dies without sending an acknowledgement the AMQP broker 
         * will redeliver it to another consumer or, if none are available at the 
         * time, the broker will wait until at least one consumer is registered 
         * for the same queue before attempting redelivery
         */ 
        $msg->delivery_info['channel']->basic_ack($msg->delivery_info['delivery_tag']);
    }

    /**
     * Generates invoice's pdf
     * 
     * @return WorkerReceiver
     */ 
    private function generatePdf()
    {
        /**
         * Mocking a pdf generation processing time.  This will take between
         * 2 and 5 seconds
         */ 
        sleep(mt_rand(2, 5));
        return $this;
    }

    /**
     * Sends email
     * 
     * @return WorkerReceiver
     */ 
    private function sendEmail()
    {
        /**
         * Mocking email sending time.  This will take between 1 and 3 seconds
         */ 
        sleep(mt_rand(1,3));
        return $this;
    }
}
```

像往常一样，我们必须创建一个连接，派生一个通道并声明一个队列(队列的参数必须与生产者相同)。

```
<?php
/* ... SOME CODE HERE ... */

        $channel->basic_qos(
            null,   #prefetch size - prefetch window size in octets, null meaning "no specific limit"
            1,      #prefetch count - prefetch window in terms of whole messages
            null    #global - global=null to mean that the QoS settings should apply per-consumer, global=true to mean that the QoS settings should apply per-channel
            );

/* ... SOME CODE HERE ... */
```

为了拥有工作者行为(在几个进程之间分派消息)，我们必须用`$channel->basic_qos()`声明服务质量(qos)参数:

*   *预取大小*:没有具体的限制，我们可以根据需要拥有任意多的工人
*   *预取计数*:在发送回确认之前，每个工作者检索多少消息。这将使工作进程一次处理一条消息。
*   *全局*:空表示以上设置仅适用于该消费者

接下来，我们将开始消费，参数有一个关键的不同。我们将关闭自动确认，因为当我们处理完消息并准备好接收新消息时，我们将通知 RabbitMQ 服务器。

现在，我们如何发送 ack？请看一下`WorkerReceiver::process()`方法(它在接收消息时被声明为回调方法)。对`generatedPdf()`和`sendEmail()`方法的调用只是虚拟方法，模拟完成这两个任务所花费的时间。`$msg`参数不仅包含生产者发送的有效载荷，还包含生产者使用的对象的信息。我们可以用`$msg->delivery_info['channel']`提取关于生产者使用的通道的信息(它与我们用`$connection->channel();`为消费者打开的通道是相同的对象类型)。由于我们需要向生产者的通道发送一个确认，表明我们已经完成了这个过程，我们将使用它的`basic_ack()`方法，将自动生成的 delivery tag ( `$msg->delivery_info['delivery_tag']` ) RabbitMQ 作为一个参数发送，以便正确地关联 ack 属于哪个消息。

我们如何激励工人？只需创建如下所示的文件，调用`WorkerReceiver::listen()`方法:

```
<?php
chdir(dirname(__DIR__));

require_once('vendor/autoload.php');

use Acme\AmqpWrapper\WorkerReceiver;

$worker = new WorkerReceiver();

$worker->listen();
```

现在使用 php 命令(如`php worker.php`或您给上述文件起的任何名字)来启动 worker。但是等等，目的是有两个或更多的工人，不是吗？没问题，用同样的方式激发更多的工人，以便拥有同一个文件的多个进程，RabbitMQ 将注册消费者，并根据 QoS 参数在他们之间分配工作。

## 示例 2:发送 RPC 请求并期待回复

到目前为止，我们一直在向 RabbitMQ 服务器发送消息，用户不必等待回复。这对于异步流程来说是可以的，因为异步流程花费的时间可能比用户愿意花在查看“ok”消息上的时间要多。但是如果我们真的需要回复呢？让我们从一个复杂的计算中得出一些结果，这样我们就可以把它展示给用户？

假设我们有一个集中的登录服务器(单点登录),它将作为一个独立于应用程序的身份验证机制。到达这个服务器的唯一方法是通过 RabbitMQ。我们需要实现一种方法，将登录凭证发送到该服务器，并等待授权/拒绝访问响应。

我们需要实现以下模式:

和往常一样，我们先来看看制作方:

```
<?php
namespace Acme\AmqpWrapper;

use PhpAmqpLib\Connection\AMQPConnection;
use PhpAmqpLib\Message\AMQPMessage;

class RpcSender
{
    private $response;

    /**
     * @var string
     */
    private $corr_id;

    /* ...SOME OTHER CODE HERE... */

    /**
     * @param array $credentials
     * @return string
     */
    public function execute($credentials)
    {
    	$connection = new AMQPConnection('localhost', 5672, 'guest', 'guest');
		$channel = $connection->channel();

		/*
		 * creates an anonymous exclusive callback queue
		 * $callback_queue has a value like amq.gen-_U0kJVm8helFzQk9P0z9gg
		 */
		list($callback_queue, ,) = $channel->queue_declare(
			"", 	#queue
			false, 	#passive
			false, 	#durable
			true, 	#exclusive
			false	#auto delete
			);

		$channel->basic_consume(
			$callback_queue, 			#queue
			'', 						#consumer tag
			false, 						#no local
			false, 						#no ack
			false, 						#exclusive
			false, 						#no wait
			array($this, 'onResponse')	#callback
			);

		$this->response = null;

		/*
		 * $this->corr_id has a value like 53e26b393313a
		 */
		$this->corr_id = uniqid();
		$jsonCredentials = json_encode($credentials);

		/*
		 * create a message with two properties: reply_to, which is set to the 
		 * callback queue and correlation_id, which is set to a unique value for 
		 * every request
		 */
		$msg = new AMQPMessage(
		    $jsonCredentials,    #body
		    array('correlation_id' => $this->corr_id, 'reply_to' => $callback_queue)    #properties
			);

		/*
		 * The request is sent to an rpc_queue queue.
		 */
		$channel->basic_publish(
			$msg,		#message 
			'', 		#exchange
			'rpc_queue'	#routing key
			);

		while(!$this->response) {
			$channel->wait();
		}

		$channel->close();
		$connection->close();

		return $this->response;
    }

    /**
     * When a message appears, it checks the correlation_id property. If it
     * matches the value from the request it returns the response to the
     * application.
     *
     * @param AMQPMessage $rep
     */
    public function onResponse(AMQPMessage $rep) {
    	if($rep->get('correlation_id') == $this->corr_id) {
			$this->response = $rep->body;
		}
	}
}
```

看一下`RpcSender::execute`方法，请注意`$credentials`参数是一个`['username'=>'x', 'password'=>'y']`形式的数组。同样，我们像往常一样打开一个新连接并创建一个通道。

```
<?php
//...
        list($callback_queue, ,) = $channel->queue_declare(
            "",     #queue
            false,  #passive
            false,  #durable
            true,   #exclusive
            false   #auto delete
            );
//...
```

第一个区别来自于队列的声明。首先请注意，我们使用了`list()`构造来捕捉来自`$channel->queue_declare()`的结果。这是因为我们在声明队列名称时没有显式地发送它，所以我们需要找出这个队列是如何被标识的。我们只对结果数组的第一个元素感兴趣，它将是队列的唯一标识符(类似于`amq.gen-_U0kJVm8helFzQk9P0z9gg`)。第二个变化是我们需要声明这个队列是独占的，这样就不会混淆其他并发进程的结果。

另一个大的变化是，生产者也将是队列的消费者，当执行`$channel->basic_consume()`时，请注意我们提供了在声明队列时得到的`$callback_queue`值。像每个消费者一样，我们将声明一个回调以在流程收到响应时执行。

```
<?php
//...
        /*
         * $this->corr_id has a value like 53e26b393313a
         */
        $this->corr_id = uniqid();
//...
```

接下来，我们必须为消息创建一个关联 id，这只不过是每条消息的唯一标识符。在这个例子中，我们使用`uniqid()`的输出，但是你可以使用你喜欢的任何机制(只要它不产生竞争条件，不需要成为一个强大的、加密安全的 RNG)。

```
<?php
//...
        $msg = new AMQPMessage(
            $jsonCredentials,    #body
            array('correlation_id' => $this->corr_id, 'reply_to' => $callback_queue)    #properties
            );
//...
```

现在让我们创建一条消息，它与我们在前两个示例中使用的相比有了重要的变化。除了分配一个 json 编码的字符串，其中包含我们想要验证的凭证，我们还必须向`AMQPMessage`构造函数提供一个数组，其中定义了两个属性:

*   *correlation_id* :消息的标签
*   *reply_to* :声明时生成的队列标识

发布消息后，我们将评估响应，开始时将为空。当响应值保持为空时，我们将等待来自带有`$channel->wait();`的通道的响应。

一旦我们收到来自通道的响应，回调方法将被调用(`RpcSender::onResponse()`)。这个方法将把接收到的相关 id 与生成的 id 进行匹配，如果它们相同，将设置响应体，从而中断 while 循环。

RPC 消费者呢？这是:

```
<?php
namespace Acme\AmqpWrapper;

use PhpAmqpLib\Connection\AMQPConnection;
use PhpAmqpLib\Message\AMQPMessage;

class RpcReceiver
{
    /* ... SOME OTHER CODE HERE... */

    /**
     * Listens for incoming messages
     */
    public function listen()
    {
        $connection = new AMQPConnection('localhost', 5672, 'guest', 'guest');
        $channel = $connection->channel();

        $channel->queue_declare(
            'rpc_queue',    #queue 
            false,          #passive
            false,          #durable
            false,          #exclusive
            false           #autodelete
            );

        $channel->basic_qos(
            null,   #prefetch size
            1,      #prefetch count
            null    #global
            );

        $channel->basic_consume(
            'rpc_queue',                #queue
            '',                         #consumer tag
            false,                      #no local
            false,                      #no ack
            false,                      #exclusive
            false,                      #no wait
            array($this, 'callback')    #callback
            );

        while(count($channel->callbacks)) {
            $channel->wait();
        }

        $channel->close();
        $connection->close();
    }

    /**
     * Executes when a message is received.
     *
     * @param AMQPMessage $req
     */
    public function callback(AMQPMessage $req) {

        $credentials = json_decode($req->body);
    	$authResult = $this->auth($credentials);

    	/*
    	 * Creating a reply message with the same correlation id than the incoming message
    	 */
    	$msg = new AMQPMessage(
    	    json_encode(array('status' => $authResult)),            #message
    	    array('correlation_id' => $req->get('correlation_id'))  #options
    	    );

    	/*
    	 * Publishing to the same channel from the incoming message
    	 */
    	$req->delivery_info['channel']->basic_publish(
    	    $msg,                   #message
    	    '',                     #exchange
    	    $req->get('reply_to')   #routing key
    	    );

    	/*
    	 * Acknowledging the message
    	 */
    	$req->delivery_info['channel']->basic_ack(
    	    $req->delivery_info['delivery_tag'] #delivery tag
    	    );
    }

    /**
     * @param \stdClass $credentials
     * @return bool
     */
    private function auth(\stdClass $credentials) {
    	if (($credentials->username == 'admin') && ($credentials->password == 'admin')) {
    	    return true;
    	} else {
    	    return false;
    	}
    }
}
```

相同的旧连接和信道创建:)

与声明队列相同，但是这个队列将有一个预定义的名称(*RPC _ queue*’)。我们将定义 QoS 参数，因为我们将停用自动 ack，这样我们就可以在完成凭据验证并获得结果时发出通知。

```
<?php
//...
        $msg = new AMQPMessage(
            json_encode(array('status' => $authResult)),            #message
            array('correlation_id' => $req->get('correlation_id'))  #options
            );
//...
```

魔力来自于声明的回调。一旦我们完成了对凭证的认证(是的，我知道这个过程是针对静态用户名/密码值完成的，本教程不是关于如何认证凭证；) )，我们必须使用生成器创建的相同关联 id 来创建返回消息。我们可以从带有`$req->get('correlation_id')`的请求消息中提取这个值，并像在生产者中一样传递这个值。

```
<?php
//...
        $req->delivery_info['channel']->basic_publish(
            $msg,                   #message
            '',                     #exchange
            $req->get('reply_to')   #routing key
            );
//...
```

现在，我们必须将该消息发布到在生成器中创建的同一个队列中(名称为“random”的队列)。我们用`$req->get('reply_to')`提取队列的名称，并将其用作`basic_publish()`中的路由关键字。

一旦我们发布了消息，我们必须使用`$req->delivery_info['delivery_tag']`中的 delivery 标记将 ack 通知发送到带有`$req->delivery_info['channel']->basic_ack()`的通道，这样生产者就可以停止等待。

再次，启动一个倾听的过程，你就可以开始了。您甚至可以将示例 2 和示例 3 结合起来，使用一个多工作器 rpc 进程来执行身份验证请求，这样就可以通过启动几个工作器来进行扩展。

关于 RabbitMQ 和 AMQP 还有很多要说的，比如虚拟主机、交换类型、服务器管理等等……你可以在这里和[文档页面](https://www.rabbitmq.com/documentation.html)找到更多的应用模式(比如路由、主题)[。还有一个管理 RabbitMQ 的](https://www.rabbitmq.com/getstarted.html)[命令行工具](https://www.rabbitmq.com/management-cli.html)，以及一个基于 web 的界面[。](https://www.rabbitmq.com/management.html)

如果您喜欢这个教程系列，并且希望看到更多关于 MQ 和更多真实世界的用例，请在下面的评论中告诉我们！

## 分享这篇文章