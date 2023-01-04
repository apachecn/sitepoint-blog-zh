# 在 PHP 中重新实现范围运算符

> 原文：<https://www.sitepoint.com/re-implementing-the-range-operator-in-php/>

*我们有时会在[其他地方](https://github.com/tpunt/php-internals-articles)看到一些惊人的帖子，经作者许可，我们会在 SitePoint 上转发这些帖子。这就是一个这样的例子。在下面的帖子中， [Thomas Punt](https://www.sitepoint.com/author/tpunt/) 重新实现了之前在 PHP 中实现的[range 运算符，这次使用了一种改进的方法。如果您曾经对 PHP 的内部特性和向您最喜欢的编程语言添加特性感兴趣，现在是学习的时候了！](https://www.sitepoint.com/implementing-the-range-operator-in-php)*

本文假设读者能够从源代码构建 PHP。如果不是这样，那么请先看看 [PHP 内部书籍](http://www.phpinternalsbook.com/)的[构建 PHP 章节](http://www.phpinternalsbook.com/build_system/building_php.html)。

![Flying elephpant](img/3e39cf4c9d5b0a06ebf6a8576b4ca3bd.png)

* * *

在这篇文章的前传中，我展示了一种用 PHP 实现范围操作符的方法。然而，最初的实现很少是最好的，因此本文的目的是研究如何改进以前的实现。

再次感谢[尼基塔·波波夫](https://github.com/nikic/)校对这篇文章！

## 以前的实施缺陷

最初的实现将 range 操作符的所有逻辑都放在 Zend VM 中，当执行`ZEND_RANGE`操作码时，这迫使计算纯粹在运行时发生。这不仅意味着计算不能转移到字面量的操作数的编译时间，还意味着一些特性根本不起作用。

在这个实现中，我们将把范围操作符逻辑从 Zend VM 中移出，使计算能够在编译时(对于文字操作数)或运行时(对于动态操作数)完成。这不仅为 Opcache 用户提供了一个小小的优势，更重要的是允许常量表达式特性与范围操作符一起使用。

例如:

```
// as constant definitions
const AN_ARRAY = 1 |> 100;

// as initial property definitions
class A
{
	private $a = 1 |> 2;
}

// as default values for optional parameters:
function a($a = 1 |> 2)
{
	//
} 
```

所以事不宜迟，让我们重新实现范围操作符。

## 更新词法分析器

lexer 实现保持不变。令牌首先注册在[Zend/Zend _ language _ scanner . l](http://lxr.php.net/xref/PHP_7_0/Zend/zend_language_scanner.l)(第~1200 行):

```
<ST_IN_SCRIPTING>"|>" {
    RETURN_TOKEN(T_RANGE);
} 
```

然后在[Zend/Zend _ language _ parser . y](http://lxr.php.net/xref/PHP_7_0/Zend/zend_language_parser.y)(第~220 行)中声明:

```
%token T_RANGE 		     "|> (T_RANGE)" 
```

必须再次通过进入 **ext/tokenizer** 目录并执行 **tokenizer_data_gen.sh** 文件来重新生成 tokenizer 扩展。

## 更新解析器

解析器的实现部分与之前相同。我们再次通过在下一行(第 70 行)的末尾添加`T_RANGE`标记来说明操作符的优先级和结合性:

```
%nonassoc T_IS_EQUAL T_IS_NOT_EQUAL T_IS_IDENTICAL T_IS_NOT_IDENTICAL T_SPACESHIP T_RANGE 
```

然后我们再次更新`expr_without_variable`产生规则，尽管这一次语义动作(花括号内的代码)会略有不同。用下面的代码更新它(我把它放在了`T_SPACESHIP`规则的下面，第 930 行):

```
 |	expr T_RANGE expr
			{ $$ = zend_ast_create_binary_op(ZEND_RANGE, $1, $3); } 
```

这一次，我们使用了`zend_ast_create_binary_op`函数(而不是`zend_ast_create`函数)，它为我们创建了一个`ZEND_AST_BINARY_OP`节点。`zend_ast_create_binary_op`采用操作码名称，用于在编译阶段区分二进制操作。

因为我们现在重用了`ZEND_AST_BINARY_OP`节点类型，所以不需要像之前在 [Zend/zend_ast.h](http://lxr.php.net/xref/PHP_7_0/Zend/zend_ast.h) 文件中那样定义一个新的`ZEND_AST_RANGE`节点类型。

## 更新编译阶段

这一次，不需要更新 **Zend/zend_compile.c** 文件，因为[已经包含了处理二进制操作的必要逻辑](http://lxr.php.net/xref/PHP_7_0/Zend/zend_compile.c#7137)。因此，我们只是通过使我们的操作符成为一个`ZEND_AST_BINARY_OP`节点来重用这个逻辑。

以下是`zend_compile_binary_op`函数的精简版本:

```
void zend_compile_binary_op(znode *result, zend_ast *ast) /* {{{ */
{
	zend_ast *left_ast = ast->child[0];
	zend_ast *right_ast = ast->child[1];
	uint32_t opcode = ast->attr;

	znode left_node, right_node;
	zend_compile_expr(&left_node, left_ast);
	zend_compile_expr(&right_node, right_ast);

	if (left_node.op_type == IS_CONST && right_node.op_type == IS_CONST) {
		if (zend_try_ct_eval_binary_op(&result->u.constant, opcode,
				&left_node.u.constant, &right_node.u.constant)
		) {
			result->op_type = IS_CONST;
			zval_ptr_dtor(&left_node.u.constant);
			zval_ptr_dtor(&right_node.u.constant);
			return;
		}
	}

	do {
		// redacted code
		zend_emit_op_tmp(result, opcode, &left_node, &right_node);
	} while (0);
}
/* }}} */ 
```

正如我们所见，它与我们上次创建的`zend_compile_range`函数非常相似。两个重要的区别在于如何获取操作码类型，以及当两个操作数都是文字时会发生什么。

这次从 AST 节点获取操作码类型(与上次看到的硬编码相反)，因为`ZEND_AST_BINARY_OP`节点存储这个值(从新的产生式规则的语义动作来看)以区分二进制操作。当两个操作数都是文字时，将调用`zend_try_ct_eval_binary_op`函数。该函数如下所示:

```
static inline zend_bool zend_try_ct_eval_binary_op(zval *result, uint32_t opcode, zval *op1, zval *op2) /* {{{ */
{
	binary_op_type fn = get_binary_op(opcode);

	/* don't evaluate division by zero at compile-time */
	if ((opcode == ZEND_DIV || opcode == ZEND_MOD) &&
	    zval_get_long(op2) == 0) {
		return 0;
	} else if ((opcode == ZEND_SL || opcode == ZEND_SR) &&
	    zval_get_long(op2) < 0) {
		return 0;
	}

	fn(result, op1, op2);
	return 1;
}
/* }}} */ 
```

该函数根据操作码类型从 [Zend/zend_opcode.c](http://lxr.php.net/xref/PHP_7_0/Zend/zend_opcode.c) 中的`get_binary_op`函数([源](http://lxr.php.net/xref/PHP_7_0/Zend/zend_opcode.c#721))获得回调。这意味着我们接下来需要更新这个函数来迎合`ZEND_RANGE`操作码。将下面的 case 语句添加到`get_binary_op`函数中(第~750 行):

```
 case ZEND_RANGE:
			return (binary_op_type) range_function; 
```

现在我们必须定义`range_function`函数。这将在 [Zend/zend_operators.c](http://lxr.php.net/xref/PHP_7_0/Zend/zend_operators.c) 文件中与所有其他操作符一起完成:

```
ZEND_API int ZEND_FASTCALL range_function(zval *result, zval *op1, zval *op2) /* {{{ */
{
	zval tmp;

	ZVAL_DEREF(op1);
	ZVAL_DEREF(op2);

	if (Z_TYPE_P(op1) == IS_LONG && Z_TYPE_P(op2) == IS_LONG) {
		zend_long min = Z_LVAL_P(op1), max = Z_LVAL_P(op2);
		zend_ulong size, i;

		if (min > max) {
			zend_throw_error(NULL, "Min should be less than (or equal to) max");
			return FAILURE;
		}

		// calculate size (one less than the total size for an inclusive range)
		size = max - min;

		// the size cannot be greater than or equal to HT_MAX_SIZE
		// HT_MAX_SIZE - 1 takes into account the inclusive range size
		if (size >= HT_MAX_SIZE - 1) {
			zend_throw_error(NULL, "Range size is too large");
			return FAILURE;
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
	} else if ( // if both operands are either integers or doubles
		(Z_TYPE_P(op1) == IS_LONG || Z_TYPE_P(op1) == IS_DOUBLE)
		&& (Z_TYPE_P(op2) == IS_LONG || Z_TYPE_P(op2) == IS_DOUBLE)
	) {
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
			return FAILURE;
		}

		size = max - min;

		if (size >= HT_MAX_SIZE - 1) {
			zend_throw_error(NULL, "Range size is too large");
			return FAILURE;
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
	} else {
		zend_throw_error(NULL, "Unsupported operand types - only ints and floats are supported");
		return FAILURE;
	}

    return SUCCESS;
}
/* }}} */ 
```

函数原型包含两个新的宏:`ZEND_API`和`ZEND_FASTCALL`。`ZEND_API`用于控制函数的可见性，使它们对编译为共享对象的扩展可用。`ZEND_FASTCALL`用于确保使用更有效的调用约定，其中前两个参数将使用寄存器而不是堆栈来传递(与 x86 上的 64 位版本相比，更适用于 32 位版本)。

函数体与我们在上一篇文章中的 **Zend/zend_vm_def.h** 文件非常相似。特定于 VM 的东西不再存在，包括`HANDLE_EXCEPTION`宏调用(已经被替换为`return FAILURE;`)，并且`ZEND_VM_NEXT_OPCODE_CHECK_EXCEPTION`宏调用已经被完全移除(这种检查和操作需要留在 VM 中，因此稍后将从 VM 代码中调用该宏)。

另一个值得注意的区别是，我们将`ZVAL_DEFEF`应用于两个操作数，以确保引用得到正确处理。这是以前在 VM 内部使用伪宏`GET_OPn_ZVAL_PTR_DEREF`完成的事情，但是现在已经转移到这个函数中。这样做并不是因为在编译时需要它(因为对于编译时处理，两个操作数都必须是文字，它们不能被引用)，而是因为它使代码库中的其他地方能够安全地调用`range_function`，而不必担心引用处理。因此，引用处理是由大多数操作符函数执行的，而不是在它们的 VM 操作码定义中执行的(除非性能很重要)。

最后，我们必须将`range_function`原型添加到 [Zend/zend_operators.h](http://lxr.php.net/xref/PHP_7_0/Zend/zend_operators.h) 文件中:

```
ZEND_API int ZEND_FASTCALL range_function(zval *result, zval *op1, zval *op2); 
```

## 更新 Zend 虚拟机

现在，我们必须再次更新 Zend VM，以便在运行时处理`ZEND_RANGE`操作码的执行。将以下代码放入 **Zend/zend_vm_def.h** (底部):

```
ZEND_VM_HANDLER(182, ZEND_RANGE, CONST|TMPVAR|CV, CONST|TMPVAR|CV)
{
	USE_OPLINE
	zend_free_op free_op1, free_op2;
	zval *op1, *op2;

	SAVE_OPLINE();
	op1 = GET_OP1_ZVAL_PTR(BP_VAR_R);
	op2 = GET_OP2_ZVAL_PTR(BP_VAR_R);
	range_function(EX_VAR(opline->result.var), op1, op2);
	FREE_OP1();
	FREE_OP2();
	ZEND_VM_NEXT_OPCODE_CHECK_EXCEPTION();
} 
```

(同样，操作码号必须比当前最高操作码号大 1，这可以在 **Zend/zend_vm_opcodes.h** 文件的底部看到。)

这次的定义要短得多，因为所有的工作都在`range_function`中处理。我们只需调用这个函数，传入当前 opline 的结果操作数来保存计算出的值。从`range_function`中移除的异常检查和跳到下一个操作码仍然在 VM 中通过调用最后的`ZEND_VM_NEXT_OPCODE_CHECK_EXCEPTION`来处理。另外，如前所述，我们通过使用`GET_OPn_ZVAL_PTR`伪宏(而不是`GET_OPn_ZVAL_PTR_DEREF`)来避免在 VM 中处理引用。

现在通过执行 **Zend/zend_vm_gen.php** 文件重新生成虚拟机。

最后，漂亮的打印机需要再次更新文件 [Zend/zend_ast.c](http://lxr.php.net/xref/PHP_7_0/Zend/zend_ast.c) 。通过指定新运算符的优先级为 170 来更新优先级表注释(第 520 行):

```
*  170     non-associative == != === !== |> 
```

然后，在`zend_ast_export_ex`函数中插入一条`case`语句，处理`ZEND_AST_BINARY_OP` case 语句中的`ZEND_RANGE`操作码(第~1300 行):

```
case ZEND_RANGE:               BINARY_OP(" |> ",  170, 171, 171); 
```

## 结论

本文展示了实现范围操作符的另一种方法，其中计算逻辑被转移到 VM 之外。这样做的好处是能够在常量表达式上下文中使用范围操作符。

本系列文章的第三部分将通过介绍如何重载这个操作符来构建这个实现。这将使对象能够被用作操作数(例如那些来自 GMP 库或那些实现了`__toString`方法的对象)。它还将展示我们如何为字符串添加适当的 T2 支持(不像 PHP 当前的范围 T4 函数那样支持)。但是现在，我希望这已经很好地展示了在 PHP 中实现操作符时 ZE 的一些进一步的方面。

## 分享这篇文章