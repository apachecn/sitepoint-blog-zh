# 队列数据结构:如何构建节点任务队列

> 原文：<https://www.sitepoint.com/implement-task-queue-node-js/>

本教程解释了队列数据结构并演示了排队系统。队列通常用于处理长时间运行的任务，如电子邮件简讯传递。下面，您将构建一个简单的节点任务队列。

一接到请求就执行任务并不总是可行的。

考虑一个电子邮件简讯管理系统。写完后，管理员必须点击一个红色的“立即发送”按钮。应用程序*可以*立即发送每封电子邮件，并显示“已完成”的响应。这对于十几条消息来说是可行的，但是对于 1000 或更多的用户来说需要多长时间呢？浏览器请求将在进程完成前超时。

另一个例子:用户可以上传任意数量的照片到图库应用程序。系统调整每个图像的大小并使其变得清晰，以适应不同的尺寸。这个过程可以在上传时运行，但是会导致每个图像的延迟。

在这些情况下，分离任务会更有效。用户收到即时响应，但任务处理在后台进行。其他应用程序或服务器处理任务并调度失败时的重试。用户可以接收警报或检查日志来确定进度。

## 什么是队列数据结构？

一个**队列**是一个数据结构，它保存了一组项目:

*   任何进程都可以在任何时候发送(或*入队*)一个项目——比如将时事通讯 X 发送给收件人 y。
*   任何进程都可以接收(或*让*出队)队列前面的项目，例如，在队列中等待时间最长的项目。

队列数据结构是一种先进先出(FIFO)结构。添加到队列中的第一个项目将是第一个出来的。

## 一个基本的 JavaScript 任务队列数据结构

您可以使用 JavaScript 数组创建任务队列。 [`push()`方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)将一个项目添加到数组的末尾，而 [`shift()`方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/shift)从开始处移除并返回一个项目:

```
const queue = [];

queue.push( 'item 1' );
queue.push( 'item 2' );

console.log( queue.shift() ); // item 1
console.log( queue.shift() ); // item 2
console.log( queue.shift() ); // undefined 
```

您的队列数据结构可以在单个数组元素中保存任何数据。您可以推送字符串、数字、布尔值、其他数组或对象。

您可以使用 ES6 类来定义任意数量的独立队列:

```
class Queue {

  constructor() { this.q = []; }
  send( item )  { this.q.push( item ); }
  receive()     { return this.q.shift(); }

}

// define two queues
const q1 = new Queue();
const q2 = new Queue();

q1.send('item 1');
q2.send('item 2');

console.log( q1.receive() ); // item 1
console.log( q1.receive() ); // undefined
console.log( q2.receive() ); // item 2 
```

这些简单的队列数据结构对于不太重要的客户端代码可能很有用，比如对 UI 更新进行排队，以便在单个 DOM 更新中进行处理。如果需要的话，localStorage 或 [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) 可以提供一定程度的数据持久性。

## 排队平台

内存队列对于复杂的服务器应用程序来说不太实用:

1.  两个或多个独立的应用程序不能(容易地)访问同一个队列。
2.  当应用程序终止时，队列数据消失。

专门构建的消息代理软件提供了更健壮的排队。平台各不相同，但提供的功能包括:

*   具有复制、分片和集群选项的数据库选择中的数据持久性
*   一系列访问协议，通常包括 HTTP 和 Web 套接字
*   任意数量的独立队列
*   延迟消息传递，消息处理可以在稍后进行
*   类似事务的支持，当处理未被确认时，消息被重新排队
*   发布-订阅模式，在这种模式下，当队列中出现新项目时，应用程序会收到一个事件

消息代理软件包括 [Redis](https://redis.io/) 、 [RabbitMQ](https://www.rabbitmq.com/) 、 [Apache ActiveMQ](http://activemq.apache.org/) 和 [Gearman](http://gearman.org/) 。云消息服务包括[亚马逊 SQS](https://aws.amazon.com/sqs/) 、 [Azure 服务总线](https://azure.microsoft.com/services/service-bus/)和[谷歌发布/订阅](https://cloud.google.com/pubsub)。

这些可能是企业级应用程序的可行选项。然而，如果您有更简单的需求并且已经使用了一个数据库，那么它们可能是多余的。

## 使用 MongoDB 作为我们的节点任务队列的消息代理

可以用几百行代码开发一个复杂的节点任务队列系统来管理队列数据结构。

这里描述的`queue-mongodb`模块使用 [MongoDB](https://www.mongodb.com/) 进行数据存储，但是相同的概念可以被任何 SQL 或 NoSQL 数据库采用。该代码可在 [GitHub](https://github.com/craigbuckler/queue-mongodb) 和 [npm](https://www.npmjs.com/package/@craigbuckler/queue-mongodb) 上获得。

## 节点任务队列项目:入门

确保您已经安装了 [Node.js](https://nodejs.org/) 14 或更高版本，然后创建一个新的项目文件夹，如`queue-test`。添加新的`package.json`文件:

```
{
  "name": "queue-test",
  "version": "1.0.0",
  "description": "Queue test",
  "type": "module",
  "scripts": {
    "send": "node ./send.js",
    "receive": "node ./receive.js"
  }
} 
```

*注意:`"type": "module"`配置项目使用 ES6 模块。`"scripts"`将发送和接收排队的项目。*

安装[队列-mongodb](https://www.npmjs.com/package/@craigbuckler/queue-mongodb) 模块:

```
npm install @craigbuckler/queue-mongodb 
```

然后用您的 MongoDB 数据库连接凭证创建一个`.env`文件。例如:

```
QUEUE_DB_HOST=localhost
QUEUE_DB_PORT=27017
QUEUE_DB_USER=root
QUEUE_DB_PASS=mysecret
QUEUE_DB_NAME=qdb
QUEUE_DB_COLL=queue 
```

*注意:这将在`qdb`数据库(`QUEUE_DB_NAME`)中创建一个`queue`集合(`QUEUE_DB_COLL`)。您可以使用现有的数据库，但要确保集合不会与另一个冲突。*

*数据库的读/写权限必须授予密码为`mysecret` ( `QUEUE_DB_PASS`)的用户`root` ( `QUEUE_DB_USER`)。如果不需要身份验证，请将这两个值都设置为空白。*

如果 MongoDB 数据库尚未运行，则启动它。有 [Docker](https://www.docker.com/) 和 [Docker Compose](https://docs.docker.com/compose/) 的人可以新建一个`docker-compose.yml`文件；

```
version: '3'

services:

  queuedb:
    environment:
      - MONGO_INITDB_ROOT_USERNAME=${QUEUE_DB_USER}
      - MONGO_INITDB_ROOT_PASSWORD=${QUEUE_DB_PASS}
    image: mongo:4.4-bionic
    container_name: queuedb
    volumes:
      - queuedata:/data/db
    ports:
      - "${QUEUE_DB_PORT}:${QUEUE_DB_PORT}"
    restart: always

volumes:
  queuedata: 
```

然后运行`docker-compose up`下载并启动带有持久数据卷的 MongoDB。

*Docker 可用 Linux、macOS、Windows 10。参见 [Docker 安装说明](https://dockerwebdev.com/tutorials/install-docker/)。*

创建一个新的`send.js`文件，将随机生成的电子邮件消息添加到名为`news`的队列中:

```
// Queue module
import { Queue } from '@craigbuckler/queue-mongodb';

// initialize queue named 'news'
const newsQ = new Queue('news');

// random name
const name = String.fromCharCode(65 + Math.random() * 26).repeat(1 + Math.random() * 10);

// add object to queue
const send = await newsQ.send({
  name:     name,
  email:    `${ name.toLowerCase() }@test.com`,
  date:     new Date(),
  message:  `Hey there, ${ name }!`
});

console.log('send', send);

// get number of items remaining in queue
console.log('items queued:', await newsQ.count());

// close connection and quit
await newsQ.close(); 
```

用`npm run send`执行它，您将看到如下输出:

```
send {
  _id: 607d692563bd6d05bb459931,
  sent: 2021-04-19T11:27:33.000Z,
  data: {
    name: 'AAA',
    email: 'aaa@test.com',
    date: 2021-04-19T11:27:33.426Z,
    message: 'Hey there, AAA!'
  }
}
items queued: 1 
```

`.send()`方法返回一个`qItem`对象，其中包含:

1.  MongoDB 文档`_id`
2.  项目最初排队的日期/时间，以及
3.  消息的副本`data`

运行脚本任意次，将更多的项目添加到队列中。每次运行时,`items queued`都会增加。

现在创建一个新的`receive.js`文件，从同一个节点任务队列中检索消息:

```
// Queue module
import { Queue } from '@craigbuckler/queue-mongodb';

// initialize queue named 'news'
const newsQ = new Queue('news');

let qItem;

do {

  qItem = await newsQ.receive();

  if (qItem) {

    console.log('\nreceive', qItem);

    // ... process qItem.data ...
    // ... to send email ...

  }

} while (qItem);

// number of items remaining in queue
console.log('items queued:', await newsQ.count());

await newsQ.close(); 
```

运行`npm run receive`获取并处理排队的项目:

```
receive {
  _id: 607d692563bd6d05bb459931,
  sent: 2021-04-19T11:27:33.000Z,
  data: {
    name: 'AAA',
    email: 'aaa@test.com',
    date: 2021-04-19T11:27:33.426Z,
    message: 'Hey there, AAA!'
  }
}
items queued: 0 
```

在这个例子中没有发送电子邮件，但是这可以使用 [Nodemailer](https://www.npmjs.com/package/nodemailer) 或另一个合适的模块来实现。

如果处理失败— *可能是因为邮件服务器关闭了(*)——一个项目可以通过以下方式重新排队:

```
newsQ.send( qItem.data, 600 ); 
```

第二个`600`参数是可选的秒数或未来日期。该命令会在 600 秒(十分钟)后将项目重新排队。

这是一个简单的例子，但是任何应用程序都可以向任意数量的队列发送数据。另一个进程，可能作为一个`cron`作业启动，可以在必要时接收和处理项目。

## `queue-mongodb`模块如何工作

传递给类构造函数的`type`字符串定义了一个队列名。`.send()`方法在传递数据添加到队列时创建一个新的 MongoDB 文档。MongoDB 文档包含:

1.  一个 MongoDB `_id`(创建日期/时间编码在值中)。
2.  队列`type`。
3.  名为`proc`的处理日期/时间值。可以设置未来时间，但当前时间是默认时间。
4.  第`data`项。这可以是任何东西:布尔值、数字、字符串、数组、对象等等。

`.receive()`方法定位在过去具有匹配的`type`和`proc`日期/时间的最老的文档。文档被格式化，返回到调用代码，并从数据库中删除。

以下章节[进一步详细描述了模块](https://github.com/craigbuckler/queue-mongodb/blob/main/index.js)。

## `queue-mongodb`模块:初始化

如有必要， [`dotenv`模块](https://www.npmjs.com/package/dotenv)读取`.env`环境变量。使用官方 [`mongodb`驱动模块](https://www.npmjs.com/package/mongodb)创建数据库连接对象:

```
// modules
import dotenv from 'dotenv';
import mongoDB from 'mongodb';

// environment variables
if (!process.env.QUEUE_DB_HOST) {
  dotenv.config();
}

// MongoDB database client
const
  dbName = process.env.QUEUE_DB_NAME || 'qdb',
  qCollectionName = process.env.QUEUE_DB_COLL || 'queue',
  qAuth = process.env.QUEUE_DB_USER ? `${ process.env.QUEUE_DB_USER }:${ process.env.QUEUE_DB_PASS || '' }@` : '',

  dbClient = new mongoDB.MongoClient(
    `mongodb://${ qAuth }${ process.env.QUEUE_DB_HOST || 'localhost' }:${ process.env.QUEUE_DB_PORT || '27017' }/`,
    { useNewUrlParser: true, useUnifiedTopology: true }
  ); 
```

`qCollection`变量保存对数据库队列集合的引用(由`QUEUE_DB_COLL`定义)。它由`dbConnect()`函数创建并返回，该函数还定义了集合模式和必要时的索引。所有的`Queue`方法运行`const q = await dbConnect();`来获取集合引用:

```
let qCollection; // queue collection

// shared connection
async function dbConnect() {

  // collection available
  if (qCollection) return qCollection;

  // connect to database
  await dbClient.connect();

  // collection defined?
  const
    db = dbClient.db( dbName ),
    colList = await db.listCollections({ name: qCollectionName }, { nameOnly: true }).toArray();

  if (!colList.length) {

    // define collection schema
    let $jsonSchema = {
      bsonType: 'object',
      required: [ 'type', 'proc', 'data' ],
      properties: {
        type: { bsonType: 'string', minLength: 1 },
        proc: { bsonType: 'date' }
      }
    };
    await db.createCollection(qCollectionName, { validator: { $jsonSchema } });

    // define indexes
    await db.collection( qCollectionName ).createIndexes([
      { key: { type: 1 } },
      { key: { proc: 1 } }
    ]);

  }

  // return queue collection
  qCollection = db.collection( qCollectionName );
  return qCollection;

} 
```

`dbClose()`函数关闭数据库连接:

```
// close MongoDB database connection
async function dbClose() {

  if (qCollection) {
    await dbClient.close();
    qCollection = null;
  }

} 
```

## `queue-mongodb`模块:`Queue`构造器

`Queue`构造函数设置队列`type`或名称:

```
export class Queue {

  constructor(type = 'DEFAULT') {

    this.type = type;

  } 
```

## `queue-mongodb`模块:`Queue.send()`方法

`.send()`方法使用适当的`type`将数据添加到队列中。它有一个可选的`delayUntil`参数，通过指定秒数或一个`Date()`，在未来的某个时间将一个项目添加到队列中。

该方法向数据库中插入一个新文档，如果不成功，则返回一个`qItem`对象({ `_id`、`sent`、`data` })或`null`:

```
 async send(data = null, delayUntil) {

    try {

      // calculate start date/time
      let proc = new Date();
      if (delayUntil instanceof Date) {
        proc = delayUntil;
      }
      else if (!isNaN(delayUntil)) {
        proc = new Date( +proc + delayUntil * 1000);
      }

      // add item to queue
      const
        q     = await dbConnect(),
        ins   = await q.insertOne({
          type: this.type, proc, data
        });

      // return qItem
      return ins && ins.insertedCount && ins.insertedId ? { _id: ins.insertedId, sent: ins.insertedId.getTimestamp(), data } : null;

    }
    catch(err) {

      console.log(`Queue.send error:\n${ err }`);
      return null;

    }

  } 
```

## `queue-mongodb`模块:`Queue.receive()`方法

`.receive()`方法检索并删除数据库中具有特定的`type`和过去的`proc`日期/时间的最早的排队项目。如果没有可用内容或出现错误，则返回一个`qItem`对象({ `_id`、`sent`、`data` })或`null`:

```
 async receive() {

    try {

      // find and delete next item on queue
      const
        now = new Date(),
        q   = await dbConnect(),
        rec = await q.findOneAndDelete(
          {
            type: this.type,
            proc: { $lt: now }
          },
          {
            sort: { proc: 1 }
          }
        );

      const v = rec && rec.value;

      // return qItem
      return v ? { _id: v._id, sent: v._id.getTimestamp(), data: v.data } : null;

    }
    catch(err) {

      console.log(`Queue.receive error:\n${ err }`);
      return null;

    }

  } 
```

## `queue-mongodb`模块:`Queue.remove()`方法

`.remove()`方法删除由`.send()`方法返回的`qItem`对象({ `_id`、`sent`、`data` })标识的队列项。它可用于移除队列中的项目，而不管它在队列中的位置。

当出现错误时，该方法返回已删除文档的数量(通常为 1)或`null`:

```
 async remove(qItem) {

    // no item to remove
    if (!qItem || !qItem._id) return null;

    try {

      const
        q   = await dbConnect(),
        del = await q.deleteOne({ _id: qItem._id });

      return del.deletedCount;

    }
    catch(err) {

      console.log(`Queue.remove error:\n${ err }`);
      return null;

    }

  } 
```

## `queue-mongodb`模块:`Queue.purge()`方法

`.purge()`方法删除同一`type`的所有排队项目，并返回删除次数:

```
 async purge() {

    try {

      const
        q   = await dbConnect(),
        del = await q.deleteMany({ type: this.type });

      return del.deletedCount;

    }
    catch(err) {

      console.log(`Queue.purge error:\n${ err }`);
      return null;

    }

  } 
```

## `queue-mongodb`模块:`Queue.count()`方法

`.count()`方法返回同一个`type`的排队项目的数量:

```
 async count() {

    try {

      const q = await dbConnect();
      return await q.countDocuments({ type: this.type });

    }
    catch(err) {

      console.log(`Queue.count error:\n${ err }`);
      return null;

    }

  } 
```

## `queue-mongodb`模块:`Queue.close()`方法

`.close()`方法运行`dbClose()`函数来终止数据库连接，以便 Node.js 事件循环可以结束:

```
 async close() {

    try {

      await dbClose();

    }
    catch(err) {

      console.log(`Queue.close error:\n${ err }`);
      return null;

    }

  }

// end of class
} 
```

## 新队列

对于任何具有计算开销大、可能导致瓶颈的功能的 web 应用程序来说，队列都是一个需要考虑的因素。他们可以通过将应用程序分解成更小、更快、更健壮的流程来提高性能和维护。专用的 message broker 软件是一个选项，但是像我们今天构建的节点任务队列这样的简单排队系统只需要几十行代码就可以实现。

## 分享这篇文章