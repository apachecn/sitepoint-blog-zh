# Java 序列化:构建持久电话簿

> 原文：<https://www.sitepoint.com/java-serialization-building-a-persistent-phone-book/>

序列化是一个强大的工具。它允许我们在磁盘上存储对象，并在必要时在内存中重建它们——也许是在程序重新启动时。对于任何想要超越“运行并忘记”类型的程序的人来说，序列化是必不可少的。我们可以序列化对象，在程序中创造一种持久性，这样当程序结束时，我们收集的信息不会丢失或忘记。让我们看看如何在 Java 中实现序列化

Oracle 的人是这样定义可序列化对象的:

> 序列化对象意味着将其状态转换为字节流，以便字节流可以还原为对象的副本。

程序员序列化一个对象可能有很多原因，但是我们将把重点放在把它存储在一个文件中以便以后访问的原因上。

在这种情况下，反序列化是将对象的序列化形式还原为对象的副本以便按预期使用的过程。您可以序列化任何对象，只要该对象是实现`java.io.Serializable`接口或其子接口、`java.io.Externalizable`本身的类的实例，或者是实现该接口的类的子类。

## 应用程序

我们将在控制台中构建一个简单的电话簿应用程序。该程序将允许用户添加联系人到他们的电话簿，查看他们的联系人，删除联系人，并将他们的联系人保存到文件中以备后用。当用户结束程序并稍后重新启动它时，他们将被要求输入他们想从中加载电话簿联系人的文件的名称。如果包含电话簿联系人的序列化形式的文件存在，电话簿将从磁盘加载，并可供用户操作。

与本文相关的代码可以在这里找到[。](https://github.com/sitepoint-editors/sitepoint-serialized-java-phone-book)

## 焦点

遵循[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)，本项目共有`PhoneBookApp`(主班)、`PhoneBook`、`PhoneBookEntry`、`PhoneBookFileManager`六个班，两个包。然而，为了节省时间，我们将只深入我们的主类和包含封装我们的电话簿功能的类的包，并且我们将只关注每个类中与序列化相关的部分。

## 电话簿应用程序

```
public class PhoneBookApp {
    //the name of the file to save and or load a phone book to or from
    private static String phoneBookFileName = "default-phone-book";
    //the phone book to store all the user's contacts
    private static PhoneBook phoneBook;
    //initialize a Scanner to capture user input
    private static Scanner userInputScanner = new Scanner(System.in);
    public static void main(String[] args) {
        Logger.message("Starting Phone Book App!");
        loadPhoneBook();

        //forever
        for(;;) {
            //show the menu
            showMenu();
        }
    }

    private static void loadPhoneBook() {...}
    private static void showMenu() {...}
    private static void handleUserMenuSelection(){...}
    private enum UserOption{...} 
} 
```

我们的`PhoneBookApp`类处理运行我们的程序来与用户交互。程序首先让用户知道我们正在加载电话簿。我们的`loadPhoneBook()`方法被调用来完成这项工作，方法是向用户询问他们想要从中加载电话簿的文件的名称。首先，该文件不应该存在，所以我们将告诉用户我们将创建一个新的`PhoneBook`来存储他们的联系人。这个新的电话簿由`phoneBook`变量表示，到时候它的条目将用提供的名称保存在一个文件中。如果从磁盘加载电话簿，它将被返回并存储在`phoneBook`:

```
private static void loadPhoneBook() {
        Logger.prompt("Where do you want to load your phone book from? File name: ");
        if(userInputScanner.hasNextLine()) {
            phoneBookFileName = userInputScanner.nextLine();
        }

        //try to load the user's phone book with the file name
        phoneBook = PhoneBook.load(phoneBookFileName);
        if(phoneBook != null) {
            //great, the phone book was loaded
            Logger.message(format("Loaded your %s phone book of %d contacts.", 
                    phoneBookFileName, phoneBook.getSize())
            );
        } else {
            //no phone book loaded. create new one
            phoneBook = new PhoneBook(phoneBookFileName);
            Logger.message("Opened a new phone book at " + phoneBookFileName);
        }
    } 
```

main 方法通过永远向用户显示可能的操作菜单来结束，或者直到用户退出。用户可以查看存储在电话簿中的联系人，添加新联系人，删除联系人，将联系人保存到电话簿的文件中，以及退出以结束程序。这些动作由我们的`UserOption`枚举表示，并在我们的`handleUserMenuSelection()`方法中处理。它只是接收用户输入，并将其与带有`switch`的`UserOption`匹配，以完成动作。

## 电话簿

```
public class PhoneBook {
    //the name of the file to store this phone book in disk. Unchangeable
    private final String fileName;
    /*Stores entries for this phone book. 
    The entries of this map may be referred to as contacts*/
    private final HashMap<String,PhoneBookEntry> entriesMap = new HashMap<>();
    //the number of unsaved changes, such as new or removed contacts, to this phone book.
    private int numUnsavedChanges = 0;

    public PhoneBook(String fileName) {}
    public Collection<PhoneBookEntry> getEntries() {}
    public String getFileName() {...}
    public int getSize() {...}
    public int getNumUnsavedChanges() {...}

    public AddContactResult addContact(String name, String number) {...}

    public void addFromDisk(Collection<PhoneBookEntry> phoneBookEntries) {...}

    public boolean deleteContact(String name) {...}
    public void display() {...}
    public boolean isValidName(String name) {...}
    private boolean isValidPhoneNumber(String number) {}
    public boolean save() {...}
    public static PhoneBook load(String fileName) {...}
    public enum AddContactResult {...}
} 
```

我们班将制作电话簿。它将处理对用户电话簿中联系人的所有操作。要实例化一个新的`PhoneBook`，我们只需要向它的构造函数提供保存它的文件名。电话簿的联系人条目存储在一个`HashMap`中，其中联系人的名字是确保每个名字只映射到一个`PhoneBookEntry`的关键。如果你想要重名，可以随意切换到一个`List`。

`PhoneBook`类提供了实例方法来添加和删除电话簿中的联系人，并将联系人保存到磁盘上的文件中。该类还提供了一个`static`加载方法，该方法接受一个文件名来加载并返回一个`PhoneBook`。该类的`save()`和`load()`方法参考了它们在 package-local `PhoneBookFileManager`类中对应的方法:

```
public boolean save() {
    boolean success = PhoneBookFileManager.save(this);
    if(success) numUnsavedChanges = 0;
    return success;
}

public static PhoneBook load(String fileName) {
    return PhoneBookFileManager.load((fileName));
} 
```

## PhoneBookEntry

`PhoneBookEntry`类的一个实例负责保存电话簿联系人的信息。它必须保存并显示联系人的姓名和电话号码。就这样，对吧？嗯，差不多了。这门课比看起来更重要。让我解释一下。

这个类对我们的电话簿的序列化很有帮助。如果还不清楚的话，认识到电话簿是一个抽象的概念是很重要的。物理的是构成电话簿的联系人。因此，我们程序中最重要的部分是`PhoneBookEntry`,因为它模拟了电话簿联系人。如果我们有了联系人，我们就自动有了一个电话簿，因为我们可以很容易地用他们来构造和填充电话簿。因此，我们只需要将联系人存储在磁盘上的文件中。

太好了，那我们怎么做？

```
class PhoneBookEntry implements Serializable {
    //the name of this contact.
    private final String name;
    //the number of this contact.
    private String number;
    /*whether or not this contact is new and unsaved. 
    This won't be serialized.*/
    private transient boolean isNew;

    public PhoneBookEntry(String name, String number) {
        this.name = name;
        this.number = number;
        this.isNew = true;
    }

    public void setNumber(String number) {...}
    public String getName() {...}

    public void setIsNew(boolean isNew) {...}

    @Override
    public String toString() {...}
} 
```

注意这个类实现了`Serializable`。正如我上面解释的，这意味着它的实例是可序列化的:能够被转换成字节流。作为字节流，我们可以将对象写出到文件中。等等，就这样了？差不多了，但是还有一些事情你应该知道。

所有标识类的信息都记录在序列化流中。这意味着存储在序列化对象中的所有信息都将在我们写出它时存储在文件中。但是，如果您不希望字段成为写出的字节流的一部分，该怎么办呢？您很幸运:Java 提供了一个名为`transient`的便利修饰符，您可以使用它在序列化过程中忽略一个字段。如果你看一下我们的`PhoneBookEntry`定义，你会发现我们忽略了`isNew`字段。因此，只有联系人的`name`和`number`将成为其字节流的一部分。一旦我们涵盖了所有这些，我们可以继续序列化我们的对象。

## PhoneBookManager

```
class PhoneBookFileManager {

    protected static boolean save(PhoneBook phoneBook) {...}

    protected static PhoneBook load(String fileName) {...}

    private static String getFileNameWithExtension(String fileName) {...}

    private static void closeCloseable(Closeable closeable) {...}
} 
```

根据单一责任原则，我们的`PhoneBook`类并不直接负责将电话簿保存到磁盘上的文件中，也不直接负责从磁盘上的文件中加载电话簿；`PhoneBookFileManager`承担了这个责任。这个类的最后两个方法是简单的实用方法，由更重要的`save()`和`load()`方法使用。让我们来探讨后两者。

`save()`方法负责序列化所提供的`PhoneBook`的联系人，并将它们写到磁盘上电话簿的`fileName`实例字段所标识的文件中。虽然这些方法都有很好的文档记录，但让我快速解释一下它们是如何工作的。

```
protected static boolean save(PhoneBook phoneBook) {
    if(phoneBook != null) {
        String fileName = phoneBook.getFileName();

        //make sure the file is a txt file
        String fileNameAndExt = getFileNameWithExtension(fileName);

        FileOutputStream fileOutputStream = null;
        ObjectOutputStream objectOutputStream = null;
        try {
            //create a file output stream to write the objects to
            fileOutputStream = new FileOutputStream(fileNameAndExt);
            /*create an object output stream 
            to write out the objects to the file*/
            objectOutputStream = new ObjectOutputStream(fileOutputStream);

            /*convert the collection of phone book entries into a LinkedList
            because LinkedLists implement Serializable*/
            LinkedList<PhoneBookEntry> serializableList = 
                new LinkedList<>(phoneBook.getEntries());
            //write the serializable list to the object output stream
            objectOutputStream.writeObject(serializableList);
            //flush the object output stream
            objectOutputStream.flush();

            /*set each entry's isNew value to false 
            because they are saved now.*/
            for(PhoneBookEntry entry: serializableList) {
                entry.setIsNew(false);
            }

            //SUCCESS!
            return true;
        } catch (IOException e) {
            //fail
            e.printStackTrace();
        } finally {
            /*before proceeding, 
            close output streams if they were opened*/
            closeCloseable(fileOutputStream);
            closeCloseable(objectOutputStream);
        }
    }

    //fail
    return false;
} 
```

### 序列化电话簿条目

为了序列化电话簿的联系人，我们获取它的文件名和扩展名来实例化一个`FileOutputStream`。使用我们的`fileOutputStream`，我们实例化了一个`ObjectOutputStream`:我们可以通过它把对象写到文件中。接下来，我们必须从电话簿中获取联系人。

调用我们的`phoneBook`的`getEntries()`实例方法将返回存储在电话簿中的所有`PhoneBookEntry`实例的`Collection`。因为`Collection`接口不实现`Serializable`，我们必须将其转换成实现的`LinkedList`或`ArrayList`；我选择`LinkedList`是因为它和我有关(想想吧)。

现在我们有了可序列化的对象，我们可以将它写入我们的`objectOutputStream`并将其刷新到`fileOutputStream`。这就是序列化我们的电话簿联系人并将他们保存到一个文件。使用我们的`try-catch`块来捕捉任何可能出现的错误，在返回保存操作的结果之前，我们用一个`finally`块来关闭输出流。如果你打开你提供的用来保存联系人的文件，你会看到它充满了数据，尽管人类无法阅读。

### 反序列化电话簿条目

在从文件中加载联系人时:

```
protected static PhoneBook load(String fileName) {
    if(fileName != null && !fileName.trim().isEmpty()) {
        //make sure the file is a txt file
        String fileNameWithExt = getFileNameWithExtension(fileName);

        FileInputStream fileInputStream = null;
        ObjectInputStream objectinputstream = null;
        try {

            /*create the file input stream with 
            the fileNameWithExt to read the objects from*/
            fileInputStream = new FileInputStream(fileNameWithExt);
            /*create an object input stream on the file input stream 
            to read in the objects from the file*/
            objectinputstream = new ObjectInputStream(fileInputStream);

            /*read the deserialized object from the object input stream 
            and cast it to a collection of PhoneBookEntry*/
            Collection<PhoneBookEntry> deserializedPhoneBookEntries = 
                (Collection<PhoneBookEntry>) objectinputstream.readObject();

            //create a new PhoneBook to load the deserialized entries into
            PhoneBook phoneBook = new PhoneBook(fileName);
            //add the collection of phone book entries to the phone book
            phoneBook.addFromFile(deserializedPhoneBookEntries);

            //SUCCESSS! Rreturn the loaded phone book
            return phoneBook;
        } catch (FileNotFoundException e) {
            //fail
            Logger.debug(format("Loading phone book from %s failed." 
                + " No phone book found at that directory.",
                    fileNameWithExt));
        } catch (IOException e) {
            //fail
            e.printStackTrace();
            Logger.debug(format("Loading phone book from %s failed. %s.",
                    fileNameWithExt, e.getMessage())
            );
        } catch (ClassNotFoundException e) {
            //fail
            e.printStackTrace();
            Logger.debug(format("Loading phone book from %s failed. " +
                "Error deserializing data and converting to proper object.",
                 fileNameWithExt)
            );
        } finally {
            //before proceeding, close input streams if they were opened
            closeCloseable(fileInputStream);
            closeCloseable(objectinputstream);
        }
    }

    //fail
    return null;
} 
```

除了方向完全相反之外，`load()`方法的工作方式很像`save()`。我们不是开一辆`FileOutputStream`的`ObjectOutputStream`，而是开一辆`FileInputStream`的`ObjectInputStream`。正如您所期望的，我们调用我们的`objectInputStream`的`readObject()`实例方法来读取作为对象的文件内容。我们必须决定读入什么类型的对象，并将其存储在适当的变量中。因为我们向文件中写出了一个`Collection`，所以我们应该这样读回它；这就是为什么我们将 read 对象转换为一个`Collection`。

现在我们已经有了反序列化的`PhoneBookEntry`集合，我们可以从中重构一个`PhoneBook`对象。在`try`块的末尾，我们用提供的文件名实例化了一个新的`PhoneBook`，并用条目集合作为唯一参数调用它的`addFromFile()`方法。我们通过关闭输入流并将加载的`PhoneBook`对象返回给调用者来完成这个方法。这就是`PhoneBook.addFromFile()`的样子:

```
public void addFromFile(Collection<PhoneBookEntry> phoneBookEntries) {
    if(phoneBookEntries != null) {
        for (PhoneBookEntry entry : phoneBookEntries) {
            if (entry != null) {
                entriesMap.put(entry.getName(), entry);
            }
        }
    }
} 
```

与`PhoneBook.addContact()`不同，这种方法不会在将联系人的姓名或号码添加到电话簿条目映射之前验证它，因为我们相信文件没有损坏。

![phones](img/010ae751caf41d97b27b1dfb566e4886.png)

## 运行程序

为了测试和观察序列化和反序列化功能，您需要运行该程序两次。首先，运行它来添加联系人并保存它。您需要在结束程序之前保存文件，以便它将电话簿的条目序列化到提供的文件中。第二次运行时，提供与第一次相同的文件名来反序列化电话簿的联系人，并从文件中加载它。

添加新联系人后，请注意第二次运行时电话簿中联系人的打印结果。从文件加载的联系人没有打印“新”标记，因为他们的`transient isNew`字段没有序列化。只有从磁盘加载电话簿的联系人后添加的联系人具有“新”标记。

确保在序列化电话簿后不要更改`PhoneBookEntry`的字段，否则当您试图反序列化对象时，将会遇到与`PhoneBookEntry`的`serialVersionUID`不兼容的问题。

## 结论

那是一个漫长的过程，但现在结束了——我很高兴你成功了！我们设法开发了一个功能性的电话簿应用程序，同时遵循单一责任原则。你甚至可以把它当作你自己的电话簿，以防你的智能手机突然退出。

您现在知道了如何序列化和反序列化对象，以及如何将它们存储在文件中，并在必要时将它们读回您的程序中。应用程序本身可以使用一些改进来验证用户提供的数据和更好的用户体验。试着调整它，让它变得更好。看看你能用它做些什么。

点击[此处](https://gist.github.com/SitePointEditors/7305a711a452fe1d93357a3b275f62bb)和[此处](https://gist.github.com/SitePointEditors/2be83329548bccf55c26d87ca79b791f)查看程序的可能输出。虽然菜单是在每个动作之前打印出来的，但为了简洁起见，我只在程序每次运行时显示一次。如果你有任何问题，请随时留下评论，我会尽力回答。

## 分享这篇文章