# 使用 CDI/Weld 注入 JPA/Hibernate 实体管理器

> 原文：<https://www.sitepoint.com/cdi-weld-inject-jpa-hibernate-entity-managers/>

### 目录 

*   [创建可注入实体管理器](#creatinganinjectableentitymanager)
*   [使用@PersistentContext 注释](#usingthepersistentcontextannotation)
*   [使用生产者方法](#usingaproducermethod)
*   [用基本数据访问层(DAL)抽象数据访问](#abstractingdataaccesswithabasicdataaccesslayerdal)
*   [一个简单的应用层](#asimpleapplicationlayer)
*   [总结](#summary)
*   [评论](#comments)

在这篇文章中，我们将了解在开发一个全面的 [JPA](https://en.wikipedia.org/wiki/Java_Persistence_API) / [Hibernate](http://hibernate.org/orm) 应用程序时，利用 [CDI](https://docs.oracle.com/javaee/7/tutorial/) (上下文和依赖注入)/ [Weld](http://weld.cdi-spec.org/) 的力量是多么容易。CDI 固有的许可性质允许将几乎任何类转换成可注入的组件，从而使 Java EE 资源的注入像注入 POJOs 一样容易。这意味着，举例来说，[实体管理器](http://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html)甚至[数据源](https://docs.oracle.com/javase/tutorial/jdbc/basics/sqldatasources.html)对象可以通过使用一些自定义限定符被注入到任何地方。

这是一个非常强大——但却被低估了——的特性，经常被 CDI 新手所忽视，他们不知道这个标准提供了开箱即用的功能。现在让我们看看如何使实体管理器成为可注入的资源，并从头开始构建一个简单的 JPA 应用程序！

在[最近的一篇文章](https://www.sitepoint.com/introduction-contexts-dependency-injection-cdi/)中，我介绍了 CDI / Weld 串联的基础知识，从使用`@Default`、`@Alternative`和`@Produces`注释，到使用一些更精炼的特性，比如多态注入点和定制限定符。我用它们开发了一个简单的独立应用程序，展示了如何在运行时将一个简单接口的不同实现者注入到一个客户机对象中，以解析控制台中输入的字符串。我们现在将使用这些技能，所以如果你不完全确定如何做这些事情，[仔细阅读一下](https://www.sitepoint.com/introduction-contexts-dependency-injection-cdi/)——我会等着。

考虑到我们将在本演练中大量使用 JPA / Hibernate，我假设您至少对它们有一个最低限度的背景了解——否则请查看[本 Hibernate 教程](https://www.sitepoint.com/hibernate-introduction-persisting-java-objects/)。

## 创建可注入的实体管理器

任何基于 CDI 的 JPA 应用程序的关键是创建一个可注入的实体管理器。值得注意的是，不管我们使用什么方法来获取管理器，一旦它成为可注入的 Java EE 资源，本文剩余部分中显示的所有示例都同样有效。从那时起，跨不同层创建和注入各种对象就变得非常简单了。

因此，我们需要解决的第一个任务就是:使实体管理器成为可注入的资源。这样做，我们可以将 JPA 绑定到 Hibernate(参考 JPA 实现),并在一些简单的 JPA 实体上运行 CRUD 操作，而不会有任何麻烦。

事实上，我们可以选择一些简单的方法来实现这一点。

### 使用`@PersistentContext`注释

最不费力的方法是*，它只能与 Java EE 应用服务器*一起工作，比如 [Apache TomEE](http://tomee.apache.org/apache-tomee.html) 、[JBoss widfly](http://wildfly.org/)或 [Jetty](http://www.eclipse.org/jetty) ，这就是`@PersistenceContext`注释。正如我们所预料的，这种方法将一个[持久化上下文](http://docs.oracle.com/javaee/7/api/javax/persistence/PersistenceContext.html)(当然还有一个持久化单元)绑定到一个实体管理器，管理器的生命周期完全由容器管理(也称为容器管理的实体管理器)。

从广义上讲，这种方法可以通过使用自定义限定符来实现，如下所示:

```
@Qualifier
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.METHOD,
        ElementType.TYPE, ElementType.PARAMETER})
public @interface MySQLDatabase {}

public class EntityManagerProducer {

    @Produces
    @PersistenceContext(unitName = "my-persistence-unit")
    @MySQLDatabase
    private EntityManager entityManager;

} 
```

在这种情况下，`unitName`属性指定了与相应的持久性上下文相关联的示例持久性单元的名称。因此，如果您打算使用这种方法，请确保该名称与 Hibernate 的`persistence.xml`文件中指定的名称相匹配。

有了这些代码，一个简单的 [DAO](https://en.wikipedia.org/wiki/Data_access_object) 类(在构造函数中使用实体管理器)可以定义如下:

```
public class MyDao {

    private EntityManager entityManager;

    @Inject
    public MyDao(@MySQLDatabase EntityManager entityManager) {
        this.entityManager = entityManager;
    }

} 
```

虽然这将与完全合格的 Java EE 容器一起工作(如果您的系统上已经安装了一个，请随意尝试)，但是我们不想依赖这种特定的方法，因为我们不会使用 Java EE 容器来运行本文中介绍的 JPA 应用程序。

### 使用生产者方法

简单的 Java、CDI 和 Weld 应该能帮我们完成这项工作。这种环境下我们怎么做经理的注入？我们可以将非托管实体管理器的创建封装在一个[生产者方法](https://www.sitepoint.com/introduction-contexts-dependency-injection-cdi/#usingtheproducesannotation)中，并绑定一个自定义限定符，如下所示:

```
@Qualifier
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.METHOD,
        ElementType.TYPE, ElementType.PARAMETER})
public @interface MySQLDatabase {}

public class EntityManagerProducer {

    @Produces
    @MySQLDatabase
    public EntityManager createEntityManager() {
        return Persistence
                .createEntityManagerFactory("my-persistence-unit")
                .createEntityManager();
    }

    public void close(
            @Disposes @MySQLDatabase EntityManager entityManager) {
        entityManager.close();
    }

} 
```

在这种情况下，`createEntityManager()`方法负责通过实体管理器工厂创建非托管实体管理器。与第一种方法一样，传递给`createEntityManagerFactory()`方法的持久性单元的名称必须与 Hibernate 的配置文件中定义的名称相同。在你试图弄清楚为什么 Hibernate 找不到一个持久化单元来工作之前，一定要做到这一点。

这是最基本的 JPA，所以这里唯一值得强调的是使用了[`@Disposes`注释](http://docs.oracle.com/javaee/7/api/javax/enterprise/inject/Disposes.html)。它通知 CDI 容器这个方法关闭了实体管理器，这使得容器在释放管理器之前调用它。

在这个阶段，我们已经设法用一个简单的生产者方法创建了一个完全可注入的实体管理器。但是让我们想一想:如果我们不打算以一种富有成效的方式使用经理，我们为什么要为这个过程的曲折而烦恼呢？呀！

正如我们前面看到的，一个典型的用例是将管理器注入到一个 DAO 类中，这将允许我们通过一个容易使用的 API 对一些 JPA 实体执行 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 操作，并且最好的是，不必从上到下向客户端代码公开管理器的 API。设计这个精炼、抽象的 API 最有效的方法是实现一个简单的[数据访问层](https://en.wikipedia.org/wiki/Data_access_layer) (DAL)。CDI 使构建这一层变得轻而易举，但是像往常一样，一个例子将帮助我们更容易地理解这一过程的内部工作。

![Published by Sean Michael Ragan under CC-BY 2.0](img/9e36e1d83a5ed5922fff39661f726126.png)

[发表](https://www.flickr.com/photos/seanmichaelragan/5224386526 "Source on Flickr")由[西恩·迈克尔拉冈](https://www.flickr.com/photos/seanmichaelragan/ "Author on Flickr")下 [CC-BY 2.0](https://creativecommons.org/licenses/by/2.0/ "Link to Licence")

## 用基本数据访问层(DAL)抽象数据访问

虽然通过使用实体管理器作为一个独立的组件来运行 JPA 实体上的 CRUD 操作当然是可能的，但是这样做我们将会错过面向对象设计的三大支柱的好处。第一个是依赖注入本身(这就是为什么 CDI 中有阿迪)，第二个是关注点分离，第三个是抽象。

数据访问层是访问底层存储机制的附加层(在本例中是 MySQL 数据库，但也可以是其他任何东西)。通过实现至少一个基本的 DAL，我们将一举两得:一方面，我们将使用 CDI 将实体管理器注入该层，另一方面，我们将向客户端代码公开一个简洁的抽象接口来访问所讨论的存储。

在这种情况下，该层将由一个通用接口和一个实现组成(尽管在运行时还可以添加更多的实现来交换持久性机制，例如，除了实体关系数据库之外的其他机制):

```
public interface EntityDao<T> {

    T find(int id);

    List<T> findAll(String table);

    void update(int id, Consumer<T>... updates) throws Exception;

    void save(T entity);

    void remove(int id);

} 
```

上面的接口模拟了一个典型的 CRUD API，使用它的实现者将能够在给定的 t 类型实体上运行 CRUD 操作。

另一方面，实现者稍微复杂一些，但是它简单地展示了如何将非托管实体管理器注入到 DAO 类的构造函数中:

```
public class BaseEntityDao<T> implements EntityDao<T> {

    private EntityManager entityManager;
    private Class<T> entityClass;

    @Inject
    public BaseEntityDao(
            @MySQLDatabase EntityManager entityManager,
            @UserClass Class<T> entityClass) {
        this.entityManager = entityManager;
        this.entityClass = entityClass;
    }

    public T find(int id) {
        return entityManager.find(entityClass, id);
    }

    public List<T> findAll(String table) {
        String str = "select e from table e";
        String jpql = str.replace("table", table);
        return entityManager.createQuery(jpql).getResultList();
    }

    public void update(int id, Consumer<T>... updates) throws Exception {
        T entity = find(id);
        Arrays.stream(updates).forEach(up -> up.accept(entity));
        beginTransaction();
        commitTransaction();
    }

    public void save(T entity) {
        beginTransaction();
        entityManager.persist(entity);
        commitTransaction();
    }

    public void remove(int id) {
        T entity = find(id);
        beginTransaction();
        entityManager.remove(entity);
        commitTransaction();
    }

    private void beginTransaction() {
        try {
            entityManager.getTransaction().begin();
        } catch (IllegalStateException e) {
            rollBackTransaction();
        }
    }

    private void commitTransaction() {
        try {
            entityManager.getTransaction().commit();
        } catch (IllegalStateException | RollbackException e) {
            rollBackTransaction();
        }
    }

    private void rollBackTransaction() {
        try {
            entityManager.getTransaction().rollback();
        } catch (IllegalStateException | PersistenceException e) {
            e.printStackTrace();
        }
    }

} 
```

*(注意:我想把相应的功劳归于我们的 Java 频道编辑 [Nicolai Parlog](https://www.sitepoint.com/author/nicolaip/) ，他在 GitHub 上提供了一些富有成效的反馈后，提供了一个比我最初编写的`update()`方法更健壮的实现)*。

围绕经典的[聚合](https://en.wikipedia.org/wiki/Object_composition)关系建模，`BaseEntityDao`类只是一个带有声明性 API 的适配器，它对所提供的实体执行 CRUD 操作。但是在 API 背后推动所有这些功能的类的实际主力(也就是 adaptee)是注入的实体管理器！看看使用 CDI 将管理器注入 DAO 类有多容易？我打赌你知道。

更好的是，该类只向客户端代码公开 CRUD 方法，同时巧妙地隐藏了实体管理器的整个 API，因为在这个特定的领域中不需要它。

此外，`BaseEntityDao`声明了对建模用户实体的域类的依赖。以下是注入该类所需的自定义限定符和生成器:

```
@Qualifier
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.METHOD,
        ElementType.TYPE, ElementType.PARAMETER})
public @interface UserClass {}

public class UserClassProducer {

    @Produces
    @UserClass
    public Class createUserClass() {
        return User.class;
    }

} 
```

`UserClassProducer`类是一个典型的 CDI 生产者，它创建了一个`User`实体类。为了简洁起见，我没有在这里包括实体类，因为它看起来和以前的 [Hibernate post](https://www.sitepoint.com/hibernate-introduction-persisting-java-objects/) 中显示的一模一样。你可以在那里随意查看。

至此，我们已经实现了一个功能性数据访问层，它使用一个 DAO 类对一些用户实体执行 CRUD 操作。虽然这非常方便，因为该类可以在不同的上下文和场景中有效地使用，但这里缺少一个微小的细节。

虽然不是严格强制的，但最好至少实现一个瘦应用层，并将让用户通过数据访问层运行 CRUD 操作所需的所有功能都放入其中。这将更加清楚如何在 JPA 应用程序中利用 CDI / Weld 作为底层 DI 机制。

## 一个简单的应用层

我们简洁的数据访问层可以在非常广泛的环境中使用，从 web 和移动应用程序到基于桌面的应用程序。选择几乎是无限的。不过，为了简单起见，我们将基于控制台中显示的一组数字选项，使用它从 MySQL 数据库中获取、更新和删除用户实体。

下面是如何实现一个简单的应用层，其功能归结为根据控制台中输入的字符串在用户实体上执行特定的 CRUD 操作。当然，实际运行这些操作的依赖项是`BaseEntityDao`类的一个实例，它和一个`BufferedReader`对象一起被注入到构造函数中。这个合作者是用生产者方法创建的，如[CDI 介绍文章](https://www.sitepoint.com/introduction-contexts-dependency-injection-cdi/)所示。

```
public class UserApplication {

    private EntityDao<User> userDao;
    private BufferedReader userInputReader;

    @Inject
    public UserApplication(
            BaseEntityDao<User> userDao,
            BufferedReader userInputReader) {
        this.userDao = userDao;
        this.userInputReader = userInputReader;
    }

    public void run() throws Exception {
        // runs until the user quits
        Boolean running = true;
        while (running) {
            System.out.println("Enter an option: "
                    + "1) Insert a new user. "
                    + "2) Find a user. "
                    + "3) List all users "
                    + "4) Edit a user. "
                    + "5) Delete a user. "
                    + "6) Quit the application");
            running = runUserOperation(readUserInput());
        }
    }

    private boolean runUserOperation(String option) throws Exception {
        switch (option) {
            case "1":
                persistNewUser();
                return true;
            case "2":
                fetchExistingUser();
                return true;
            case "3":
                fetchAllExistingUsers();
                return true;
            case "4":
                updateExistingUser();
                return true;
            case "5":
                removeExistingUser();
                return true;
            case "6":
                return false;
        }
        return true;
    }

    private void persistNewUser() throws IOException {
        String name = requestStringInput("the name of the user");
        String email = requestStringInput("the email of the user");
        userDao.save(new User(name, email));
    }

    private void fetchExistingUser() throws IOException {
        int id = requestIntegerInput("the user ID");
        User user = userDao.find(id);
        System.out.println(user);
    }

    private void fetchAllExistingUsers() throws IOException {
        userDao.findAll("User").stream().forEach(System.out::println);
    }

    private void updateExistingUser() throws Exception {
        int id = requestIntegerInput("the user ID");
        String name = requestStringInput("the name of the user");
        String email = requestStringInput("the email of the user");
        userDao.update(id,
                user -> user.setName(name),
                user -> user.setEmail(email));
    }

    private void removeExistingUser() throws IOException {
        int id = requestIntegerInput("the user ID");
        userDao.remove(id);
    }

    private String readUserInput() throws IOException {
        return userInputReader.readLine();
    }

    private String requestStringInput(String request) throws IOException {
        System.out.printf("Enter %s: ", request);
        return readUserInput();
    }

    private int requestIntegerInput(String request) throws IOException {
        System.out.printf("Enter %s: ", request);
        return Integer.parseInt(readUserInput());
    }

} 
```

最后但同样重要的是，我们应该从我们的 IDE 中启动这个应用程序，看看它是否真的做了它承诺的事情。为此，我们需要以编程方式引导 Weld，并从 Weld 容器中获取一个`UserApplication`类的实例，如下所示。

```
public class Main {

    public static void main(String[] args) throws IOException {
        Weld weld = new Weld();
        WeldContainer container = weld.initialize();
        UserApplication userApplication = container.instance()
                .select(UserApplication.class)
                .get();
        userApplication.run();
        weld.shutdown();
    }

} 
```

如果一切顺利，我们应该被提示在控制台中输入一个数字选项。这将触发从相应的 MySQL 数据库中插入、获取、更新或删除用户实体(除了当我们输入选项 6 时，因为这将退出程序)。

除了该示例有限的、相当简单的功能之外，它还方便地展示了如何在一个成熟的 JPA 应用程序的实现中使用 CDI / Weld，以及该标准在以一种毫不费力的方式进行依赖注入时是多么强大。因此，无论你想把什么样的复杂程度推进到你的类的依赖关系中，无论是 POJOs、实体管理器、数据源对象，你说吧:CDI 将在幕后为你构建你的对象图，最棒的是，只需要很少的要求。

## 摘要

CDI / Weld tandem 使得构建 JPA 应用程序成为一个简单的过程，因为该标准有助于创建可注入的实体管理器，要么使用`@PersistentContext`注释，要么结合*生产者*方法和定制限定符。

不管你用什么方法组装你的对象图(因为你在做依赖注入，对吗？)，CDI 就在这里呆着。有些人可能会认为该标准过于冗长，并以用一堆注释和工厂不必要地污染您的代码为代价来执行注入，当然他们是对的，至少在某种程度上是对的。毕竟，理想情况下，注射器应该以透明、安静的方式工作，从而保持其*外部*条件的完整性。应用程序逻辑应该总是完全不知道注射器的存在。

但是拥有这样一种远离实用主义的乌托邦心态，并不能给我们买到任何真正有用的东西。我们都知道，CDI(以及所有可用的 ORM 框架)热切地使用反射 API 来完成它的工作，以及一组标准化的注释。

如果你不介意这样生活，那么 CDI 会让你的生活轻松很多。另一方面，如果你更传统，更喜欢使用简单的、老派的工厂和 T2 的构建者来构建你的对象图，那也很好。

## 分享这篇文章