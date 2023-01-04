# 用龙目语整理你的 POJOs

> 原文：<https://www.sitepoint.com/declutter-pojos-with-lombok-tutorial/>

### 目录 

*   [介绍龙目岛](#introducinglombok)
*   [使用龙目语](#usinglombok)
*   [实现构建器](#implementingbuilder)
*   [减少龙目岛样板文件](#reducinglombokboilerplate)
*   [用 Lombok 定义自定义方法](#definingcustommethodswithlombok)
*   [龙目岛的运作方式](#howlombokworks)
*   [德龙博克](#delombok)
*   [总结](#summary)
*   [评论](#comments)

我对 Java 又爱又恨。一方面，它是一种成熟的编程语言，有各种各样的框架和库，使得开发相对容易。另一方面，它非常冗长，需要为普通任务编写大量样板代码。随着 Java 8 中 lambdas 和 streams 的引入，情况有所好转，但在某些领域仍不尽如人意，比如编写普通的旧 Java 对象 [POJO](https://en.wikipedia.org/wiki/Plain_Old_Java_Object) 。在这篇文章中，我将向你展示如何用 [Lombok](https://projectlombok.org/) 用几行代码重写 POJOs。

## 冗长的 POJOs

看看这个普通的 POJO 类，它只有三个字段:`name`、`surname`和`age`。它有 POJO 类共有的元素:getters、setters、`equals`、`hashCode`和`toString`方法。

```
public class User {

    private String name;
    private String surname;
    private int age;

    public User(String name, String surname, int age) {
        this.name = name;
        this.surname = surname;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSurname() {
        return surname;
    }

    public void setSurname(String surname) {
        this.surname = surname;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        User user = (User) o;
        return age == user.age
                && Objects.equals(name, user.name)
                && Objects.equals(surname, user.surname);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, surname, age);
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", surname='" + surname + '\'' +
                ", age=" + age +
                '}';
    }

} 
```

仅仅实现这个简单的包含三个字段的类就花了我们 59 行代码。当然，IDE 可以为我们生成它，但是请记住，代码被读取的次数要比它被编写的次数多得多。因此，比表示这个简单概念所需的大量代码行更糟糕的是，代码无法清楚地传达实现的意图。回答这些问题需要多长时间:

*   哪些字段没有 getter/setter？
*   在`equals` / `hashCode` / `toString`方法中有哪些字段没有使用？
*   哪些字段有非标准的 getter/setter？
*   哪些 getter/setter 有非公共修饰符？

得到这些答案不是不可能，但比应该的要难得多。[信噪比](https://en.wikipedia.org/wiki/Signal-to-noise_ratio)太低了！

如果我们需要实现[构建器模式](https://en.wikipedia.org/wiki/Builder_pattern)，情况会变得更糟。除了所有的 POJO 代码之外，我们还需要为另一个类维护类似的样板文件——大约是无用代码数量的两倍。好像这还不够糟糕，当我们需要添加一个新字段时，我们必须在多个地方更新 builder 和 POJO 类。这使得很容易遗漏一些东西并引入一个 bug。

## 介绍龙目岛

很高兴有更好的方法。Lombok 是一个库，它允许我们使用一组简单而强大的注释来定义 POJO 类。这些注释指定特定字段是否应该有 getter/setter，是否应该参与`equals` / `hashCode` / `toString`方法，等等。Lombok 已经存在了几年，并被用于许多商业和开源项目。

所以让我们将 Lombok 添加到我们的项目中。这非常简单，只需将以下依赖项添加到您的`pom.xml`文件中:

```
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <version>1.16.10</version>
  <scope>provided</scope>
</dependency> 
```

在这之后，你的 Lombok 代码将会顺利编译，但是如果你使用的是 IDE，你就有麻烦了。由于 Lombok 注释会生成新代码(参见下面的[Lombok 如何工作](#howlombokworks))，IDE 应该知道什么注释隐式地添加了什么方法。幸运的是，有几个针对不同 ide 的插件，所以你可以挑选你最喜欢的。

如果你使用 Intellij IDEA，你需要下载一个第三方[插件](https://plugins.jetbrains.com/plugin/6317?pr=idea%20plugin)来支持 Lombok。如果你使用 Eclipse，你需要下载 [lombok.jar](https://projectlombok.org/download.html) 并执行它。它将安装 Eclipse 插件。对于 NetBeans，您需要相同的 JAR，并且必须[启用注释处理](https://projectlombok.org/setup/netbeans.html)。

现在我们准备用 Lombok 改进我们的代码。

## 使用 Lombok

Lombok 提供了一些基本的注释来定义一个特定的字段是否应该有一个访问器方法，或者是否应该在`equals` / `hashCode` / `toString`方法中使用:

```
@EqualsAndHashCode
@ToString
@AllArgsConstructor
public class User {

    @Setter
    @Getter
    private String name;

    @Setter
    @Getter
    private String surname;

    @Setter
    @Getter
    private int age;

} 
```

如你所见，我们有五个基本的注释。`@EqualsAndHashCode`和`@ToString`指示 Lombok 生成将使用类中所有字段的`equals`、`hashCode,`和`toString`方法。`@AllArgsConstructor`将创建一个构造函数，它的参数和类中的字段一样多。注释被应用到每一个字段，并将创建 getters 和 setters。

现在，我们可以像使用常规 POJO 类一样使用这个类:

```
User user = new User("John", "Doe", 32);
user.setAge(30);
user.equals(new User("John", "Doe", 30)); // true 
```

如果您不希望注释使用所有字段，您可以提供一个可选参数来指定哪些字段应该用于生成方法。因此，如果我们不想在`toString`方法中使用`age`字段，您可以这样做:

```
@EqualsAndHashCode
@ToString(of = {"name", "surname"})
@AllArgsConstructor
public class User {

    @Getter
    @Setter
    private String name;

    @Getter
    @Setter
    private String surname;

    @Getter
    @Setter
    private int age;

} 
```

我认为这段代码已经比我们开始时的要好得多，但是它看起来仍然不够简洁。这些重复的`@Getter` / `@Setter`标注怎么办？如果您只需要在类中公开几个字段，这样使用它们是很方便的，但是如果您需要为所有字段生成访问器，这就变得很烦人了。

为了使这种情况更加简洁，Lombok 允许在类级别上使用这些注释。如果在类级别上使用了`@Getter` / `@Setter`注释，Lombok 将为类中的所有字段生成 getters 和 setters。

```
@EqualsAndHashCode
@ToString
@AllArgsConstructor
@Getter
@Setter
public class User {

    private String name;
    private String surname;
    private int age;

} 
```

这并没有改变类的行为，但是现在代码更加简洁了。

### 实现生成器

在这篇文章的开始，我认为构建者增加了另一层样板文件。Lombok 将编写构建器的练习变成了一行程序。要做到这一点，只需在类的顶部添加`@Builder`注释，您将拥有一个带有流体接口的构建器供您使用:

```
@EqualsAndHashCode
@ToString
@AllArgsConstructor
@Getter
@Setter
@Builder
public class User {

    private String name;
    private String surname;
    private int age;

} 
```

现在，我们可以使用生成器创建类的一个实例:

```
User user = User.builder()
      .name("John")
      .surname("Doe")
      .age(32)
      .build(); 
```

### 减少 Lombok 样板文件

结果代码可读性更好，但是这一组重复的注释怎么办？大多数 POJOs 都有这些方法，反复使用相同的注释仍然很麻烦。Lombok 提供了两种注释，可以使这变得更加简单:

*   `@Data`——替换`@Getter`、`@Setter`、`@EqualsAndHashCode`、`@ToString`和`@RequiredArgsConstructor`的注释
*   `@Value`–和前面一样，但是生成一个不可变的类，没有设置器

`@Data`和`@Value`注释的名字来源于所谓的*数据类*和*值类*。*数据类*是可变类，通过 getters 和 setters 公开它们的字段。 [*另一方面，值类*](http://blog.joda.org/2014/03/valjos-value-java-objects.html) 是不可变的，除了`equals`、`hashCode`和`toString`方法之外，通常没有逻辑。

所以我们可以用两个注释重写我们的类:

```
@Data
@Builder
public class User {

    private String name;
    private String surname;
    private int age;

} 
```

这个例子不仅更短，而且更好地表达了这段代码的意思。很明显，这个类有三个字段，它们都有 getters 和 setters，它们都用在`hashCode` / `equals` / `toString`方法中，并且有一个 builder 类。

### 用 Lombok 定义自定义方法

你可能会问的一个问题是，如果你需要定义一个自定义的 getter 或 setter 怎么办？如果您需要为`age`值添加自定义边界检查，该怎么办？

在这种情况下，Lombok 的行为非常简单:如果 Lomobok 将要生成的方法已经在类中，它不会生成新的方法。因此，它永远不会覆盖您的方法，只会向类中添加新方法。

![](img/12192e095c9a0f95c20995d8c048d90a.png)

## 龙目岛的运作方式

那么龙目岛是如何施展魔法的呢？原来它依赖于一个官方的 Java 标准和一个巨大的黑客。

但在此之前，我们必须更好地理解 Java 编译器。在编译期间，它做一些繁重的工作，将源代码转换成一个名为[抽象语法树](https://en.wikipedia.org/wiki/Abstract_syntax_tree) (AST)的树结构。AST 包含关于原始代码的所有信息，比如类、方法和程序语句。在其上，编译器执行各种检查和转换，然后使用产生的 AST 生成最终的字节码指令。

基于此，Java 6 引入了*可插拔注释处理 API* (由 [JSR 269](https://www.jcp.org/en/jsr/detail?id=269) 标准化)，允许 Java 库在编译过程中执行自定义代码。该标准的初衷是库将只检查 ast，特别是注释，并使用它们来实现定制代码验证或新源文件的生成。

但是 Lombok 的创建者发现他们可以用不同的方式使用这个功能。通过使用非公共 API，他们设法修改编译器提供的 ast，以便在编译阶段添加新的方法、字段甚至类。然后，编译器使用这个修改后的 AST 为 Lombok 生成的方法和字段生成字节码，就好像它们是由开发人员编写的一样。

## 德龙博克

并不是每个添加到项目中的依赖项都会永远存在，有时我们需要去掉一个才能前进。幸运的是，Lombok 很容易让我们使用一个叫做`delombok`的过程将所有“Lombok”的类还原为普通的 Java。为此，您需要调用`lombok.jar`中提供的`delombok`命令:

```
java -jar lombok.jar delombok src -d src-delomboked 
```

这将为 Lombok 通常在编译期间旋转的所有方法和类生成源代码，并将其写入指定的文件夹。

您甚至可以将`delombok`任务添加到 Ant 脚本中:

```
<mkdir dir="build/src-delomboked" />
<delombok verbose="true" encoding="UTF-8"
          to="build/src-delomboked" from="src">
    <format value="suppressWarnings:skip" />
</delombok> 
```

如果您想保持代码不变，但需要为带有 Lombok 注释的代码生成 JavaDoc，后一种方法很方便。

## 摘要

正如您所看到的，Lombok 可以显著地整理您的 Java 代码。它使用简洁的注释来生成重复和冗长的构建器类和方法，如 getters、setters 和 constructors。它可以很容易地帮助你摆脱成千上万行样板代码，即使是在一个中等规模的项目。Lombok 还允许你使你的代码更有表现力，更简洁，并能帮助你避免一些错误。最后，它还具有足够的通用性，可用于从控制台应用程序到 web 应用程序的各种项目。

## 分享这篇文章