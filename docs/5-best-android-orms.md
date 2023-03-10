# 5 个最好的 Android ORMs

> 原文：<https://www.sitepoint.com/5-best-android-orms/>

如果您正在开发一个 Android 应用程序，您可能需要在某个地方存储数据。您可以选择云服务(在这种情况下，使用 [SyncAdapter](https://developer.android.com/reference/android/content/AbstractThreadedSyncAdapter.html) 将是一个好主意)，或者将您的数据存储在嵌入式 SQLite 数据库中。如果您选择第二个选项，您将不得不在编写 SQL 查询、使用内容提供者(如果您想与其他应用程序共享您的数据，这很有用)或使用 [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) 之间做出选择。

在本文中，我将讨论一些您可能考虑在应用程序中使用的 Android ORMs。

## OrmLite

[OrmLite](https://ormlite.com/sqlite_java_android_orm.shtml) 是我想到的第一个 Android ORM。然而，OrmLite 不是 Android ORM，它是一个支持 SQL 数据库的 Java ORM。凡是用 Java 的地方都可以用，比如 [JDBC](https://www.oracle.com/technetwork/java/javase/jdbc/index.html) 连接、 [Spring](https://spring.io/) ，还有安卓。

它大量使用注释，比如为定义一个表的每个类使用`@DatabaseTable`，或者为类中的每个字段使用`@DatabaseField`。

使用 OrmLite 定义表格的一个简单示例如下:

```
@DatabaseTable(tableName = "users")
public class User {
    @DatabaseField(id = true)
    private String username;
    @DatabaseField
    private String password;

    public User() {
        // ORMLite needs a no-arg constructor
    }
    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    // Implementing getter and setter methods
    public String getUserame() {
        return this.username;
    }
    public void setName(String username) {
        this.username = username;
    }
    public String getPassword() {
        return this.password;
    }
    public void setPassword(String password) {
        this.password = password;
    }
}
```

OrmLite for Android 是开源的，你可以在 GitHub 上找到它。更多信息请点击这里阅读[的官方文档。](https://ormlite.com/javadoc/ormlite-core/doc-files/ormlite.html)

## sugarcrm

[SugarORM](https://satyan.github.io/sugar/index.html) 是一款专为 Android 打造的 ORM。它附带了一个既容易学习又容易记忆的 API。它自己创建必要的表，给你一个简单的方法来创建一对一和一对多的关系，并且只使用三个函数来简化 CRUD，`save()`、`delete()`和`find()`(或者`findById()`)。

将这四个`meta-data`标签添加到您的应用程序`AndroidManifest.xml`中，配置您的应用程序使用 SugarORM:

```
<meta-data android:name="DATABASE" android:value="my_database.db" />
<meta-data android:name="VERSION" android:value="1" />
<meta-data android:name="QUERY_LOG" android:value="true" />
<meta-data android:name="DOMAIN_PACKAGE_NAME" android:value="com.my-domain" />
```

现在，您可以在需要制作表格的类中扩展这个 ORM，如下所示:

```
public class User extends SugarRecord<User> {
    String username;
    String password;
    int age;
    @Ignore
    String bio; //this will be ignored by SugarORM

    public User() { }

    public User(String username, String password,int age){
        this.username = username;
        this.password = password;
        this.age = age;
    }
}
```

因此，添加新用户应该是:

```
User johndoe = new User(getContext(),"john.doe","secret",19);
johndoe.save(); //stores the new user into the database
```

删除所有 19 岁的用户将会是:

```
List<User> nineteens = User.find(User.class,"age = ?",new int[]{19});
foreach(user in nineteens) {
    user.delete();
}
```

更多信息，请阅读 SugarORM 的在线文档。

## 绿岛

说到性能，‘快’和绿岛是同义词。如其网站上所说，*“大多数实体可以以每秒几千个实体的速度被插入、更新和加载；*。如果它不是那么好，[这些应用](https://www.appbrain.com/stats/libraries/details/greendao/greendao)就不会使用它。与 OrmLite 相比，它几乎快了 4.5 倍。

greenDAO vs OrmLite

说到大小，它小于 100kb，所以不影响 APK 大小非常多。

跟随[本教程](https://blog.surecase.eu/using-greendao-with-android-studio-ide/)，使用 Android Studio 展示了绿岛在一个 Android 应用中的用法。您可以在 [GitHub](https://github.com/greenrobot/greenDAO) 上查看绿岛源代码，并阅读绿岛官方文档。

## 活跃的 Android

像其他 ORM 一样， [ActiveAndroid](https://www.activeandroid.com/) 帮助您在不编写 SQL 查询的情况下从 SQLite 存储和检索记录。

在您的项目中包含 ActiveAndroid 需要将一个`jar`文件添加到您的 Android 项目的`/libs`文件夹中。正如[入门](https://github.com/pardom/ActiveAndroid/wiki/Getting-started)指南中所述，你可以从 [GitHub](https://github.com/pardom/ActiveAndroid) 中克隆源代码，并使用 [Maven](https://maven.apache.org/) 进行编译。包括它之后，你应该把这些`meta-data`标签添加到你的应用的`AndroidManifest.xml`:

```
<meta-data android:name="AA_DB_NAME" android:value="my_database.db" />
<meta-data android:name="AA_DB_VERSION" android:value="1" />
```

添加这些标签后，您可以像这样在活动中调用`ActiveAndroid.initialize()`:

```
public class MyActivity extends Activity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ActiveAndroid.initialize(this);

        //rest of the app
    }
}
```

现在应用程序被配置为使用 ActiveAndroid，您可以通过使用`Annotations`创建模型作为 Java 类:

```
@Table(name = "User")
public class User extends Model {
    @Column(name = "username")
    public String username;

    @Column(name = "password")
    public String password;

    public User() {
        super();
    }

    public User(String username,String password) {
        super();
        this.username = username;
        this.password = password;
    }
}
```

这是一个使用 ActiveAndroid 的简单例子。[文档](https://www.activeandroid.com/)将帮助你进一步理解 ActiveAndroid ORM 的用法。

## 领域

最后， [Realm](https://realm.io/) 是一个“即将到来的”ORM，目前只存在于 Android 上。它建立在 C++之上，直接在你的硬件上运行(不解释),这使得它非常快。iOS 的代码是开源的，你可以在 [GitHub](https://github.com/realm/realm-cocoa) 上找到。

在网站上，你会发现 Objective-C 和 Swift 中的一些 Realm 用例，以及一个[注册表](https://realm.us5.list-manage1.com/subscribe?u=2aab5198c2f56be1004466570&id=245830894c)来获取 Android 版本的最新消息。

## 最后的话

这些并不是市场上唯一的 Android ORMs。其他的例子还有[和](https://www.androrm.com/)机器人和[机器人。](https://github.com/roscopeco/ormdroid)

SQL 知识是每个开发人员都应该具备的技能，但是编写 SQL 查询很无聊，尤其是在有这么多 ORM 的情况下。当它们使你的工作更简单时，为什么不首先使用它们呢？

你呢？你用什么安卓 ORM？在下面评论你的选择

## 分享这篇文章