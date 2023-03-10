# Java 和 MongoDB 4.0 对多文档 ACID 事务的支持

> 原文：<https://www.sitepoint.com/java-and-mongodb-4-0-support-for-multi-document-acid-transactions/>

*本文是与 [MongoDB](https://www.mongodb.com/) 合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

[MongoDB 4.0](https://www.mongodb.com/transactions?utm_medium=sp-synd&utm_source=sitepoint&utm_content=4.0-demo&jmp=sp-ref) 增加对多文档 ACID 事务的支持。

但是等等…这是否意味着 MongoDB 直到现在才支持事务？
不，实际上 MongoDB 一直支持单文档事务形式的事务。MongoDB 4.0 将这些事务保证扩展到了多个文档、多个语句、多个集合和多个数据库。如果没有任何形式的事务数据完整性保证，数据库有什么用？

在我们开始这篇博文之前，您可以在这里找到所有代码并尝试多文档 ACID 事务。

> ## 快速启动
> 
> ### 步骤 1:启动 MongoDB
> 
> 在本地主机端口 27017 上启动最低版本为 4.0.0 的单节点 MongoDB 复制集。
> 
> 如果您使用 Docker:
> 
> *   可以用`start-mongo.sh`。
> *   做完了就可以用`stop-mongo.sh`。
> *   如果想用 Mongo Shell 连接 MongoDB，可以使用`connect-mongo.sh`。
> 
> 如果您喜欢手动启动 mongod:
> 
> *   `mkdir /tmp/data && mongod --dbpath /tmp/data --replSet rs`
> *   `mongo --eval 'rs.initiate()'`
> 
> ### 第二步:启动 Java
> 
> 本演示包含两个主程序:`ChangeStreams.java`和`Transactions.java`。
> 
> *   更改流允许您在 MongoDB 集合或数据库中发生任何数据更改时得到通知。
> *   交易过程就是演示本身。
> 
> 您需要两个 shells 来运行它们。
> 
> 如果您使用 Docker:
> 
> 第一个外壳:
> 
> ```
> ./compile-docker.sh
> ./change-streams-docker.sh 
> ```
> 
> 第二层:
> 
> ```
> ./transactions-docker.sh 
> ```
> 
> 如果您不使用 Docker，您将需要安装 Maven 3.5.X 和 JDK 10(或最低 JDK 8，但您需要更新 pom.xml 中的 Java 版本):
> 
> 第一个外壳:
> 
> ```
> ./compile.sh
> ./change-streams.sh 
> ```
> 
> 第二层:
> 
> ```
> ./transactions.sh 
> ```

让我们将现有的单文档事务与 MongoDB 4.0 的 ACID 兼容多文档事务进行比较，看看我们如何利用 Java 的这一新特性。

## MongoDB 4.0 之前的版本

即使在 MongoDB 3.6 和更早的版本中，每个写操作都被表示为一个**事务，其作用范围是存储层中的单个文档**的级别。因为文档模型将相关数据集合在一起，否则这些数据将在一个表格模式中跨独立的父子表进行建模，所以 MongoDB 的原子单文档操作提供了满足大多数应用程序的数据完整性需求的事务语义。

修改多个文档的每个典型写操作实际上都发生在几个独立的事务中:每个文档一个事务。

让我们以一个非常简单的股票管理应用程序为例。

首先，我需要一个 MongoDB 副本集，所以请按照上面给出的说明启动 MongoDB。

现在让我们将以下文档插入到一个`product`集合中:

```
MongoDB Enterprise rs:PRIMARY> db.product.insertMany([
    { "_id" : "beer", "price" : NumberDecimal("3.75"), "stock" : NumberInt(5) }, 
    { "_id" : "wine", "price" : NumberDecimal("7.5"), "stock" : NumberInt(3) }
]) 
```

假设有一个促销活动，我们想给顾客所有产品打八折。

但是在应用这个折扣之前，我们希望监控这些操作何时在 MongoDB 中随着变更流发生。

在 Mongo Shell 中执行以下命令:

```
cursor = db.product.watch([{$match: {operationType: "update"}}]);
while (!cursor.isExhausted()) {
  if (cursor.hasNext()) {
    print(tojson(cursor.next()));
  }
} 
```

将此外壳放在一边，打开另一个 Mongo 外壳并应用折扣:

```
PRIMARY> db.product.updateMany({}, {$mul: {price:0.8}})
{ "acknowledged" : true, "matchedCount" : 2, "modifiedCount" : 2 }
PRIMARY> db.product.find().pretty()
{
    "_id" : "beer",
    "price" : NumberDecimal("3.00000000000000000"),
    "stock" : 5
}
{
    "_id" : "wine",
    "price" : NumberDecimal("6.0000000000000000"),
    "stock" : 3
} 
```

如您所见，两个文档都是用一个命令行更新的，但不是在一个事务中。
下面是我们在更改流外壳中看到的内容:

```
{
    "_id" : {
        "_data" : "825B4637290000000129295A1004374DC58C611E4C8DA4E5EDE9CF309AC5463C5F6964003C62656572000004"
    },
    "operationType" : "update",
    "clusterTime" : Timestamp(1531328297, 1),
    "ns" : {
        "db" : "test",
        "coll" : "product"
    },
    "documentKey" : {
        "_id" : "beer"
    },
    "updateDescription" : {
        "updatedFields" : {
            "price" : NumberDecimal("3.00000000000000000")
        },
        "removedFields" : [ ]
    }
}
{
    "_id" : {
        "_data" : "825B4637290000000229295A1004374DC58C611E4C8DA4E5EDE9CF309AC5463C5F6964003C77696E65000004"
    },
    "operationType" : "update",
    "clusterTime" : Timestamp(1531328297, 2),
    "ns" : {
        "db" : "test",
        "coll" : "product"
    },
    "documentKey" : {
        "_id" : "wine"
    },
    "updateDescription" : {
        "updatedFields" : {
            "price" : NumberDecimal("6.0000000000000000")
        },
        "removedFields" : [ ]
    }
} 
```

正如您所看到的，两个操作的集群时间(参见`clusterTime`键)是不同的:操作发生在同一秒钟，但是时间戳的计数器增加了 1。

因此，这里每个文档一次更新一个，即使这种情况发生得非常快，其他人也可以在更新运行时阅读这些文档，并且只看到两个有折扣的产品中的一个。

大多数情况下，您可以在 MongoDB 数据库中容忍这种情况，因为我们尽可能地尝试在同一个文档中嵌入紧密链接或相关的数据。
因此，在一个事务中对同一文档进行两次更新:

```
PRIMARY> db.product.update({_id: "wine"},{$inc: {stock:1}, $set: {description : "It’s the best wine on Earth"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
PRIMARY> db.product.findOne({_id: "wine"})
{
    "_id" : "wine",
    "price" : NumberDecimal("6.0000000000000000"),
    "stock" : 4,
    "description" : "It’s the best wine on Earth"
} 
```

然而，有时候，您不能在一个文档中建模所有相关的数据，并且有很多合理的理由选择不嵌入文档。

## 具有多文档 ACID 事务的 MongoDB 4.0

MongoDB 中的多文档 ACID 事务与您可能已经从传统关系数据库中了解到的非常相似。

MongoDB 的事务是一组相关操作的对话集，必须通过全有或全无的执行来自动提交或完全回滚。

事务用于确保操作是原子的，即使跨多个集合或数据库。因此，对于快照隔离读取，另一个用户只能看到所有操作，或者看不到任何操作。

现在让我们在示例中添加一个购物车。

对于这个例子，需要两个集合，因为我们处理两个不同的业务实体:库存管理和每个客户在购物时可以创建的购物车。这些集合中每个文档的生命周期是不同的。

产品集合中的文件代表我要出售的物品。这包含产品的当前价格和当前库存。我创造了一个 POJO 来代表它:Product.java。

```
{ "_id" : "beer", "price" : NumberDecimal("3"), "stock" : NumberInt(5) } 
```

当客户在购物车中添加第一件商品时，购物车被创建，当客户继续结账或离开网站时，购物车被移除。我创造了一个 POJO 来代表它:Cart.java。

```
{
    "_id" : "Alice",
    "items" : [
        {
            "price" : NumberDecimal("3"),
            "productId" : "beer",
            "quantity" : NumberInt(2)
        }
    ]
} 
```

这里的挑战在于，我不能卖出超过我拥有的数量:如果我有 5 瓶啤酒要卖，我不能有超过 5 瓶啤酒分配到不同的客户购物车。

为了确保这一点，我必须确保创建或更新客户端购物车的操作与股票更新是原子性的。这就是多文档事务发挥作用的地方。如果有人试图购买我股票中没有的东西，交易必须失败。我将在产品库存上添加一个约束:

```
db.createCollection("product", {
   validator: {
      $jsonSchema: {
         bsonType: "object",
         required: [ "_id", "price", "stock" ],
         properties: {
            _id: {
               bsonType: "string",
               description: "must be a string and is required"
            },
            price: {
               bsonType: "decimal",
               minimum: 0,
               description: "must be a positive decimal and is required"
            },
            stock: {
               bsonType: "int",
               minimum: 0,
               description: "must be a positive integer and is required"
            }
         }
      }
   }
}) 
```

> 节点，这已经包含在 Java 代码中。

为了监控我们的示例，我们将使用 MongoDB 3.6 中引入的 MongoDB 变更流。

在这个名为`ChangeStreams.java`的进程的每个线程中，我将监控两个集合中的一个，并打印每个操作及其相关的集群时间。

```
// package and imports

public class ChangeStreams {

    private static final Bson filterUpdate = Filters.eq("operationType", "update");
    private static final Bson filterInsertUpdate = Filters.in("operationType", "insert", "update");
    private static final String jsonSchema = "{ $jsonSchema: { bsonType: \"object\", required: [ \"_id\", \"price\", \"stock\" ], properties: { _id: { bsonType: \"string\", description: \"must be a string and is required\" }, price: { bsonType: \"decimal\", minimum: 0, description: \"must be a positive decimal and is required\" }, stock: { bsonType: \"int\", minimum: 0, description: \"must be a positive integer and is required\" } } } } ";

    public static void main(String[] args) {
        MongoDatabase db = initMongoDB(args[0]);
        MongoCollection<Cart> cartCollection = db.getCollection("cart", Cart.class);
        MongoCollection<Product> productCollection = db.getCollection("product", Product.class);
        ExecutorService executor = Executors.newFixedThreadPool(2);
        executor.submit(() -> watchChangeStream(productCollection, filterUpdate));
        executor.submit(() -> watchChangeStream(cartCollection, filterInsertUpdate));
        ScheduledExecutorService scheduled = Executors.newSingleThreadScheduledExecutor();
        scheduled.scheduleWithFixedDelay(System.out::println, 0, 1, TimeUnit.SECONDS);
    }

    private static void watchChangeStream(MongoCollection<?> collection, Bson filter) {
        System.out.println("Watching " + collection.getNamespace());
        List<Bson> pipeline = Collections.singletonList(Aggregates.match(filter));
        collection.watch(pipeline)
                  .fullDocument(FullDocument.UPDATE_LOOKUP)
                  .forEach((Consumer<ChangeStreamDocument<?>>) doc -> System.out.println(
                          doc.getClusterTime() + " => " + doc.getFullDocument()));
    }

    private static MongoDatabase initMongoDB(String mongodbURI) {
        getLogger("org.mongodb.driver").setLevel(Level.SEVERE);
        CodecRegistry providers = fromProviders(PojoCodecProvider.builder().register("com.mongodb.models").build());
        CodecRegistry codecRegistry = fromRegistries(MongoClient.getDefaultCodecRegistry(), providers);
        MongoClientOptions.Builder options = new MongoClientOptions.Builder().codecRegistry(codecRegistry);
        MongoClientURI uri = new MongoClientURI(mongodbURI, options);
        MongoClient client = new MongoClient(uri);
        MongoDatabase db = client.getDatabase("test");
        db.drop();
        db.createCollection("cart");
        db.createCollection("product", productJsonSchemaValidator());
        return db;
    }

    private static CreateCollectionOptions productJsonSchemaValidator() {
        return new CreateCollectionOptions().validationOptions(
                new ValidationOptions().validationAction(ValidationAction.ERROR).validator(BsonDocument.parse(jsonSchema)));
    }
} 
```

在这个例子中，我们有 5 瓶啤酒要卖。
Alice 想买两瓶啤酒，但我们不打算使用新的 MongoDB 4.0 多文档交易。我们将在变更流中观察两个操作:一个创建购物车，一个在两个不同的集群时间更新库存。

然后，Alice 在她的购物车中又添加了 2 瓶啤酒，这次我们将使用一个事务。更改流中的结果将是两个操作在同一集群时间发生。

最后，她将尝试订购两瓶额外的啤酒，但是 jsonSchema 验证器将使产品更新失败并导致回滚。我们不会在变更流中看到任何东西。
下面是`Transaction.java`的源代码:

```
// package and import

public class Transactions {

    private static MongoClient client;
    private static MongoCollection<Cart> cartCollection;
    private static MongoCollection<Product> productCollection;

    private final BigDecimal BEER_PRICE = BigDecimal.valueOf(3);
    private final String BEER_ID = "beer";

    private final Bson stockUpdate = inc("stock", -2);
    private final Bson filterId = eq("_id", BEER_ID);
    private final Bson filterAlice = eq("_id", "Alice");
    private final Bson matchBeer = elemMatch("items", eq("productId", "beer"));
    private final Bson incrementBeers = inc("items.$.quantity", 2);

    public static void main(String[] args) {
        initMongoDB(args[0]);
        new Transactions().demo();
    }

    private static void initMongoDB(String mongodbURI) {
        getLogger("org.mongodb.driver").setLevel(Level.SEVERE);
        CodecRegistry codecRegistry = fromRegistries(MongoClient.getDefaultCodecRegistry(), fromProviders(
                PojoCodecProvider.builder().register("com.mongodb.models").build()));
        MongoClientOptions.Builder options = new MongoClientOptions.Builder().codecRegistry(codecRegistry);
        MongoClientURI uri = new MongoClientURI(mongodbURI, options);
        client = new MongoClient(uri);
        MongoDatabase db = client.getDatabase("test");
        cartCollection = db.getCollection("cart", Cart.class);
        productCollection = db.getCollection("product", Product.class);
    }

    private void demo() {
        clearCollections();
        insertProductBeer();
        printDatabaseState();
        System.out.println("#########  NO  TRANSACTION #########");
        System.out.println("Alice wants 2 beers.");
        System.out.println("We have to create a cart in the 'cart' collection and update the stock in the 'product' collection.");
        System.out.println("The 2 actions are correlated but can not be executed on the same cluster time.");
        System.out.println("Any error blocking one operation could result in stock error or beer sale we don't own.");
        System.out.println("---------------------------------------------------------------------------");
        aliceWantsTwoBeers();
        sleep();
        removingBeersFromStock();
        System.out.println("####################################\n");
        printDatabaseState();
        sleep();
        System.out.println("\n######### WITH TRANSACTION #########");
        System.out.println("Alice wants 2 extra beers.");
        System.out.println("Now we can update the 2 collections simultaneously.");
        System.out.println("The 2 operations only happen when the transaction is committed.");
        System.out.println("---------------------------------------------------------------------------");
        aliceWantsTwoExtraBeersInTransactionThenCommitOrRollback();
        sleep();
        System.out.println("\n######### WITH TRANSACTION #########");
        System.out.println("Alice wants 2 extra beers.");
        System.out.println("This time we do not have enough beers in stock so the transaction will rollback.");
        System.out.println("---------------------------------------------------------------------------");
        aliceWantsTwoExtraBeersInTransactionThenCommitOrRollback();
        client.close();
    }

    private void aliceWantsTwoExtraBeersInTransactionThenCommitOrRollback() {
        ClientSession session = client.startSession();
        try {
            session.startTransaction(TransactionOptions.builder().writeConcern(WriteConcern.MAJORITY).build());
            aliceWantsTwoExtraBeers(session);
            sleep();
            removingBeerFromStock(session);
            session.commitTransaction();
        } catch (MongoCommandException e) {
            session.abortTransaction();
            System.out.println("####### ROLLBACK TRANSACTION #######");
        } finally {
            session.close();
            System.out.println("####################################\n");
            printDatabaseState();
        }
    }

    private void removingBeersFromStock() {
        System.out.println("Trying to update beer stock : -2 beers.");
        try {
            productCollection.updateOne(filterId, stockUpdate);
        } catch (MongoCommandException e) {
            System.out.println("#####   MongoCommandException  #####");
            System.out.println("##### STOCK CANNOT BE NEGATIVE #####");
            throw e;
        }
    }

    private void removingBeerFromStock(ClientSession session) {
        System.out.println("Trying to update beer stock : -2 beers.");
        try {
            productCollection.updateOne(session, filterId, stockUpdate);
        } catch (MongoCommandException e) {
            System.out.println("#####   MongoCommandException  #####");
            System.out.println("##### STOCK CANNOT BE NEGATIVE #####");
            throw e;
        }
    }

    private void aliceWantsTwoBeers() {
        System.out.println("Alice adds 2 beers in her cart.");
        cartCollection.insertOne(new Cart("Alice", Collections.singletonList(new Cart.Item(BEER_ID, 2, BEER_PRICE))));
    }

    private void aliceWantsTwoExtraBeers(ClientSession session) {
        System.out.println("Updating Alice cart : adding 2 beers.");
        cartCollection.updateOne(session, and(filterAlice, matchBeer), incrementBeers);
    }

    private void insertProductBeer() {
        productCollection.insertOne(new Product(BEER_ID, 5, BEER_PRICE));
    }

    private void clearCollections() {
        productCollection.deleteMany(new BsonDocument());
        cartCollection.deleteMany(new BsonDocument());
    }

    private void printDatabaseState() {
        System.out.println("Database state:");
        printProducts(productCollection.find().into(new ArrayList<>()));
        printCarts(cartCollection.find().into(new ArrayList<>()));
        System.out.println();
    }

    private void printProducts(List<Product> products) {
        products.forEach(System.out::println);
    }

    private void printCarts(List<Cart> carts) {
        if (carts.isEmpty())
            System.out.println("No carts...");
        else
            carts.forEach(System.out::println);
    }

    private void sleep() {
        System.out.println("Sleeping 3 seconds...");
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            System.err.println("Oups...");
            e.printStackTrace();
        }
    }
} 
```

这里是更改流的控制台:

```
$ ./change-streams.sh 

Watching test.cart
Watching test.product

Timestamp{value=6570052721557110786, seconds=1529709604, inc=2} => Cart{id='Alice', items=[Item{productId=beer, quantity=2, price=3}]}

Timestamp{value=6570052734442012673, seconds=1529709607, inc=1} => Product{id='beer', stock=3, price=3}

Timestamp{value=6570052764506783745, seconds=1529709614, inc=1} => Product{id='beer', stock=1, price=3}
Timestamp{value=6570052764506783745, seconds=1529709614, inc=1} => Cart{id='Alice', items=[Item{productId=beer, quantity=4, price=3}]} 
```

正如您在这里看到的，我们只获得了四个操作，因为最后两个操作从未提交到数据库，因此更改流没有任何内容可以显示。

您还可以注意到，前两个集群时间是不同的，因为我们没有为前两个操作使用事务，后两个操作共享相同的集群时间，因为我们使用了新的 MongoDB 4.0 多文档事务系统，因此它们是原子的。

这里是事务 java 进程的控制台，它总结了我前面所说的一切。

```
$ ./transactions.sh 
Database state:
Product{id='beer', stock=5, price=3}
No carts...

#########  NO  TRANSACTION #########
Alice wants 2 beers.
We have to create a cart in the 'cart' collection and update the stock in the 'product' collection.
The 2 actions are correlated but can not be executed on the same cluster time.
Any error blocking one operation could result in stock error or a sale of beer that we can’t fulfill as we have no stock.
---------------------------------------------------------------------------
Alice adds 2 beers in her cart.
Sleeping 3 seconds...
Trying to update beer stock : -2 beers.
####################################

Database state:
Product{id='beer', stock=3, price=3}
Cart{id='Alice', items=[Item{productId=beer, quantity=2, price=3}]}

Sleeping 3 seconds...

######### WITH TRANSACTION #########
Alice wants 2 extra beers.
Now we can update the 2 collections simultaneously.
The 2 operations only happen when the transaction is committed.
---------------------------------------------------------------------------
Updating Alice cart : adding 2 beers.
Sleeping 3 seconds...
Trying to update beer stock : -2 beers.
####################################

Database state:
Product{id='beer', stock=1, price=3}
Cart{id='Alice', items=[Item{productId=beer, quantity=4, price=3}]}

Sleeping 3 seconds...

######### WITH TRANSACTION #########
Alice wants 2 extra beers.
This time we do not have enough beers in stock so the transaction will rollback.
---------------------------------------------------------------------------
Updating Alice cart : adding 2 beers.
Sleeping 3 seconds...
Trying to update beer stock : -2 beers.
#####   MongoCommandException  #####
##### STOCK CANNOT BE NEGATIVE #####
####### ROLLBACK TRANSACTION #######
####################################

Database state:
Product{id='beer', stock=1, price=3}
Cart{id='Alice', items=[Item{productId=beer, quantity=4, price=3}]} 
```

## 后续步骤

感谢您花时间阅读我的帖子——我希望您觉得有用和有趣。
提醒一下，所有代码都可以在 Github 库上[找到，供你试验。](https://github.com/MaBeuLux88/mongodb-4.0-demos?utm_medium=sp-synd&utm_source=sitepoint&utm_content=4.0-demo&jmp=sp-ref)

如果您正在寻找一种非常简单的方法来开始使用 MongoDB，您只需在云中点击 5 次我们的 [MongoDB Atlas 数据库服务](https://www.mongodb.com/cloud/atlas?utm_medium=sp-synd&utm_source=sitepoint&utm_content=4.0-demo&jmp=sp-ref)即可。

此外，多文档 ACID 事务并不是 MongoDB 4.0 中唯一的新特性，所以请随意查看我们关于 [MongoDB 大学 M040 的免费课程:MongoDB 4.0 中的新特性和工具](https://university.mongodb.com/courses/M040/about?utm_medium=sp-synd&utm_source=sitepoint&utm_content=4.0-demo&jmp=sp-ref)以及我们的[MongoDB 4.0 中的新特性指南](https://www.mongodb.com/collateral/mongodb-40-whats-new?utm_medium=sp-synd&utm_source=sitepoint&utm_content=4.0-demo&jmp=sp-ref)，在那里您可以了解更多关于原生类型转换、新的可视化和分析工具以及 Kubernetes 集成的信息。

## 分享这篇文章