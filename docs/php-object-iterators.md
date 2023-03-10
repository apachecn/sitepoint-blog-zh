# PHP 中复杂的对象迭代器

> 原文：<https://www.sitepoint.com/php-object-iterators/>

在我之前的文章《PHP 中的简单对象迭代器》中，我们发现了如何使用一个 [foreach](http://php.net/manual/en/control-structures.foreach.php) 循环来迭代一个对象中定义的数组项。但是，如果您需要迭代不存储在数组中的项目，例如数据库中的记录或从文件中读取的文本行，该怎么办呢？

对于下面的例子，我们将创建一个类:

1.  通过从数据库中检索记录来统计登录到 web 应用程序的用户数
2.  创建可以用 foreach 循环迭代的“user”对象集合。

最终，我们希望能够运行简单的代码，例如:

```
 $loggedin = new LoggedIn();

// count users
echo '<p>', count($loggedin), ' user(s) are currently logged in:</p>';

// iterate over users
foreach ($loggedin as $rec => $user) {
	echo
		'Record ', $rec,
		':   ID=', $user->id, 
		' Email=', $user->email,
		'<br/>';
} 
```

**note:** Recordsets are traversable

PDO 记录集已经是可遍历的了，为什么还要将该功能包装在一个类中呢？

好处是封装和重用。使用 LoggedIn 类的开发人员不需要担心实现细节，比如数据库连接或表结构。如果用户或登录详细信息被移动到另一个系统，您可以更改 LoggedIn 类，而不会影响其他代码。

此外，类的功能可以在不影响使用它的代码的情况下进行扩展，例如获取更多的用户详细信息、写入日志文件、发出安全警报等。

首先，我们将定义一个存储单个用户的 ID 和电子邮件地址的基类。当我们迭代登录的用户时，将返回此类的一个实例:

```
 class User
{
	public $id, $email;
} 
```

我们现在需要一个 LoggedIn 类来处理已经登录的用户的集合。请注意，该类将实现 Countable 和 Iterator 接口:

```
 class LoggedIn implements Countable, Iterator
{

	private $rec;		// database recordset
	private $cursor;		// number of current record
	private $item;		// current user in the collection 
```

构造函数将立即调用运行数据库查询的私有 FetchLoggedIn()方法:

```
 // constructor
	public function __construct() {
		$this->FetchLoggedIn();
	}

	// fetch logged in user records
	private function FetchLoggedIn() {

		$this->cursor = -1;
		$this->item = null;

		// find logged-in users
		$db = new PDO('mysql:host=localhost;dbname=dbname', 'dbuser', 'dbpass');
		$this->rec = $db->Prepare(
			'SELECT id, email FROM `user` WHERE loggedin=1;', 
			array(PDO::ATTR_CURSOR => PDO::CURSOR_FWDONLY)
		);

		// convert returned records to a User object
		$this->rec->setFetchMode(PDO::FETCH_INTO, new User());

		// run query
		$this->rec->execute();

	} 
```

注意:

*   `$this->cursor`是当前记录号(从零开始)。它最初被设置为-1，因为我们没有检索到任何记录。
*   `$this->item`将保存当前用户对象。
*   `$this->rec`是我们的 SQL 查询返回的记录集(如果有的话)。
*   `$this->rec->setFetchMode`行设置默认的 PDO 获取模式。当我们获取一行时，返回 User 类的一个新实例，其中$id 和$email 属性映射到记录。

因为我们的类实现了 Countable，所以我们必须创建一个名为`count()`的公共方法，该方法返回集合中的项数——在本例中，是我们的 SQL 查询返回的行数:

```
 public function count() {
		return $this->rec->rowCount();
	} 
```

我们现在需要 5 个实现迭代器功能的公共方法。第一个名字令人困惑`rewind()`。当 foreach 循环开始时调用它，并且应该“倒回”到集合中的第一项:

```
 public function rewind() {
		if ($this->cursor >= 0) $this->FetchLoggedIn();
		$this->next();
	} 
```

该代码检查是否已经返回了一个或多个记录。如果有，我们再次运行 SQL 查询，因为我们已经创建了一个只进记录集。

下面一行调用了`next()`——我们的第二个迭代器方法:

```
 public function next() {
		$this->cursor++;
		$this->item = $this->rec->fetch(PDO::FETCH_ORI_NEXT);
	} 
```

在 foreach 循环的每次迭代期间都会调用此方法。它递增`$this->cursor`，从记录集中获取下一行，并创建一个新的用户对象，该对象被分配给`$this->item`。

接下来的两个迭代器方法被命名为`current()`和`key()`，它们分别返回当前集合项(一个用户对象)和记录号:

```
 public function current() {
		return $this->item;
	}

	public function key() {
		return $this->cursor;
	} 
```

最后，我们需要一个公共的`valid()`方法。这在 foreach 循环执行完`rewind()`或`next()`后立即被调用—如果某项可用，它必须返回 true:

```
 public function valid() {
		return ($this->cursor < $this->count());
    }
// end of class definition
} 
```

我们的类是完整的，我们可以使用本文顶部的代码对登录用户进行计数或迭代。

我希望您发现本系列很有用，并在下次创建包含项目集合的对象时考虑迭代器。

## 分享这篇文章