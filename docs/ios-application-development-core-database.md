# iPhone 应用程序开发–核心数据库

> 原文：<https://www.sitepoint.com/ios-application-development-core-database/>

对于任何移动应用程序来说，将数据保存在设备中是一个非常普遍的需求。这将使用户能够在离线时访问应用程序，并且有助于更快地访问数据。

我们可以通过许多方式将数据保存在设备中。最好的方法之一是使用核心数据库将数据存储在设备中。

选择“使用核心数据”选项，创建新的应用程序“DBExampleApp”。

我们将使用上一篇教程中相同的“文章”示例。我们将修改该示例，我们将在数据库中保存文章，并从数据库中检索文章，并将其显示在列表中。

在本教程的末尾，您会找到下载完整源代码的链接。

## 我们还在等什么？

除了 Appdelegate 文件之外，将前面示例中的所有文件添加到新项目中。请记住删除“appDidLaunch”方法中除 return 语句之外的所有行。还要在“DBSampleApp-info.plist”文件中添加“主要情节提要文件基本名称”属性。

初始设置完成。运行应用程序以确保一切都完美运行。

我们将在 appdelegate 文件中找到额外的变量和方法。如果我们创建一个启用了“使用核心数据”选项的应用程序，这些额外的代码会自动生成。

数据模型文件“DBSampleApp.xcdatamodeld”也是自动生成的。

为了理解 appdelegate 文件中的方法和变量，必须了解某些核心数据术语。

**持久对象存储**:指对象存储的方式。持久对象存储可以是 sqlite 数据库、XML、文件等。核心数据库中的底层持久对象存储是一个 sqlite 数据库。

**管理对象模型**:表示数据库模式。它包含数据库中的实体或表、其属性或字段以及它们之间的关系。数据模型文件“DBSampleApp.xcdatamodeld”表示托管对象模型。

**实体描述**:在我们的例子中，‘Article’是一个实体。文章的名称和描述表示实体描述。

**被管理对象**:实体的实例代表被管理对象。

**管理对象上下文**:允许我们修改和管理数据库中的对象。它被称为暂存区，因为它维护数据库的撤消/重做历史。数据库中对象的插入、检索和删除等操作都是在托管对象上下文的帮助下完成的。

**持久性存储协调器**:它在持久性存储和应用程序的管理对象之间进行协调。

理解了数据库概念之后，现在试着理解 appdelegate 文件中的方法和变量。

选择“DBSampleApp.xcdatamodeld”文件并创建新的“Article”实体。添加标题和描述字段，如下所示。

![create a new Article entity](img/6d5c05ee1a72d9f0c09482e2abb9c1dd.png)

![Select the attribute](img/8b98ac7949a7515a98f8be37ddcc4c93.png)

选择属性并在属性检查器中检查其属性。

**瞬态–**属性不会持久存储在持久性存储中。它们的值基于其他字段值，通常在运行时计算。
**可选—**指定是否强制。
**索引-**表示主键属性

让我们也创建一个“用户”实体来理解核心数据库中的关系概念。为“用户”实体添加属性“用户 Id”和“用户名”。

一个用户可以写很多文章，所以在用户和文章实体之间创建一个一对多的关系。

将数据模型窗口底部的编辑器样式更改为“Graph”。按住 CTRL 键并从“用户”实体拖动到“文章”实体。如下所示设置关系的属性。

![Set the attributes of the relationship](img/2f47604036db6dd3efcc66bf11759d27.png)

## 删除规则

让我们从用户实体的角度来看。

**级联:**删除用户对象会删除与其关联的所有文章。
**拒绝:**用户对象不能被删除，除非且直到整个文章对象被删除。
**无效:**如果用户对象被删除，反向关系“作者”将被设置为空，前提是“作者”是可选关系。
**无操作:**删除用户对象时，在“文章”实体上不需要任何操作。

选择实体，然后选择编辑器->创建 NSManagedObject 子类。

在随后的对话框中选择实体时，将生成文章和用户管理的对象类。

## 将对象添加到数据库

```
NSManagedObjectContext *context = [appDelegate managedObjectContext];
    Article *articleDetails = [NSEntityDescription
                               insertNewObjectForEntityForName:@"Article"
                               inManagedObjectContext:context];
    [articleDetails setValue:strName forKey:@"articleTitle"];
    [articleDetails setValue:strArticleDesc forKey:@"articleDesc"];
[appDelegate saveContext];
```

创建一个“extern”app delegate 变量以访问“SPTAppDelegate”类中的变量和方法。

任何数据库操作都只能在托管对象上下文的帮助下完成。

我们已经创建了 Article 实体的实例，并为实体中的属性设置了变量“strName”和“StrArticleDesc”的值。

在 Appdelegate 中的 save context 方法的帮助下，这些值被写入数据库。

让我们创建一个用户对象，并在新创建的文章对象和用户对象之间建立一个关系。

```
User *userObj = [NSEntityDescription
                               insertNewObjectForEntityForName:@"User"
                               inManagedObjectContext:context];
    [userObj setValue:[NSNumber numberWithInt:123] forKey:@"userId"];
    [userObj setValue:@"Kanya" forKey:@"username"];

    articleDetails.author = userObj;
    [appDelegate saveContext]
```

## 从数据库中获取对象

```
NSError *error;

    NSFetchRequest *fetchRequest = [[NSFetchRequest alloc] init];
    NSEntityDescription *entity = [NSEntityDescription
                                   entityForName:@"Article" inManagedObjectContext:[appDelegate managedObjectContext]];
    [fetchRequest setEntity:entity];
    NSArray *fetchedObjects = [appDelegate.managedObjectContext executeFetchRequest:fetchRequest error:&error];
    for (Article * articleObjin fetchedObjects) {
        NSLog(@"Title: %@", [articleObj valueForKey:@"articleTitle"]);
    }
```

NSFetchRequest 用于从数据库中获取对象。提到了应该在其上执行提取操作的实体。当执行提取请求时，返回实体对象的数组。

执行 fetch 条件时应该应用的条件也可以使用下面给出的 NSPredicate 类来说明。

```
NSPredicate *predicate = [NSPredicate predicateWithFormat:@"author.userId == %@",[NSNumber numberWithInt:111]];
    [fetchRequest setPredicate:predicate];
```

如果在执行获取请求之前添加了上面的行，将返回与用户对应的文章。

我们还可以使用 NSSortDescriptor 类根据某些属性对获取的对象进行排序，如下所示

```
NSSortDescriptor *sortDesc = [NSSortDescriptor sortDescriptorWithKey:@"articleTitle" ascending:YES];
    [fetchRequest setSortDescriptors:[NSArray arrayWithObject:sortDesc]];
```

返回的对象将根据文章标题按升序排序。

## 更新数据库中的对象

```
NSError *error;
    NSFetchRequest *fetchRequest = [[NSFetchRequest alloc] init];
    NSEntityDescription *entity = [NSEntityDescription
                                   entityForName:@"Article" inManagedObjectContext:[appDelegate managedObjectContext]];
    [fetchRequest setEntity:entity];
    NSPredicate *predicate = [NSPredicate predicateWithFormat:@"articleTitle == %@",@"Sample Article"];
    [fetchRequest setPredicate:predicate];
    NSArray *fetchedObjects = [appDelegate.managedObjectContext executeFetchRequest:fetchRequest error:&error];
    if([fetchedObjects count] > 0)
    {
        Article *articleObj = [fetchedObjects objectAtIndex:0];
        articleObj.articleDesc = @"Sample Article Desc";
    }
    [appDelegate saveContext];
```

更新数据库中的对象类似于在数据库中添加对象。我们需要在谓词的帮助下获取想要更新的对象，然后更新该对象所需的属性。

## 从数据库中删除对象

```
NSError *error;

    NSFetchRequest *fetchRequest = [[NSFetchRequest alloc] init];
    NSEntityDescription *entity = [NSEntityDescription
                                   entityForName:@"Article" inManagedObjectContext:[appDelegate managedObjectContext]];
    [fetchRequest setEntity:entity];
    NSPredicate *predicate = [NSPredicate predicateWithFormat:@"author.userId == %@",[NSNumber numberWithInt:123]];
    [fetchRequest setPredicate:predicate];
    NSArray *fetchedObjects = [appDelegate.managedObjectContext executeFetchRequest:fetchRequest error:&error];
    for (Article *articleObj in fetchedObjects) {
        [appDelegate.managedObjectContext deleteObject:articleObj];

    }

    [appDelegate saveContext];
```

我们已经获取了对象，并使用托管对象上下文的“deleteObject”方法删除了它。

当您想要将更改提交到数据库时，请记住调用“saveContext”方法。

## 结论

在本教程结束时，您应该能够执行所有基本的核心数据库操作。

尝试使用上面学到的操作，并让前面的示例与核心数据库集成一起工作。

[下载完整源代码](https://uploads.sitepoint.com/wp-content/uploads/2013/11/DBSampleApp.zip)并查看核心数据库是如何集成的。

## 分享这篇文章