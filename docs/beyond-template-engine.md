# 超越模板引擎

> 原文：<https://www.sitepoint.com/beyond-template-engine/>

一般来说，模板引擎是个“好东西”

我是作为一个长期的 PHP/Perl 程序员，许多模板引擎(fastTemplate、Smarty、Perl 的 HTML::Template)的用户，以及作为我自己的作者， [bTemplate](http://www.massassi.com/bTemplate/) 。

然而，在与一位同事进行了长时间的讨论后，我认为绝大多数模板引擎(包括我自己的)都是错误的。我认为这条规则的一个例外是 [Smarty](http://smarty.php.net/) ，尽管我认为它太大了，并且考虑到本文的其余部分，相当没有意义。然而，您可能选择 Smarty(或类似的解决方案)有几个原因，本文稍后将对此进行探讨。

本文讨论模板理论。我们将看到为什么大多数“模板引擎”都是多余的，最后，我们将回顾一个轻量级的，快如闪电的替代品。

##### 下载和许可

模板类和本文使用的所有例子都可以在这里下载: [template.zip](https://www.sitepoint.com/examples/tempeng/template.zip) 。你可以根据麻省理工学院开源许可证使用这些文件中的代码，如[在](http://opensource.org/licenses/mit-license.html) [OSI](http://www.opensource.org/) 上发布的。

##### 模板引擎的一点背景知识

我们先来深究一下模板引擎的背景。模板引擎被设计成允许将业务逻辑(例如，从数据库中检索数据或计算运输成本)与数据表示分离。模板引擎解决了两个主要问题:

1.  如何实现这种分离
2.  如何将“复杂的”php 代码从 HTML 中分离出来

理论上，这使得没有 PHP 经验的 HTML 设计人员可以修改网站的外观，而不必查看任何 PHP 代码。

然而，模板系统也引入了一些复杂性。首先，我们现在有了一个由多个文件构建的“页面”。通常，您可能有负责业务逻辑的主 PHP 页面、呈现站点整体布局的外部“布局”模板、内部特定于内容的模板、数据库抽象层和模板引擎本身(可能包含也可能不包含多个文件)。或者，有些人只是在每个 PHP 页面的开头和结尾包含“页眉”和“页脚”文件。

生成一个页面需要的文件数量惊人。然而，由于 PHP 解析器相当快，使用的文件数量可能并不重要，除非您的站点获得疯狂的流量。然而，请记住模板系统引入了另一个层次的处理。不仅必须包含模板文件，还必须对它们进行解析(取决于模板系统，这可以通过多种方式实现——使用正则表达式、str_replaces、编译、词法解析等。).这就是模板基准测试变得流行的原因:因为模板引擎使用各种不同的方法来解析数据，其中一些方法比其他方法更快(而且，一些模板引擎比其他引擎提供更多的功能)。

##### 模板引擎基础

基本上，模板引擎利用一种用 c 编写的脚本语言(PHP)。在这种嵌入式脚本语言中，有另一种伪脚本语言(模板引擎支持的任何标签)。有些提供简单的变量插值和循环。其他的提供条件和嵌套循环。还有一些(至少 Smarty)提供了一个 PHP 大子集的接口，以及一个缓存层。

为什么我觉得自作聪明最接近正确？因为 Smarty 的目标是“业务逻辑与表示的分离”，而不是“PHP 代码与 HTML 代码的分离”。虽然这看起来是一个很小的区别，但却是非常重要的。任何模板引擎的最终目标都不应该是去除 HTML 中的所有逻辑。应该是把表现逻辑和业务逻辑分开。

在很多情况下，您只需要逻辑就可以正确显示数据。例如，假设您的业务逻辑是检索数据库中的用户列表。您的表示逻辑应该是在 3 列中显示用户列表。修改用户列表函数来返回 3 个数组是愚蠢的。毕竟，这个函数不应该关心数据会发生什么。然而，如果模板文件中没有某种逻辑，那正是您必须要做的。

虽然 Smarty 在这方面做得很好(允许您驾驭 PHP 的几乎所有方面)，但仍然存在一些问题。基本上，它只是提供了一个新语法的 PHP 接口。这样说，似乎有点傻。写`{foreach --args}`其实比写`<? foreach --args ?>`简单吗？如果您确实认为这更简单，那么问问自己，是否简单到可以看到包含一个巨大的模板库来实现这种分离的真正价值。诚然，Smarty 提供了许多其他优秀的特性，但似乎同样的好处可以在没有包含 Smarty 类库所涉及的巨大开销的情况下获得。

##### 替代解决方案

我基本上提倡的解决方案是一个“模板引擎”，它使用 PHP 代码作为其本机脚本语言。我知道以前有人这样做过。当我第一次读到它的时候，我想，“这有什么意义？”然而，在我研究了我的同事的论点并实现了一个使用直接 PHP 代码的模板系统之后，仍然实现了业务逻辑与表示逻辑分离的最终目标(大约 25 行代码，不包括注释)，我意识到了它的优势。

这个系统为像我们这样的开发人员提供了对大量 PHP 核心函数的访问，我们可以使用这些函数来格式化输出——像日期格式化这样的任务应该在模板中处理。此外，由于模板都是直接的 PHP 文件，像 [Zend Performance Suite](http://zend.com/store/products/zend-performance-suite.php) 和 [PHP Accelerator](http://www.php-accelerator.co.uk/) 这样的字节码缓存程序，模板可以自动缓存(因此，它们不必在每次被访问时重新解释)。如果您记得命名您的模板文件，以便这些程序可以识别它们为 php 文件，这只是一个优势(通常，您只需要确保它们具有. PHP 扩展名)。

虽然我认为这种方法远远优于典型的模板引擎，当然也有一些问题。反对这种系统的最明显的理由是 PHP 代码太复杂，设计者不应该学习 PHP。事实上，PHP 代码与 Smarty 等更高级的模板引擎的语法一样简单(如果不是更简单的话)。还有，设计师可以像这样使用 PHP 简写`<?=$var;?>`。这比`{$var}`更复杂吗？当然，它长了几个字符，但是如果您能够习惯它，您将获得 PHP 的所有功能，而没有解析模板文件所涉及的开销。

其次，也许更重要的是，基于 PHP 的模板没有内在的安全性。Smarty 提供了完全禁用模板文件中 PHP 代码的选项。它允许开发人员限制模板可以访问的函数和变量。如果没有恶意的设计者，这不是问题。然而，如果您允许外部用户上传或修改模板，我在这里介绍的基于 PHP 的解决方案绝对不提供安全性！任何代码都可以放入模板并运行。是的，甚至是一个`print_r($GLOBALS)`(这会让恶意用户访问脚本中的每个变量)！

也就是说，在我个人和专业工作过的项目中，大多数不允许最终用户修改或上传模板。因此，这不是一个问题。现在，让我们继续看代码。

##### 例子

下面是一个简单的用户列表页面的例子。

```
<?php   
require_once('template.php');   

/**   
* This variable holds the file system path to all our template files.   
*/   
$path = './templates/';   

/**   
* Create a template object for the outer template and set its variables.   
*/   
$tpl = & new Template($path);   
$tpl->set('title', 'User List');   

/**   
* Create a template object for the inner template and set its variables.  The   
* fetch_user_list() function simply returns an array of users.   
*/   
$body = & new Template($path);   
$body->set('user_list', fetch_user_list());   

/**   
* Set the fetched template of the inner template to the 'body' variable in   
* the outer template.   
*/   
$tpl->set('body', $body->fetch('user_list.tpl.php'));   

/**   
* Echo the results.   
*/   
echo $tpl->fetch('index.tpl.php');   
?> 
```

这里有两个重要的概念需要注意。首先是内部和外部模板的概念。外层模板包含定义站点主要外观的 HTML 代码。内部模板包含定义站点内容区域的 HTML 代码。当然，您可以在任意数量的层中拥有任意数量的模板。因为我通常为每个区域使用不同的模板对象，所以不存在命名空间问题。例如，我可以在内部和外部模板中都有一个名为‘title’的模板变量，而不用担心冲突。

这里有一个简单的模板示例，可以用来显示用户列表。注意特殊的 foreach 和 endforeach 语法[记录在 PHP 手册](http://www.php.net/manual/en/control-structures.alternative-syntax.php)中。这是完全可选的。

另外，你可能想知道为什么我用. php 扩展名来结束我的模板文件名。许多 php 字节码缓存解决方案(如 phpAccelerator)要求文件具有. PHP 扩展名，如果它们被认为是 PHP 文件的话。由于这些模板是 PHP 文件，为什么不利用这一点呢？

```
<table>   
    <tr>   
        <th>Id</th>   
        <th>Name</th>   
        <th>Email</th>   
        <th>Banned</th>   
    </tr>   
<? foreach($user_list as $user): ?>   
    <tr>   
        <td align="center"><?=$user['id'];?></td>   
        <td><?=$user['name'];?></td>   
        <td><a href="mailto:<?=$user['email'];?>"><?=$user['email'];?></a></td>   
        <td align="center"><?=($user['banned'] ? 'X' : '&nbsp;');?></td>   
    </tr>   
<? endforeach; ?>   
</table>

Here's a simple example of the layout.tpl.php (the template file that defines what the whole page will look like).

```

```
<html>  
    <head>  
        <title><?=$title;?></title>  
    </head>  

    <body>  

        <h2><?=$title;?></h2>  

<?=$body;?>  

    </body>  
</html>

And here's the parsed output.

```

```
<html>  
  <head>  
    <title>User List</title>  
  </head>  

  <body>  

    <h2>User List</h2>  

<table>  
  <tr>  
    <th>Id</th>  
    <th>Name</th>  
    <th>Email</th>  
    <th>Banned</th>  
  </tr>  
  <tr>  
    <td align="center">1</td>  
    <td>bob</td>  
    <td><a href="mailto:bob@mozilla.org">bob@mozilla.org</a></td>  
    <td align="center">&nbsp;</td>  
  </tr>  
  <tr>  
    <td align="center">2</td>  
    <td>judy</td>  
    <td><a href="mailto:judy@php.net">judy@php.net</a></td>  
    <td align="center">&nbsp;</td>  
  </tr>  
  <tr>  
    <td align="center">3</td>  
    <td>joe</td>  
    <td><a href="mailto:joe@opera.com">joe@opera.com</a></td>  
    <td align="center">&nbsp;</td>  
  </tr>  
  <tr>  
    <td align="center">4</td>  
    <td>billy</td>  
    <td><a href="mailto:billy@wakeside.com">billy@wakeside.com</a></td>  
    <td align="center">X</td>  
  </tr>  
  <tr>  
    <td align="center">5</td>  
    <td>eileen</td>  
    <td><a href="mailto:eileen@slashdot.org">eileen@slashdot.org</a></td>  
    <td align="center">&nbsp;</td>  
  </tr>  
</table>  
  </body>  
</html>
```

##### 贮藏

有了这样一个简单的解决方案，实现模板缓存就成了一项相当简单的任务。对于缓存，我们有第二个类，它扩展了原始的模板类。CachedTemplate 类实际上使用与原始模板类相同的 API。不同之处在于，我们必须将缓存设置传递给构造函数，并调用`fetch_cache()`而不是`fetch()`。

缓存的概念很简单。基本上，我们设置了一个缓存超时，表示输出应该保存的时间(以秒为单位)。在完成生成页面的所有工作之前，我们必须首先测试该页面是否已经被缓存，以及缓存是否仍然被认为是当前的。如果有缓存，我们就不需要为生成页面所需要的所有数据库工作和业务逻辑而烦恼了——我们可以简单地输出之前缓存的内容。

这种做法提出了能够唯一地标识缓存文件的问题。如果一个站点是由基于 GET 变量显示输出的中央脚本控制的，那么每个 PHP 文件只有一个缓存将是不成功的。例如，如果您有`index.php?page=about_us`，那么输出应该与用户调用`index.php?page=contact_us`时返回的完全不同。

这个问题通过为每个页面生成一个唯一的`cache_id`来解决。为此，我们获取被请求的实际文件，并与`REQUEST_URI`(基本上是整个 URL: `index.php?foo=bar&bar=foo`)一起散列。当然，散列由`CachedTemplate`类负责，但是要记住的重要一点是，当创建 CachedTemplate 对象时，绝对必须传递一个唯一的`cache_id`。当然，下面是一些例子。

缓存设置的使用包括以下步骤。

1.  `include()`模板源文件

*   创建一个新的`CachedTemplate`对象(并将路径传递给模板、惟一的`cache_id`和缓存超时)*   测试内容是否已经缓存*   如果是，显示文件，脚本执行结束*   否则，做所有的处理和`fetch()`模板*   `fetch_cache()`调用将自动生成一个新的缓存文件

这个脚本假设您的缓存文件将放在`./cache/`中，因此您必须创建该目录并`chmod`它，以便 Web 服务器可以写入它。还要注意，如果您在开发任何脚本的过程中发现错误，错误可以被缓存！因此，在开发过程中完全禁用缓存是一个好主意。最好的方法是将零(0)作为缓存生存期，这样，缓存总是会立即过期。

下面是一个实际使用缓存的例子。

```
<?php   
/**   
* Example of cached template usage.  Doesn't provide any speed increase since   
* we're not getting information from multiple files or a database, but it   
* introduces how the is_cached() method works.   
*/   

/**   
* First, include the template class.   
*/   
require_once('template.php');   

/**   
* Here is the path to the templates.   
*/   
$path = './templates/';   

/**   
* Define the template file we will be using for this page.   
*/   
$file = 'list.tpl.php';   

/**   
* Pass a unique string for the template we want to cache.  The template   
* file name + the server REQUEST_URI is a good choice because:   
*    1\. If you pass just the file name, re-used templates will all   
*       get the same cache.  This is not the desired behavior.   
*    2\. If you just pass the REQUEST_URI, and if you are using multiple   
*       templates per page, the templates, even though they are completely   
*       different, will share a cache file (the cache file names are based   
*       on the passed-in cache_id.   
*/   
$cache_id = $file . $_SERVER['REQUEST_URI'];   
$tpl = & new CachedTemplate($path, $cache_id, 900);   

/**   
* Test to see if the template has been cached.  If it has, we don't   
* need to do any processing.  Thus, if you put a lot of db calls in   
* here (or file reads, or anything processor/disk/db intensive), you   
* will significantly cut the amount of time it takes for a page to   
* process.   
*   
* This should be read aloud as "If NOT Is_Cached"   
*/   
if(!($tpl->is_cached())) {   
    $tpl->set('title', 'My Title');   
    $tpl->set('intro', 'The intro paragraph.');   
    $tpl->set('list', array('cat', 'dog', 'mouse'));   
}   

/**   
* Fetch the cached template.  It doesn't matter if is_cached() succeeds   
* or fails - fetch_cache() will fetch a cache if it exists, but if not,   
* it will parse and return the template as usual (and make a cache for   
* next time).   
*/   
echo $tpl->fetch_cache($file);   
?>
```

##### 设置多个变量

如何同时设置多个变量？这里有一个使用 Ricardo Garcia 提供的方法的例子。

```
<?php     
require_once('template.php');     

$tpl = & new Template('./templates/');     
$tpl->set('title', 'User Profile');     

$profile = array(     
    'name' => 'Frank',     
    'email' => 'frank@bob.com',     
    'password' => 'ultra_secret'     
);     

$tpl->set_vars($profile);     

echo $tpl->fetch('profile.tpl.php');     
?> 
```

关联的模板如下所示:

```
<table cellpadding="3" border="0" cellspacing="1">     
    <tr>     
        <td>Name</td>     
        <td><?=$name;?></td>     
    </tr>     
    <tr>     
        <td>Email</td>     
        <td><?=$email;?></td>     
    </tr>     
    <tr>     
        <td>Password</td>     
        <td><?=$password;?></td>     
    </tr>     
</table> 
```

解析后的输出如下:

```
<table cellpadding="3" border="0" cellspacing="1">    
  <tr>    
    <td>Name</td>    
    <td>Frank</td>    
  </tr>    
  <tr>    
    <td>Email</td>    
    <td>frank@bob.com</td>    
  </tr>    
  <tr>    
    <td>Password</td>    
    <td>ultra_secret</td>    
  </tr>    
</table>
```

特别感谢 Ricardo Garcia 和 Harry Fuecks 对本文的贡献。

##### 相关链接

这里有一个探索模板引擎的好资源列表。

*   [Web 应用工具包模板视图](http://wact.sourceforge.net/index.php/TemplateView)——关于所有类型模板方法的丰富信息
*   [MVC 模式](http://www.phppatterns.com/index.php/article/articleview/11/)–三层应用设计描述
*   另一个基于 php 的模板引擎，使用 PEAR::Cache_Lite
*   [模板和模板引擎](http://phppatterns.com/index.php/article/articleview/4/1/1/)–关于各种模板实现的更多信息
*   [Smarty](http://smarty.php.net/)–编译模板引擎

##### 模板类源代码

最后是模板类。

```
<?php     
/**     
* Copyright (c) 2003 Brian E. Lozier (brian@massassi.net)     
*     
* set_vars() method contributed by Ricardo Garcia (Thanks!)     
*     
* Permission is hereby granted, free of charge, to any person obtaining a copy     
* of this software and associated documentation files (the "Software"), to     
* deal in the Software without restriction, including without limitation the     
* rights to use, copy, modify, merge, publish, distribute, sublicense, and/or     
* sell copies of the Software, and to permit persons to whom the Software is     
* furnished to do so, subject to the following conditions:     
*     
* The above copyright notice and this permission notice shall be included in     
* all copies or substantial portions of the Software.     
*     
* THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR     
* IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,     
* FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE     
* AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER     
* LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING     
* FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS     
* IN THE SOFTWARE.     
*/     

class Template {     
    var $vars; /// Holds all the template variables     
    var $path; /// Path to the templates     

    /**     
     * Constructor     
     *     
     * @param string $path the path to the templates     
     *     
     * @return void     
     */     
    function Template($path = null) {     
        $this->path = $path;     
        $this->vars = array();     
    }     

    /**     
     * Set the path to the template files.     
     *     
     * @param string $path path to template files     
     *     
     * @return void     
     */     
    function set_path($path) {     
        $this->path = $path;     
    }     

    /**     
     * Set a template variable.     
     *     
     * @param string $name name of the variable to set     
     * @param mixed $value the value of the variable     
     *     
     * @return void     
     */     
    function set($name, $value) {     
        $this->vars[$name] = $value;     
    }     

    /**     
     * Set a bunch of variables at once using an associative array.     
     *     
     * @param array $vars array of vars to set     
     * @param bool $clear whether to completely overwrite the existing vars     
     *     
     * @return void     
     */     
    function set_vars($vars, $clear = false) {     
        if($clear) {     
            $this->vars = $vars;     
        }     
        else {     
            if(is_array($vars)) $this->vars = array_merge($this->vars, $vars);     
        }     
    }     

    /**     
     * Open, parse, and return the template file.     
     *     
     * @param string string the template file name     
     *     
     * @return string     
     */     
    function fetch($file) {     
        extract($this->vars);          // Extract the vars to local namespace     
        ob_start();                    // Start output buffering     
        include($this->path . $file);  // Include the file     
        $contents = ob_get_contents(); // Get the contents of the buffer     
        ob_end_clean();                // End buffering and discard     
        return $contents;              // Return the contents     
    }     
}     

/**     
* An extension to Template that provides automatic caching of     
* template contents.     
*/     
class CachedTemplate extends Template {     
    var $cache_id;     
    var $expire;     
    var $cached;     

    /**     
     * Constructor.     
     *     
     * @param string $path path to template files     
     * @param string $cache_id unique cache identifier     
     * @param int $expire number of seconds the cache will live     
     *     
     * @return void     
     */     
    function CachedTemplate($path, $cache_id = null, $expire = 900) {     
        $this->Template($path);     
        $this->cache_id = $cache_id ? 'cache/' . md5($cache_id) : $cache_id;     
        $this->expire   = $expire;     
    }     

    /**     
     * Test to see whether the currently loaded cache_id has a valid     
     * corrosponding cache file.     
     *     
     * @return bool     
     */     
    function is_cached() {     
        if($this->cached) return true;     

        // Passed a cache_id?     
        if(!$this->cache_id) return false;     

        // Cache file exists?     
        if(!file_exists($this->cache_id)) return false;     

        // Can get the time of the file?     
        if(!($mtime = filemtime($this->cache_id))) return false;     

        // Cache expired?     
        if(($mtime + $this->expire) < time()) {     
            @unlink($this->cache_id);     
            return false;     
        }     
        else {     
            /**     
             * Cache the results of this is_cached() call.  Why?  So     
             * we don't have to double the overhead for each template.     
             * If we didn't cache, it would be hitting the file system     
             * twice as much (file_exists() & filemtime() [twice each]).     
             */     
            $this->cached = true;     
            return true;     
        }     
    }     

    /**     
     * This function returns a cached copy of a template (if it exists),     
     * otherwise, it parses it as normal and caches the content.     
     *     
     * @param $file string the template file     
     *     
     * @return string     
     */     
    function fetch_cache($file) {     
        if($this->is_cached()) {     
            $fp = @fopen($this->cache_id, 'r');     
            $contents = fread($fp, filesize($this->cache_id));     
            fclose($fp);     
            return $contents;     
        }     
        else {     
            $contents = $this->fetch($file);     

            // Write the cache     
            if($fp = @fopen($this->cache_id, 'w')) {     
                fwrite($fp, $contents);     
                fclose($fp);     
            }     
            else {     
                die('Unable to write cache.');     
            }     

            return $contents;     
        }     
    }     
}     
?>
```

关于这里给出的解决方案，另一个需要注意的重要事情是，我们将模板的文件名传递给`fetch()`方法调用。如果你想重用模板对象而不必`re-set()`所有的变量，这可能是有用的。

请记住:模板引擎的目的应该是将业务逻辑与表示逻辑分开，而不是将 PHP 代码与 HTML 代码分开。

## 分享这篇文章