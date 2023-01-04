# 在 PHP 中实现范围运算符

> 原文：<https://www.sitepoint.com/implementing-the-range-operator-in-php/>

*我们有时会在[其他地方](https://github.com/tpunt/php-internals-articles)看到一些惊人的帖子，经作者许可，我们会在 SitePoint 上转发这些帖子。这就是一个这样的例子。在下面的帖子中， [Thomas Punt](https://www.sitepoint.com/author/tpunt/) 在 PHP 中实现了范围操作符。如果您曾经对 PHP 的内部特性和向您最喜欢的编程语言添加特性感兴趣，现在是学习的时候了！*

本文假设读者能够从源代码构建 PHP。如果不是这样，那么请先看看 [PHP 内部书籍](http://www.phpinternalsbook.com/)的[构建 PHP 章节](http://www.phpinternalsbook.com/build_system/building_php.html)。

![Flying elephpant](img/3e39cf4c9d5b0a06ebf6a8576b4ca3bd.png)

* * *

这篇文章将演示如何在 PHP 中实现一个新的操作符。为此，将采取以下步骤:

*   **更新词法分析器**:这将使它知道新的操作符语法，这样它就可以变成一个令牌
*   **更新解析器**:这将说明它可以用在哪里，以及它将具有什么样的优先级和结合性
*   **更新编译阶段**:这是遍历抽象语法树(AST)并从中发出操作码的地方
*   **更新 Zend VM** :这用于在脚本执行期间为操作者处理新操作码的解释

因此，本文试图对 PHP 的一些内部方面提供一个简要的概述。

此外，非常感谢尼基塔·波波夫校对并帮助改进我的文章！

## 范围运算符

本文中将要添加到 PHP 中的操作符将被称为范围操作符(`|>`)。为了简单起见，范围操作符将用以下语义来定义:

1.  增量步骤总是一个
2.  两个操作数都必须是整数或浮点数
3.  如果 min = max，则返回由 min 组成的一个元素数组。

(以上几点都将在最后一节**更新 Zend VM** 中引用，届时我们将最终实现上述语义。)

如果不满足上述任何语义，那么将抛出一个`Error`异常。因此，如果出现以下情况，就会发生这种情况:

*   操作数不是整数或浮点数
*   最小值>最大值
*   范围(最大–最小)太大

示例:

```
1 |> 3; // [1, 2, 3]
2.5 |> 5; // [2.5, 3.5, 4.5]

$a = $b = 1;
$a |> $b; // [1]

2 |> 1; // Error exception
1 |> '1'; // Error exception
new StdClass |> 1; // Error exception 
```

## 更新词法分析器

首先，新的令牌必须在 lexer 中注册，这样当源代码被令牌化时，它会把`|>`变成`T_RANGE`令牌。为此，[Zend/Zend _ language _ scanner . l](http://lxr.php.net/xref/PHP_7_0/Zend/zend_language_scanner.l)文件需要通过添加以下代码进行更新(在这里定义了所有其他的标记，第 1200 行):

```
<ST_IN_SCRIPTING>"|>" {
	RETURN_TOKEN(T_RANGE);
} 
```

`ST_IN_SCRIPTING`模式是 lexer 当前所处的状态。这意味着只有在正常脚本模式下，它才会匹配`|>`字符序列。花括号之间的代码是 C 代码，当在源代码中找到`|>`时将被执行。在这个例子中，它只是返回一个`T_RANGE`令牌。

注意:由于我们正在修改 lexer，我们将需要 Re2c 来重新生成它。PHP 的正常构建不需要这种
依赖。

接下来，`T_RANGE`标识符必须在[Zend/Zend _ language _ parser . y](http://lxr.php.net/xref/PHP_7_0/Zend/zend_language_parser.y)文件中声明。要做到这一点，我们必须在声明其他令牌标识符的地方添加下面一行(在末尾，第~220 行):

```
%token T_RANGE 		     "|> (T_RANGE)" 
```

PHP 现在可以识别新的操作符:

```
1 |> 2; // Parse error: syntax error, unexpected '|>' (T_RANGE) in... 
```

但是由于它的用法还没有定义，使用它会导致解析错误。这将在下一节中解决。

首先，我们必须在 tokenizer 扩展中重新生成**ext/tokenizer/tokenizer _ data . c**文件，以适应新添加的令牌。(tokenizer 扩展只是通过`token_get_all`和`token_name`函数为 PHP 的 lexer 提供一个到 userland 的接口。)目前，它对我们的新`T_RANGE`令牌一无所知:

```
echo token_name(token_get_all('<?php 1|>2;')[2][0]); // UNKNOWN 
```

我们通过进入 **ext/tokenizer** 目录并执行 **tokenizer_data_gen.sh** 文件来重新生成**ext/tokenizer/tokenizer _ data . c**文件。然后回到 php-src 根目录，再次构建 php。现在记号赋予器扩展再次工作了:

```
echo token_name(token_get_all('<?php 1|>2;')[2][0]); // T_RANGE 
```

## 更新解析器

解析器现在需要更新，这样它就可以验证新的`T_RANGE`标记在 PHP 脚本中的使用位置。它还负责声明新操作符的优先级和结合性，并为新构造生成抽象语法树(AST)节点。这些都将在[Zend/Zend _ language _ parser . y](http://lxr.php.net/xref/PHP_7_0/Zend/zend_language_parser.y)语法文件中完成，该文件包含 Bison 将用来生成解析器的令牌定义和产生式规则。

* * *

**题外话:**

*优先级*决定了分组表达式的规则。例如，在表达式`3 + 4 * 2`中，`*`的优先级高于`+`，因此它将被分组为`3 + (4 * 2)`。

*结合性*是操作符被链接时的行为。它决定了操作符是否可以被链接，如果可以，那么在一个特定的表达式中它将从哪个方向被分组。例如，三元运算符具有(相当奇怪的)左结合性，因此它将被分组并从左向右执行。因此，下面的表达式:

```
1 ? 0 : 1 ? 0 : 1; // 1 
```

将按如下方式执行:

```
(1 ? 0 : 1) ? 0 : 1; // 1 
```

当然，这可以被改变(阅读:修正)为与适当的分组右关联:

```
$a = 1 ? 0 : (1 ? 0 : 1); // 0 
```

然而，有些操作符是非关联的，因此根本不能链接。例如，小于(`>`)运算符就是这样，因此以下内容无效:

```
1 < $a < 2; 
```

* * *

由于范围操作符将计算一个数组，将它作为自身的输入操作数是没有用的(例如，`1 |> 3 |> 5`是没有意义的)。因此，让我们将操作符设为非关联操作符，同时，让我们将它设置为与组合比较操作符(`T_SPACESHIP`)具有相同的优先级。这是通过在下一行(第 70 行)的末尾添加`T_RANGE`标记来实现的:

```
%nonassoc T_IS_EQUAL T_IS_NOT_EQUAL T_IS_IDENTICAL T_IS_NOT_IDENTICAL T_SPACESHIP T_RANGE 
```

接下来，我们必须更新`expr_without_variable`生产规则，以适应我们的新操作员。这将通过在规则中添加以下代码来完成(我将它放在了`T_SPACESHIP`规则的下面，第 930 行):

```
 |	expr T_RANGE expr
			{ $$ = zend_ast_create(ZEND_AST_RANGE, $1, $3); } 
```

竖线字符(|)用于表示一个*或*，意味着这些规则中的任何一个都可以匹配那个特定的产生规则。当匹配发生时，花括号中的代码将被执行。`$$`表示存储表达式值的结果节点。`zend_ast_create`函数用于为我们的操作符创建 AST 节点。这个 AST 节点是以名称`ZEND_AST_RANGE`创建的，有两个值:`$1`引用左操作数(**expr**T _ RANGE expr)`$3`引用右操作数(expr T_RANGE **expr** )。

接下来，我们需要为 AST 定义`ZEND_AST_RANGE`常量。为此， [Zend/zend_ast.h](http://lxr.php.net/xref/PHP_7_0/Zend/zend_ast.h) 文件需要更新，只需在两个子节点列表下添加`ZEND_AST_RANGE`常量(我将它添加在`ZEND_AST_COALESCE`下):

```
 ZEND_AST_RANGE, 
```

现在执行我们的范围操作符只会导致解释器挂起:

```
1 |> 2; 
```

是时候更新编译阶段了。

## 更新编译阶段

我们现在需要更新编译阶段。解析器输出一个 AST，然后递归地遍历它，当 AST 中的每个节点被访问时，函数被触发执行。这些被触发的函数发出操作码，Zend VM 将在解释阶段执行这些操作码。

这个编译发生在 [Zend/zend_compile.c](http://lxr.php.net/xref/PHP_7_0/Zend/zend_compile.c) 中，所以让我们开始把我们新的 AST 节点名(`ZEND_AST_RANGE`)添加到`zend_compile_expr`函数中的大 switch 语句中(我已经把它添加到了`ZEND_AST_COALESCE`下面，第~7200 行):

```
 case ZEND_AST_RANGE:
			zend_compile_range(result, ast);
			return; 
```

现在我们必须在同一个文件的某个地方定义`zend_compile_range`函数:

```
void zend_compile_range(znode *result, zend_ast *ast) /* {{{ */
{
	zend_ast *left_ast = ast->child[0];
	zend_ast *right_ast = ast->child[1];
	znode left_node, right_node;

	zend_compile_expr(&left_node, left_ast);
	zend_compile_expr(&right_node, right_ast);

	zend_emit_op_tmp(result, ZEND_RANGE, &left_node, &right_node);
}
/* }}} */ 
```

我们首先将`ZEND_AST_RANGE`节点的左右操作数解引用到指针变量`left_ast`和`right_ast`中。然后我们定义两个`znode`变量，它们将保存对两个操作数的 AST 节点的编译结果(这是遍历 AST 并将其节点编译成操作码的递归部分)。

接下来，我们使用`zend_emit_op_tmp`函数发出带有两个操作数的`ZEND_RANGE`操作码。

现在可能是快速讨论操作码及其类型的好时机，以便更好地解释`zend_emit_op_tmp`函数的用法。

操作码是由 Zend VM 执行的指令。他们都有:

*   名称(映射到某个整数的常数)
*   op1 节点(可选)
*   op2 节点(可选)
*   结果节点(可选)。这通常用于存储操作码操作的临时值
*   一个扩展值(可选)。这是一个整数值，用于区分重载操作码的行为

* * *

**题外话:**

PHP 脚本的操作码可以通过以下方式查看:

*   phpdbg:t0 版
*   Opcache
*   [火神逻辑反汇编器(VLD)扩展](https://pecl.php.net/package/vld) : `sapi/cli/php -dvld.active=1 program.php`
*   或者，如果脚本简短，那么可以使用 [3v4l](https://3v4l.org)

* * *

操作码节点(`znode_op`结构)可以是许多不同的类型:

*   `IS_CV`–用于**C**completed**V**变量。这些是简单的变量(如`$a`)，它们被缓存在一个简单的数组中，以绕过哈希表查找。它们是在编译变量优化下引入 PHP 5.1 的。在 VLD，它们用`!n`来表示(其中 **n** 是一个整数)
*   `IS_VAR`–用于所有其他不简单的类似变量的表达式(如`$a->b`)。它们可以持有一个`IS_REFERENCE` zval，在 VLD 用`$n`表示(其中 **n** 是整数)
*   `IS_CONST`–用于文字值(如硬编码字符串)
*   `IS_TMP_VAR`–临时变量用于保存表达式的中间结果(使它们通常是短暂的)。它们也可以被引用计数(从 PHP 7 开始)，但是不能保存一个`IS_REFERENCE` zval(因为临时值不能用作引用)。它们在 VLD 用`~n`来表示(其中 **n** 是一个整数)
*   `IS_UNUSED`–通常用于将 op 节点标记为未使用。然而，有时数据会存储在`znode_op.num`成员中，以便在 VM 中使用。

这让我们回到上面的`zend_emit_op_tmp`函数，它将发出一个类型为`IS_TMP_VAR`的`zend_op`。我们希望这样做，因为我们的操作符将是一个表达式，所以它产生的值(一个数组)将是一个临时变量，可以用作另一个操作码的操作数(例如来自像`$var = 1 |> 3;`这样的代码的`ASSIGN`)。

## 更新 Zend 虚拟机

现在我们需要更新 Zend VM 来处理新操作码的执行。这将包括通过添加以下代码来更新 **Zend/zend_vm_def.h** 文件(在底部就可以了):

```
ZEND_VM_HANDLER(182, ZEND_RANGE, CONST|TMP|VAR|CV, CONST|TMP|VAR|CV)
{
	USE_OPLINE
	zend_free_op free_op1, free_op2;
	zval *op1, *op2, *result, tmp;

	SAVE_OPLINE();
	op1 = GET_OP1_ZVAL_PTR_DEREF(BP_VAR_R);
	op2 = GET_OP2_ZVAL_PTR_DEREF(BP_VAR_R);
	result = EX_VAR(opline->result.var);

	// if both operands are integers
	if (Z_TYPE_P(op1) == IS_LONG && Z_TYPE_P(op2) == IS_LONG) {
		// for when min and max are integers
	} else if ( // if both operands are either integers or doubles
		(Z_TYPE_P(op1) == IS_LONG || Z_TYPE_P(op1) == IS_DOUBLE)
		&& (Z_TYPE_P(op2) == IS_LONG || Z_TYPE_P(op2) == IS_DOUBLE)
	) {
		// for when min and max are either integers or floats
	} else {
		// for when min and max are neither integers nor floats
	}

	FREE_OP1();
	FREE_OP2();
	ZEND_VM_NEXT_OPCODE_CHECK_EXCEPTION();
} 
```

(操作码编号应该比前一个最高值多 1，所以 182 可能已经被您采用了。要快速查看当前最高的操作码号是多少，请查看 **Zend/zend_vm_opcodes.h** 文件的底部——`ZEND_VM_LAST_OPCODE`常量应该保存这个值。)

* * *

**题外话:**

上面的代码包含了一些伪宏(比如`USE_OPLINE`和`GET_OP1_ZVAL_PTR_DEREF`)。这些并不是真正的 C 宏——相反，它们在 vm 生成期间被 **Zend/zend_vm_gen.php** 脚本取代，而不是在源代码编译期间被预处理器取代。因此，如果你想查找它们的定义，你需要挖掘一下 **Zend/zend_vm_gen.php** 文件。

* * *

`ZEND_VM_HANDLER`伪宏包含每个操作码的定义。它可以有 5 个参数:

1.  操作码号(182)
2.  操作码名称(ZEND_RANGE)
3.  有效的左操作数类型(CONST|TMP|VAR|CV)(参见 **Zend/zend_vm_gen.php** 中的`$vm_op_decode`了解所有类型)
4.  有效的右操作数类型(CONST|TMP|VAR|CV)(同上)
5.  一个可选标志，保存重载操作码的扩展值(所有类型参见 **Zend/zend_vm_gen.php** 中的`$vm_ext_decode`)

从我们上面对类型的定义中，我们可以看出:

```
// CONST enables for
1 |> 5.0;

// TMP enables for
(2**2) |> (1 + 3);

// VAR enables for
$cmplx->var |> $var[1];

// CV enables for
$a |> $b; 
```

如果一个或两个操作数都没有使用，则用`ANY`标记。

请注意，`TMPVAR`被引入到 ZE3 中，它与`TMP|VAR`的相似之处在于它处理相同的操作码节点类型，但不同之处在于生成的代码不同。`TMPVAR`生成一个方法来处理`TMP`和`VAR`，这减少了 VM 的大小，但是需要更多的条件逻辑。相反，`TMP|VAR`为`TMP`和`VAR`生成方法，增加了 VM 的大小，但条件更少。

转到操作码定义的主体，我们首先调用`USE_OPLINE`伪宏来声明`opline`变量(一个`zend_op`结构)。这将用于获取操作数(使用类似`GET_OP1_ZVAL_PTR_DEREF`的伪宏)并设置操作码的返回值。

接下来，我们声明两个`zend_free_op`变量。这些只是指向 zval 的指针，它们是为我们使用的每个操作数声明的。当检查特定的操作数是否需要释放时，会用到它们。然后声明四个`zval`变量。`op1`和`op2`是指向保存操作数值的`zval`的指针。`result`被声明为存储操作码运算的结果。最后，`tmp`被用作范围循环操作的中间值，它将在每次迭代时被复制到哈希表中。

然后，`op1`和`op2`变量分别被`GET_OP1_ZVAL_PTR_DEREF`和`GET_OP2_ZVAL_PTR_DEREF`伪宏初始化。这些伪宏还负责[初始化`free_op1`和`free_op2`变量](http://lxr.php.net/xref/PHP_7_0/Zend/zend_vm_gen.php#142)。传递到上述宏中的`BP_VAR_R`常量是一个类型标志。代表*回插变量读取*，在[获取编译变量](http://lxr.php.net/xref/PHP_7_0/Zend/zend_execute.c#_get_zval_cv_lookup)时使用。最后，opline 的结果被解引用并分配给`result`，以备后用。

现在，当`min`和`max`都是整数时，让我们填写第一个`if`主体:

```
zend_long min = Z_LVAL_P(op1), max = Z_LVAL_P(op2);
zend_ulong size, i;

if (min > max) {
	zend_throw_error(NULL, "Min should be less than (or equal to) max");
	HANDLE_EXCEPTION();
}

// calculate size (one less than the total size for an inclusive range)
size = max - min;

// the size cannot be greater than or equal to HT_MAX_SIZE
// HT_MAX_SIZE - 1 takes into account the inclusive range size
if (size >= HT_MAX_SIZE - 1) {
	zend_throw_error(NULL, "Range size is too large");
	HANDLE_EXCEPTION();
}

// increment the size to take into account the inclusive range
++size;

// set the zval type to be a long
Z_TYPE_INFO(tmp) = IS_LONG;

// initialise the array to a given size
array_init_size(result, size);
zend_hash_real_init(Z_ARRVAL_P(result), 1);
ZEND_HASH_FILL_PACKED(Z_ARRVAL_P(result)) {
	for (i = 0; i < size; ++i) {
		Z_LVAL(tmp) = min + i;
		ZEND_HASH_FILL_ADD(&tmp);
	}
} ZEND_HASH_FILL_END();
ZEND_VM_NEXT_OPCODE_CHECK_EXCEPTION(); 
```

我们从定义`min`和`max`变量开始。这些被声明为`zend_long`，在声明长整数时必须使用(同样的还有`zend_ulong`用于定义无符号长整数)。然后将这个大小声明为`zend_ulong`，它将保存要生成的数组的大小。

然后检查`min`是否大于`max`，如果是，抛出`Error`异常。通过将`NULL`作为第一个参数传递给`zend_throw_error`，异常类默认为`Error`。我们可以通过对错误进行子类化来专门处理这个异常，并在 [Zend/zend_exceptions.c](http://lxr.php.net/xref/PHP_7_0/Zend/zend_exceptions.c) 中创建一个新的类条目，但这可能最好在后面的文章中讨论。如果抛出一个异常，那么我们调用`HANDLE_EXCEPTION`伪宏跳到下一个要执行的操作码。

接下来，我们计算要生成的数组的大小。这个大小比实际大小小一，因为它没有考虑包含范围。我们之所以不简单地在这个大小上加 1，是因为如果`min`等于`ZEND_LONG_MIN` ( `PHP_INT_MIN`)并且`max`等于`ZEND_LONG_MAX` ( `PHP_INT_MAX`)，就有可能发生溢出。

然后对照`HT_MAX_SIZE`常量检查大小，以确保数组适合哈希表。总数组大小不得大于或等于`HT_MAX_SIZE`——如果是，那么我们再次抛出一个错误异常并退出 VM。

因为`HT_MAX_SIZE`等于`INT_MAX + 1`，我们知道如果`size`小于这个，我们可以安全地增加大小，而不用担心溢出。这就是我们下一步要做的，这样我们的`size`现在就能容纳一个包容的范围。

然后我们将`tmp` zval 的类型改为`IS_LONG`，然后使用`array_init_size`宏初始化`result`。这个宏基本上将`result`的类型设置为`IS_ARRAY_EX`，为`zend_array`结构(一个哈希表)分配内存，并设置其对应的哈希表。然后，`zend_hash_real_init`函数为保存数组中每个元素的`Bucket`结构分配内存。第二个参数`1`指定我们希望它是一个打包的哈希表。

* * *

**题外话:**

打包的哈希表实际上是一个*实际的*数组，即通过整数键进行数字访问的数组(不像 PHP 中典型的关联数组)。PHP 7 中引入了这种优化，因为人们认识到 PHP 中的许多数组都是整数索引的(键以升序排列)。打包的散列表允许散列表桶被直接访问(像普通数组一样)。参见 Nikita 的 [PHP 的新 hashtable 实现](http://nikic.github.io/2014/12/22/PHPs-new-hashtable-implementation.html)文章了解更多信息。

* * *

*注意:`_zend_array`结构有两个别名:`zend_array`和`HashTable`。*

接下来，我们填充数组。这是通过`ZEND_HASH_FILL_PACKED`宏([定义](http://lxr.php.net/xref/PHP_7_0/Zend/zend_hash.h#873))完成的，它基本上跟踪当前要插入的桶。在生成数组时，`tmp` zval 存储中间结果(数组元素)。`ZEND_HASH_FILL_ADD`宏复制了`tmp`，将这个副本插入到当前的散列表桶中，并为下一次迭代增加到下一个桶。

最后，`ZEND_VM_NEXT_OPCODE_CHECK_EXCEPTION`宏(在 ZE3 中引入来代替 ZE2 中单独的`CHECK_EXCEPTION()`和`ZEND_VM_NEXT_OPCODE()`调用)检查是否发生了异常。如果没有发生异常，那么 VM 跳到下一个操作码。

现在让我们来看看`else if`模块:

```
long double min, max, size, i;

if (Z_TYPE_P(op1) == IS_LONG) {
	min = (long double) Z_LVAL_P(op1);
	max = (long double) Z_DVAL_P(op2);
} else if (Z_TYPE_P(op2) == IS_LONG) {
	min = (long double) Z_DVAL_P(op1);
	max = (long double) Z_LVAL_P(op2);
} else {
	min = (long double) Z_DVAL_P(op1);
	max = (long double) Z_DVAL_P(op2);
}

if (min > max) {
	zend_throw_error(NULL, "Min should be less than (or equal to) max");
	HANDLE_EXCEPTION();
}

size = max - min;

if (size >= HT_MAX_SIZE - 1) {
	zend_throw_error(NULL, "Range size is too large");
	HANDLE_EXCEPTION();
}

// we cast the size to an integer to get rid of the decimal places,
// since we only care about whole number sizes
size = (int) size + 1;

Z_TYPE_INFO(tmp) = IS_DOUBLE;

array_init_size(result, size);
zend_hash_real_init(Z_ARRVAL_P(result), 1);
ZEND_HASH_FILL_PACKED(Z_ARRVAL_P(result)) {
	for (i = 0; i < size; ++i) {
		Z_DVAL(tmp) = min + i;
		ZEND_HASH_FILL_ADD(&tmp);
	}
} ZEND_HASH_FILL_END();
ZEND_VM_NEXT_OPCODE_CHECK_EXCEPTION(); 
```

*注意:我们使用`long double`来处理混合使用浮点数和整数作为操作数的情况。这是因为`double`只有 53 位的精度，所以任何大于 2^53 的整数都不能精确地表示为`double`。另一方面，`long double`具有至少 64 位的精度，因此它可以精确地表示 64 位整数。*

这段代码与前面的逻辑非常相似。现在的主要区别是我们将数据作为浮点数来处理。这包括用`Z_DVAL_P`宏获取它们，将`tmp`的类型信息设置为`IS_DOUBLE`，并用`Z_DVAL`宏插入 zval(double 类型)。

最后，我们必须处理`min`和`max`中的一个(或两个)不是整数或浮点数的情况。正如我们的范围操作符语义的第 2 点所定义的，只有整数和浮点数被支持作为操作数——如果提供了其他任何东西，将会抛出一个错误异常。将以下代码粘贴到`else`块中:

```
zend_throw_error(NULL, "Unsupported operand types - only ints and floats are supported");
HANDLE_EXCEPTION(); 
```

操作码定义完成后，我们现在必须重新生成 VM。这是通过运行 **Zend/zend_vm_gen.php** 文件来完成的，该文件将使用 **Zend/zend_vm_def.h** 文件来重新生成 **Zend/zend_vm_opcodes.h** 、 **Zend/zend_vm_opcodes.c** 和 **Zend/zend_vm_execute.h** 文件。

现在再次构建 PHP，这样我们就可以看到 range 操作符的作用了:

```
var_dump(1 |> 1.5);

var_dump(PHP_INT_MIN |> PHP_INT_MIN + 1); 
```

产出:

```
array(1) {
  [0]=>
  float(1)
}

array(2) {
  [0]=>
  int(-9223372036854775808)
  [1]=>
  int(-9223372036854775807)
} 
```

现在我们的操作员终于工作了！不过，我们还没有完成。我们仍然需要更新 AST pretty 打印机(将 AST 转换回代码)。它目前不支持我们的 range 运算符——在`assert()`中使用它就可以看出这一点:

```
assert(1 |> 2); // segfaults 
```

*注意，`assert()`使用 pretty 打印机输出被断言的表达式，作为失败时错误消息的一部分。只有当断言的表达式不是字符串形式时才这样做(因为否则就不需要漂亮的打印机了)，这是 PHP 7 的新特性。*

为了纠正这一点，我们只需要更新[Zend/Zend _ ast . c]([http://lxr.php.net/xref/PHP_7_0/Zend/zend_ast.c](http://lxr.php.net/xref/PHP_7_0/Zend/zend_ast.c))文件，将我们的`ZEND_AST_RANGE`节点转换成一个字符串。我们将首先更新优先级表注释(第 520 行),指定我们的新操作符的优先级为 170(这应该与 zend_language_parser.y 文件相匹配):

```
*  170     non-associative == != === !== |> 
```

接下来，我们需要在`zend_ast_export_ex`函数中插入一个`case`语句来处理`ZEND_AST_RANGE`(就在`case ZEND_AST_GREATER`之上):

```
case ZEND_AST_RANGE:                   BINARY_OP(" |> ",  170, 171, 171);
case ZEND_AST_GREATER:                 BINARY_OP(" > ",   180, 181, 181);
case ZEND_AST_GREATER_EQUAL:           BINARY_OP(" >= ",  180, 181, 181); 
```

漂亮的打印机现已更新，`assert()`再次正常工作:

```
assert(false && 1 |> 2); // Warning: assert(): assert(false && 1 |> 2) failed... 
```

## 结论

这篇文章覆盖了很多领域，尽管很薄。它展示了 ZE 在运行 PHP 脚本时所经历的阶段，这些阶段是如何相互操作的，以及我们如何修改其中的每一个阶段以在 PHP 中包含一个新的操作符。本文仅展示了 PHP 中 range 操作符的一种可能的实现——我将在后续文章中介绍另一种(更好的)实现。

## 分享这篇文章