# 如何让现代 PHP 更现代？带预处理！

> 原文：<https://www.sitepoint.com/how-to-make-modern-php-more-modern-with-preprocessing/>

让我们找点乐子。不久前，[我试验了 PHP 宏](https://www.sitepoint.com/php-macros-for-fun-and-profit)，增加了 Python 范围语法。然后，才华横溢的 [SaraMG](https://twitter.com/SaraMG) [提到了 RFC](https://twitter.com/SaraMG/status/824075559684575232) ，而 [LordKabelo](https://twitter.com/LordKabelo) 则建议在 PHP 中加入 C#风格的 getters 和 setters。

意识到对于一个局外人来说，建议和实现一个新的语言特性是多么的慢，我向我的编辑…

> 本教程的代码可以在 [Github](https://github.com/sitepoint-editors/tutorial-sharpening-php) 上找到。已经用 PHP `^7.1`测试过了，生成的代码应该可以在 PHP `^5.6|^7.0`上运行。

![Vector illustration of cog with upward facing arrows running through it, indicating preprocessing, upgrading, improving](img/a915e01818aaa6caeaa5936a23221d3b.png)

## 宏又是如何工作的？

自从我谈到宏已经有一段时间了(也许你从来没有听说过它们)。为了唤起你的记忆，他们采用了这样的代码:

```
macro {
  →(···expression)
} >> {
  ··stringify(···expression)
}

macro {
  T_VARIABLE·A[
    ···range
  ]
} >> {
  eval(
    '$list = ' . →(T_VARIABLE·A) . ';' .
    '$lower = ' . explode('..', →(···range))[0] . ';' .
    '$upper = ' . explode('..', →(···range))[1] . ';' .
    'return array_slice($list, $lower, $upper - $lower);'
  )
} 
```

…并转变自定义 PHP 语法，就像这样:

```
$few = many[1..3]; 
```

…转换成有效的 PHP 语法，就像这样:

```
$few = eval(
    '$list = ' . '$many' . ';'.
    '$lower = ' . explode('..', '1..3')[0] . ';' .
    '$upper = ' . explode('..', '1..3')[1] . ';' .
    'return array_slice($list, $lower, $upper - $lower);'
); 
```

> 如果你想看看这是如何工作的，可以去[我写的关于它的文章](https://www.sitepoint.com/php-macros-for-fun-and-profit)。

诀窍是理解解析器如何标记一串代码，构建一个宏模式，然后将该模式递归地应用于新语法。

然而，宏库并没有被很好地记录。很难确切地知道模式需要什么样子，或者最终生成什么有效的语法。每一个新的应用程序都要求在其他人理解实际情况之前编写一个这样的教程。

## 建立一个基地

那么，让我们看看手头的应用程序。我们想在 PHP 中加入 getter 和 setter 语法，类似于 C#的语法。在我们这样做之前，我们需要有一个良好的代码基础。也许是某种特征的形式，我们可以将它添加到需要这种新功能的类中。

我们需要实现检查类定义的代码，并为它看到的每个特殊属性或注释创建这些动态 getter 和 setter 方法。

也许我们可以从定义一个特殊的方法名格式和神奇的`__get`和`__set`方法开始:

```
namespace App;

trait AccessorTrait
{
  /**
   * @inheritdoc
   *
   * @param string $property
   * @param mixed $value
   */
  public function __get($property)
  {
    if (method_exists($this, "__get_{$property}")) {
      return $this->{"__get_{$property}"}();
    }
  }

  /**
   * @inheritdoc
   *
   * @param string $property
   * @param mixed $value
   */
  public function __set($property, $value)
  {
    if (method_exists($this, "__set_{$property}")) {
      return $this->{"__set_{$property}"}($value);
    }
  }
} 
```

以名称`__get_`和`__set_`开始的每个方法都需要连接到一个尚未定义的属性。我们可以想象这样的语法:

```
namespace App;

class Sprocket
{
    private $type {
        get {
            return $this->type;
        }

        set {
            $this->type = strtoupper($value);
        }
    };
} 
```

…被转换成非常类似于:

```
namespace App;

class Sprocket {
    use AccessorTrait;

    private $type;

    private function __get_type() {
        return $this->type;  
    }

    private function __set_type($value) {
        $this->type = strtoupper($value);   
    }
} 
```

## 定义宏

定义所需的宏是其中最难的部分。由于缺乏文档(和广泛使用)，并且只有少数有用的异常消息，所以大部分都是反复试验。

我花了几个小时想出了以下模式:

```
macro ·unsafe {
  ·ns()·class {
    ···body
  }
} >> {
·class {
    use AccessorTrait;

    ···body
  }
}

macro ·unsafe {
  private T_VARIABLE·var {
    get {
      ···getter
    }

    set {
      ···setter
    }
  };
} >> {
  private T_VARIABLE·var;

  private function ··concat(__get_ ··unvar(T_VARIABLE·var))() {
    ···getter
  }

  private function ··concat(__set_ ··unvar(T_VARIABLE·var))($value) {
    ···setter
  }
} 
```

好了，让我们看看这两个宏在做什么:

1.  我们从匹配`class MyClass { ... }`开始，并插入我们之前构建的`AccessorTrait`。这提供了`__get`和`__set`的实现，它们将`__get_bar`链接到`print $class->bar`等。
2.  我们匹配访问器块语法，并用一个普通的属性定义替换它，后面跟着几个单独的方法定义。我们可以将`get { ... }`和`set { ... }`块的确切内容包装在这些函数中。

起初，当您运行这段代码时，您会得到一个错误。这是因为`··unvar`函数不是宏处理器的标准部分。这是我必须添加的东西，从`$type`转换到`type`:

```
namespace Yay\DSL\Expanders;

use Yay\Token;
use Yay\TokenStream;

function unvar(TokenStream $ts) : TokenStream {
  $str = str_replace('$', '', (string) $ts);

  return
    TokenStream::fromSequence(
      new Token(
        T_CONSTANT_ENCAPSED_STRING, $str
      )
    )
  ;
} 
```

我能够复制(几乎完全复制)宏解析器中包含的`··stringify`扩展器。你不需要了解太多 Yay 的内部结构就能明白它在做什么。将一个`TokenStream`转换为一个字符串(在这个上下文中)意味着您正在获取当前引用的任何令牌的字符串值——在这个例子中是`··unvar(T_VARIABLE·var)`——并对其执行字符串操作。

> `(string) $ts`变成了`"$type"`，与`"T_VARIABLE·var"`相对。

通常，当这些宏被放入它们要应用的脚本中时，它们就会被应用。换句话说，我们可以创建一个类似如下的脚本:

```
<?php

macro ·unsafe {
  ...
} >> {
  ...
}

macro ·unsafe {
  ...
} >> {
  ...
}

namespace App;

trait AccessorTrait
{
  ...
}

class Sprocket
{
  private $type {
    get {
      return $this->type;
    }

    set {
      $this->type = strtoupper($value);
    }
  };
} 
```

…然后我们可以使用如下命令来运行它:

```
vendor/bin/yay src/Sprocket.pre >> src/Sprocket.php 
```

最后，我们可以使用以下代码(通过一些 Composer PSR-4 自动加载):

```
require __DIR__ . "/vendor/autoload.php";

$sprocket = new App\Sprocket();
$sprocket->type = "acme sprocket";

print $sprocket->type; // Acme Sprocket 
```

## 自动化转换

作为一个手工过程，这很糟糕。谁想在每次更改`src/Sprocket.pre`时运行 bash 命令呢？幸运的是，我们可以自动化这一点！

第一步是定义自定义自动加载器:

```
spl_autoload_register(function($class) {
  $definitions = require __DIR__ . "/vendor/composer/autoload_psr4.php";

  foreach ($definitions as $prefix => $paths) {
    $prefixLength = strlen($prefix);

    if (strncmp($prefix, $class, $prefixLength) !== 0) {
      continue;
    }

    $relativeClass = substr($class, $prefixLength);

    foreach ($paths as $path) {
      $php = $path . "/" . str_replace("\\", "/", $relativeClass) . ".php";

      $pre = $path . "/" . str_replace("\\", "/", $relativeClass) . ".pre";

      $relative = ltrim(str_replace(__DIR__, "", $pre), DIRECTORY_SEPARATOR);

      $macros = __DIR__ . "/macros.pre";

      if (file_exists($pre)) {
        // ... convert and load file
      }
    }
  }
}, false, true); 
```

> 您可以将该文件另存为`autoload.php`，并通过 Composer 的自动加载器使用`files`自动加载来包含它，正如文档中的[所解释的。](https://getcomposer.org/doc/04-schema.md#files)

该定义的第一部分直接来自 PSR-4 规范的[示例实现。我们获取 Composer 的 PSR-4 定义文件，对于每个前缀，我们检查它是否与当前加载的类匹配。](http://www.php-fig.org/psr/psr-4/examples)

如果匹配，我们检查每个潜在的路径，直到我们找到一个`file.pre`，其中定义了我们的定制语法。然后我们获取一个`macros.pre`文件的内容(在项目的基本目录中),并创建一个临时文件——使用`macros.pre`内容+匹配文件的内容。这意味着宏可以用于我们传递给 Yay 的文件。一旦 Yay 编译了`file.pre.interim` → `file.php`，我们就删除`file.pre.interim`。

该过程的代码是:

```
if (file_exists($php)) {
  unlink($php);
}

file_put_contents(
  "{$pre}.interim",
  str_replace(
    "<?php",
    file_get_contents($macros),
    file_get_contents($pre)
  )
);

exec("vendor/bin/yay {$pre}.interim >> {$php}");

$comment = "
  # This file is generated, changes you make will be lost.
  # Make your changes in {$relative} instead.
";

file_put_contents(
  $php,
  str_replace(
    "<?php",
    "<?php\n{$comment}",
    file_get_contents($php)
  )
);

unlink("{$pre}.interim");

require_once $php; 
```

请注意在调用`spl_autoload_register`结束时的两个布尔值。首先是这个自动加载器是否应该抛出加载错误的异常。第二个问题是这个自动加载器是否应该放在堆栈的前面。这将它放在 Composer 的自动加载器之前，这意味着我们可以在 Composer 试图加载`file.php`之前转换`file.pre`！

## 创建插件框架

这种自动化很好，但是如果每个项目都要重复，那就浪费了。如果我们可以`composer require`一个依赖(对于一个新的语言特性),并且它可以工作，那会怎么样？让我们这样做吧…

首先，我们需要创建一个新的 repo，包含以下文件:

*   `composer.json` →自动加载以下文件
*   `functions.php` →创建宏路径函数(到其他库可以动态添加自己的宏文件)
*   `expanders.php` →创建扩展函数，如`··unvar`
*   `autoload.php` →增加 Composer 的自动加载程序，将每个库的宏文件加载到每个编译的`.pre`文件中

```
{
  "name": "pre/plugin",
  "require": {
    "php": "^7.0",
    "yay/yay": "dev-master"
  },
  "autoload": {
    "files": [
      "functions.php",
      "expanders.php",
      "autoload.php"
    ]
  },
  "minimum-stability": "dev",
  "prefer-stable": true
} 
```

> 这是来自`composer.json`

```
<?php

namespace Pre;

define("GLOBAL_KEY", "PRE_MACRO_PATHS");

/**
 * Creates the list of macros, if it is undefined.
 */
function initMacroPaths() {
  if (!isset($GLOBALS[GLOBAL_KEY])) {
    $GLOBALS[GLOBAL_KEY] = [];
  }
}

/**
 * Adds a path to the list of macro files.
 *
 * @param string $path
 */
function addMacroPath($path) {
  initMacroPaths();
  array_push($GLOBALS[GLOBAL_KEY], $path);
}

/**
 * Removes a path to the list of macro files.
 *
 * @param string $path
 */
function removeMacroPath($path) {
  initMacroPaths();

  $GLOBALS[GLOBAL_KEY] = array_filter(
    $GLOBALS[GLOBAL_KEY],
    function($next) use ($path) {
      return $next !== $path;
    }
  );
}

/**
 * Gets all macro file paths.
 *
 * @return array
 */
function getMacroPaths() {
  initMacroPaths();
  return $GLOBALS[GLOBAL_KEY];
} 
```

> 这是来自`functions.php`

想到使用`$GLOBALS`作为宏文件路径的存储，您可能会感到害怕。这不重要，因为我们可以用任何其他方式存储这些路径。这只是演示该模式的最简单的方法。

```
<?php

namespace Yay\DSL\Expanders;

use Yay\Token;
use Yay\TokenStream;

function unvar(TokenStream $ts) : TokenStream {
  $str = str_replace('$', '', (string) $ts);

  return
    TokenStream::fromSequence(
      new Token(
        T_CONSTANT_ENCAPSED_STRING, $str
      )
    )
  ;
} 
```

> 这是来自`expanders.php`

```
<?php

namespace Pre;

if (file_exists(__DIR__ . "/../../autoload.php")) {
  define("BASE_DIR", realpath(__DIR__ . "/../../../"));
}

spl_autoload_register(function($class) {
  $definitions = require BASE_DIR . "/vendor/composer/autoload_psr4.php";

  foreach ($definitions as $prefix => $paths) {
    // ...check $prefixLength

    foreach ($paths as $path) {
      // ...create $php and $pre

      $relative = ltrim(str_replace(BASE_DIR, "", $pre), DIRECTORY_SEPARATOR);

      $macros = BASE_DIR . "/macros.pre";

      if (file_exists($pre)) {
        // ...remove existing PHP file

        foreach (getMacroPaths() as $macroPath) {
          file_put_contents(
            "{$pre}.interim",
            str_replace(
              "<?php",
              file_get_contents($macroPath),
              file_get_contents($pre)
            )
          );
        }

        // ...write and include the PHP file
      }
    }
  }
}, false, true); 
```

> 这是来自`autoload.php`

现在，额外的宏插件可以使用这些函数将它们自己的代码钩入系统…

## 创建新的语言功能

随着插件代码的构建，我们可以将我们的类访问器重构为一个独立的、自动应用的特性。我们需要再创建几个文件来实现这一点:

*   `composer.json` →需要基础插件库并自动加载以下文件
*   `macros.pre` →该插件的宏代码
*   `functions.php` →放置以将访问器宏挂接到基本插件系统
*   `src/AccessorsTrait.php` →与之前基本相同

```
{
    "name": "pre/class-accessors",
    "require": {
        "php": "^7.0",
        "pre/plugin": "dev-master"
    },
    "autoload": {
        "files": [
            "functions.php"
        ],
        "psr-4": {
            "Pre\\": "src"
        }
    },
    "minimum-stability": "dev",
    "prefer-stable": true
} 
```

> 这是来自`composer.json`

```
namespace Pre;

addMacroPath(__DIR__ . "/macros.pre"); 
```

> 这是来自`functions.php`

```
macro ·unsafe {
  ·ns()·class {
      ···body
  }
} >> {
  ·class {
    use \Pre\AccessorsTrait;

    ···body
  }
}

macro ·unsafe {
  private T_VARIABLE·variable {
    get {
      ···getter
    }

    set {
      ···setter
    }
  };
} >> {
  // ...
}

macro ·unsafe {
  private T_VARIABLE·variable {
    set {
      ···setter
    }

    get {
      ···getter
    }
  };
} >> {
  // ...
}

macro ·unsafe {
  private T_VARIABLE·variable {
    set {
      ···setter
    }
  };
} >> {
  // ...
}

macro ·unsafe {
  private T_VARIABLE·variable {
    get {
      ···getter
    }
  };
} >> {
  // ...
} 
```

> 这是来自`macros.pre`

与以前的版本相比，这个宏文件更详细一些。可能有一种更优雅的方式来处理所有可以定义访问器的安排，但是我还没有找到。

## 把所有的放在一起

现在一切都打包好了，使用新的语言特性就相当简单了。看看这个快速演示吧！

![Demonstration](img/d4f98a36901578a7ae28a9dc5a614ed8.png)

你可以在 Github 上找到这些插件库:

*   [基础插件](https://github.com/preprocess/pre-plugin)
*   [类访问器插件](https://github.com/preprocess/pre-class-accessors)

## 结论

和所有事情一样，这可能会被滥用。宏也不例外。尽管从概念上来说它很酷，但这段代码肯定不是生产就绪的。

**请不要成为那个评论你认为使用这些代码会有多糟糕的人**。实际上，我并不建议你以这种形式使用这段代码。

话虽如此，也许你认为这是一个很酷的想法。你能想到你希望 PHP 获得的其他语言特性吗？也许您可以使用类访问器库作为一个例子来开始。也许你想使用插件库来实现自动化，以至于你可以看到你的想法是否有生命力。

请在评论中告诉我们进展如何。

> 自从撰写本教程以来，我一直在疯狂地研究底层库。以至于现在有一个托管和演示这些代码的网站: [https://preprocess.io](https://preprocess.io) 。它仍处于 alpha 状态，但它展示了我在这里谈到的所有代码，以及其他一些代码。还有一个方便的 REPL，如果你想尝试任何宏。

## 分享这篇文章