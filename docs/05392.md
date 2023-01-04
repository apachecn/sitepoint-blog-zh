# 用 Phake 自动化 PHP 真实世界的例子

> 原文：<https://www.sitepoint.com/automate-php-phake-real-world-examples/>

在第一部分中，我们介绍了 Phake 的基础知识，并演示了用它执行任务的方法，包括组、依赖和参数。在这一部分中，我们将看看 Phake 的一些真实应用程序示例。注意，下面的例子很大程度上是基于我通常手工做的需要某种自动化的事情。

### 使用打包任务将文件上传到服务器

让我们从编写一个将文件上传到 FTP 服务器的任务开始。

```
task('up', function($args){

  $host = $args['host'];
  $user = $args['user'];
  $pass = $args['pass'];
  $port = 21; //default ftp port
  $timeout = 60; //timeout for uploading individual files

  //connect to server
  $ftp = ftp_connect($host, $port, $timeout);
  ftp_login($ftp, $user, $pass); //login to server

  $root_local_dir = $args['local_path'];
  $root_ftp_dir = $args['remote_path'];
  $dir_to_upload = $args['local_path'];

  $iterator = new RecursiveIteratorIterator(new RecursiveDirectoryIterator($dir_to_upload), RecursiveIteratorIterator::SELF_FIRST);

  //iterate through all the files and folders in the specified directory
  foreach($iterator as $path){
    //if the current path refers to a file
    if($path->isFile()){	

      $current_file = $path->__toString();       	
      $dest_file = str_replace($root_local_dir, $root_ftp_dir, $current_file);   	
      $result = ftp_nb_put($ftp, $dest_file, $current_file, FTP_BINARY);

      //while the end of the file is not yet reached keep uploading
      while($result == FTP_MOREDATA){
        $result = ftp_nb_continue($ftp);
      }

      //once the whole file has finished uploading
      if($result == FTP_FINISHED){
        echo "uploaded: " . $current_file . "\n";
      }

     }else{
       //if the current path refers to a directory
       $current_dir = $path->__toString();

       //if the name of the directory doesn't begin with dot
       if(substr($current_dir, -1) != '.'){ 
         //remove the path to the directory from the current path
         $current_dir = str_replace($root_local_dir, '', $current_dir);
         //remove the beginning slash from current path
         $current_dir = substr($current_dir, 1);

         //create the directory in the server
         ftp_mksubdirs($ftp, $root_ftp_dir, $current_dir);
         echo "created dir: " . $current_dir . "\n";
       }
     } 
   }
});
```

从上面的代码中可以看出，该任务接受五个参数:

*   **主机**–您要上传文件的服务器的域名或 ip 地址
*   **用户**
*   **通过**
*   **local _ path**–您想要上传的路径
*   **远程路径**–服务器中的路径

然后我们将这些参数传递给`ftp_connect`方法。`ftp_connect`方法返回一个 ftp 流，然后我们将它作为第一个参数与用户名和密码一起提供给`ftp_login`方法。

之后，我们可以使用[递归迭代器迭代器](http://www.php.net/manual/en/class.recursiveiteratoriterator.php)和[递归目录迭代器](http://www.php.net/manual/en/class.recursivedirectoryiterator.php)遍历本地路径中的所有文件。递归迭代器迭代器接受一个递归目录迭代器对象作为它的第一个参数，第二个参数是来自`RecursiveIteratorIterator`类的常量。在我们的任务中，我们提供了`RecursiveIteratorIterator::SELF_FIRST`作为第二个参数。这指示迭代器在每次迭代中包含完整路径。然后我们使用一个`foreach`循环遍历该路径中的所有文件和目录。

我们可以使用`isFile`方法检查当前路径是否是一个文件。如果它是一个文件，我们只需使用`__toString`魔法将它转换成一个字符串，一旦转换完毕，我们就将它赋给`$current_file`变量。接下来，我们声明另一个变量(`$dest_file`)，它将保存当前文件的路径，但不包含根目录的路径。这将用作将在服务器上创建的文件的路径名。一旦这些变量被赋值，我们就可以将它们作为`ftp_nb_put`方法的参数。它将前面由`ftp_connect`方法返回的 ftp 流作为第一个参数。第二个参数是文件在服务器上的路径，第三个参数是文件在计算机上的路径。第四个论点是转移的方式；在这种情况下，我们使用了`FTP_BINARY`。然后，我们可以使用`ftp_nb_continue`方法上传文件，该方法将 ftp 流作为其参数。只要这个方法返回了`FTP_MOREDATA`常量，就意味着还没有到达文件的末尾，所以我们使用一个`while`循环来检查它。一旦完成，它就返回`FTP_FINISHED`。

如果当前路径指向一个目录，我们也将其转换为一个字符串，然后检查它是否以`.`开头，因为您可能已经知道我们需要排除以`.`开头的目录。一个流行的例子是 git 用来存储历史的`.git`目录。我们需要排除这些目录，因为应用程序或网站通常不需要它们来运行并包含元数据或版本信息。

如果当前目录不是隐藏目录，我们将从其中删除根路径，因为我们不需要在服务器中创建该路径。这就是为什么我们使用`str_replace`用一个空字符串替换我们不需要的部分。

一旦完成，我们就可以调用`ftp_mksubdirs`方法在服务器中创建路径。

`ftp_mksubdirs`方法获取 ftp 流、服务器中的根目录和当前目录的路径。注意，这不是 PHP 内置的方法；我们首先需要声明它，然后才能使用它。函数如下:

```
function ftp_mksubdirs($ftpcon, $ftpbasedir, $ftpath){
   @ftp_chdir($ftpcon, $ftpbasedir);
   $parts = explode('/',$ftpath); 
   foreach($parts as $part){
      if(!@ftp_chdir($ftpcon, $part)){
	ftp_mkdir($ftpcon, $part);
	ftp_chdir($ftpcon, $part);
      }
   }
}
```

这个函数的作用是创建目录，如果它们还不存在的话。所以如果你通过下面的路径:

```
app/views/users
```

……而`app`、`views`和`users`目录在服务器上还不存在，这是它创建的所有目录。

您可以通过在终端中执行如下命令来运行上面的任务:

```
phake up host=someserver.com user=me pass=secret local_path=/home/wern/www/someproject remote_path=/public_html
```

请注意，这项任务主要适用于我自己的个人用例——您可能正在以另一种方式进行部署，或者您可能根本不需要`str_replace`部分。重要的是，这是一个我实际使用的实时任务，它让我的生活变得更加轻松。

### 为数据库设定种子

使用 Phake，我们还可以编写一个任务来为我们播种数据库。为此，我们需要使用一个名为 Faker 的库。要使用 Faker，请在项目的`composer.json`文件中包含以下内容:

```
{
  "require": {
    "fzaninotto/faker": "1.5.*@dev" 
  }
}
```

接下来创建一个任务，并将其命名为`seed_users`。它需要一个名为`rows`的参数。这允许您设置要插入到表格中的行数。

```
task('seed_users', function($args){
    $rows = 1000;
    if(!empty($args['rows']){
        $count = $args['rows'];
    }
});
```

接下来，我们使用 [Mysqli](http://www.php.net/manual/en/intro.mysqli.php) 连接到数据库:

```
$host = 'localhost';
$user = 'user';
$pass = 'secret';
$database = 'test';

$db = new Mysqli($host, $user, $pass, $database);
```

然后，我们初始化一个新的 Faker 生成器，并将其赋给`$faker`变量。通过一个`for`循环，我们重复执行生成用户数据并将其保存到数据库中的代码。我们将它设置为执行，直到它达到我们作为参数的行数。默认情况下，它将执行 100 次，生成 100 个随机用户，但都使用相同的密码，所以我们可以在需要时轻松登录。

```
$faker = Faker\Factory::create();

for($x = 0; $x <= $rows; $x++){

    $email = $faker->email;
    $password = password_hash('secret', PASSWORD_DEFAULT);
    $first_name = $faker->firstName;
    $last_name = $faker->lastName;
    $address = $faker->address;

    $db->query("INSERT INTO users SET email = '$email', password = '$password'");

    $db->query("INSERT INTO user_info SET first_name = '$first_name', last_name = '$last_name', address = '$address'");
}

echo "Database was seeded!\n";
```

将所有东西放在一起，我们最终完成以下任务:

```
task('seed_users', function($args){

  $rows = 1000;
  if(!empty($args['rows']){
    $rows = $args['rows'];
  }

  $host = 'localhost';
  $user = 'user';
  $pass = 'secret';
  $database = 'test';

  $db = new Mysqli($host, $user, $pass, $database); 
  $faker = Faker\Factory::create();

  for($x = 0; $x <= $rows; $x++){

    $email = $faker->email;
    $password = password_hash('secret', PASSWORD_DEFAULT);
    $first_name = $faker->firstName;
    $last_name = $faker->lastName;
    $address = $faker->address;

    $db->query("INSERT INTO users SET email = '$email', password = '$password'");

    $db->query("INSERT INTO user_info SET first_name = '$first_name', last_name = '$last_name', address = '$address'");
  }

  echo "Database was seeded!\n";
});
```

完成后，您可以通过执行以下命令来调用该任务:

```
phake seed_users rows=1000
```

### 同步数据

我经常发现自己重复做的另一项任务是将数据库更改从我的本地机器同步到服务器。有了 Phake，这可以通过使用 MySQL 自带的数据库备份程序`mysqldump`轻松实现自动化。在这个任务中，您所要做的就是使用 PHP 中的`exec`方法执行 mysqldump 命令。mysqldump 程序接受数据库的名称、用户和密码作为它的参数。然后，您可以通过管道将它发送到`ssh`命令。一旦`ssh`被执行，它会要求你输入密码，一旦你输入了密码，它就会执行`mysql $remote_db`命令。这样做的目的是获取之前由`mysqldump`命令转储的查询，并在服务器上执行它。请注意，这将覆盖服务器上当前的内容，因此只有当您仍处于应用程序的开发阶段时，才建议您这样做。

```
task('sync_db', function($args){
  $db = $args['db']; //name of the local database
  $user = $args['db_user']; //database user
  $pass = $args['db_pass']; //the user's password
  $host = $args['host']; //domain name of the server you want to connect to
  $ssh_user = $args['ssh_user']; //the user used for logging in
  $remote_db = $args['remote_db']; //the name of the database in the server
  exec("mysqldump $db -u $user -p$pass | ssh $ssh_user@$host mysql $remote_db");
});
```

您可以使用以下命令调用上面的任务。只需将凭据替换为您自己的凭据:

```
phake sync_db db=my_db db_user=me db_pass=secret ssh_user=me host=somewebsite.com remote_db=my_db
```

请注意，如果这不起作用，您可能需要在位于`/etc/ssh/sshd_config`的`sshd_config`文件中设置以下值:

```
X11Forwarding yes
X11UseLocalhost no
```

完成后，重新加载 sshd 以使更改生效:

```
sudo /etc/init.d/sshd reload
```

### 结论

Phake 是一种很好的方式，通过让计算机为您工作来自动化日常任务并节省时间。有了 Phake，你几乎可以自动化你能想到的任何事情。它为您提供了任务自动化的所有必要工具。如果你发现自己在做一些重复的事情，想想如何用 Phake 实现自动化。

有什么有趣的任务和使用案例想和我们分享吗？请在评论中告诉我们。

## 分享这篇文章