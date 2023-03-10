# 如何创建一个 Laravel CSS-Minify 命令

> 原文：<https://www.sitepoint.com/create-laravel-css-minify-command/>

在本文中，您将学习如何使用 Laravel 的 Artisan 命令行工具，以及如何创建自定义命令。请注意，您需要熟悉 Laravel 框架才能充分理解本文。

## 我们在建造什么

在本教程中，我们将构建一个命令来缩小我们的 css 资产，其用法如下:

```
cssmin 'output_path'  'file1'...'fileN'  --comments --concat
```

*   `output_path`:(必选)保存缩小文件的路径，(`style.css` - > `style.min.css`)。
*   `file1 ... fileN`:(必选)要缩小的文件列表。
*   `--comments`:(可选)添加此选项以保留注释。
*   `--concat`:(可选)将缩小后的文件连接成一个名为`all.min.css`的文件。

## 什么是 Laravel 命令

`Artisan`是 Laravel 中命令行实用程序的名称。它附带了一组预定义的命令，您可以用`php artisan list`列出这些命令。如果你想显示特定命令的帮助，你可以使用`php artisan help command`。

## 创建 Css Minifier 命令

要创建 artisan 命令，可以使用`command:make`命令。该命令接受一个参数:

*   `name`:命令的类名。

三个选项:

*   `--command`:运行命令应该键入的名称。
*   `--path`:默认情况下，命令存储在`app/commands`文件夹中，但是，您可以使用此选项进行更改。
*   `--namespace`:你可以使用这个选项来命名你的命令集，例如在命令`command:make`中，`make`命令在`command`命名空间下。

现在，为了创建我们的命令，我们将使用`php artisan command:make CssMinCommand --command=cssmin`，它将在我们的`app/commands`目录中创建一个`CssMinCommand.php`文件。

```
use Illuminate\Console\Command;
use Symfony\Component\Console\Input\InputOption;
use Symfony\Component\Console\Input\InputArgument;

class CssminCommand extends Command{
    protected $name = 'cssmin';
    protected $description = 'Command description.';

    public function __construct(){
		parent::__construct();
	}

    public function fire(){
		//
	}

	protected function getArguments(){
		return array(
			array('example', InputArgument::REQUIRED, 'An example argument.'),
		);
	}

	protected function getOptions(){
		return array(
			array('example', null, InputOption::VALUE_OPTIONAL, 'An example option.', null),
		);
	}
}
```

我们的`CssMinCommand`类扩展了`Illuminate\Console\Command`，并覆盖了两个方法(`getArguments`、`getOptions`)。

*   `getArguments`:这个函数返回一个应该传递给命令的参数数组，(例如:我们传递给`cssmin`命令的文件列表)。
*   `getOptions`:返回您可以传递给命令的选项或开关列表。(如`--comments`)。

**注意:**选项可能有值，也可能没有值，`--comments`只是一个标志，如果传递给命令，它将返回`true`，而`--ouptput='public/assets'`将返回一个值。

当您的命令被执行时，会调用`fire`方法，所以这是我们需要放置命令逻辑的地方。

### 注册命令

如果你试图运行我们的命令`php artisan cssmin 'args'`，你会得到一个`Command "cssmin" is not defined`。

要注册一个命令，您需要将其添加到`artisan.php`文件中:

```
Artisan::add( new CssMinCommand );

//or through the container
Artisan::add( App::make("CssMinCommand") );
```

如果你不想把你的命令放在`artisan.php`文件中，你可以创建一个单独的文件并包含它，或者如果你正在创建一个包，你可以在你的[服务提供商](http://laravel.com/docs/packages#service-providers)中注册它们。

### 争论

在我们的`getArguments`方法中，我们将定义我们的`output`和`files`。
要定义一个参数，我们需要传递一组值:

```
array(  'name',  'mode',  'description',  'defaultValue'  )
```

*   `name`:检索参数时使用的键名。
*   `mode`:可以有三个选项之一:

    *   `InputArgument::REQUIRED`:自变量是必需的。
    *   `InputArgument::OPTIONAL`:参数是可选的。
    *   `InputArgument::IS_ARRAY`:参数接受多个值(例如:`file1...fileN`)。

    但是，你可以像`InputArgument::IS_ARRAY | InputArgument::REQUIRED`一样组合它们(参数是必需的，并且必须是数组)。

*   `description`:打印命令帮助时有用。
*   `defaultValue`:如果没有提供参数。

所以我们的`getArguments`方法将是:

```
protected function getArguments(){
        return array(
            array(
                'output', 
                InputArgument::REQUIRED,
                'Path to output directory'
            ),
            array(
                'files', 
                InputArgument::IS_ARRAY | InputArgument::OPTIONAL ,
                "List of css files to minify"
            ),
        );
    }
```

**注意:**当使用`IS_ARRAY`参数时，它应该是返回的参数数组中的最后一个。(很明显)。

### 选择

我们的`cssmin`命令只有两个选项。为了定义一个选项，我们传递一个数组:

```
array('name',  'shortcut',  'mode',  'description',  'defaultValue')
```

*   `name`:您的期权名称(例如:`comments`)。
*   `shortcut`:你的期权的一个较短版本(例如:`--verbose`和`-v`)。
*   `mode`:可以是四个选项之一(`InputOption::VALUE_IS_ARRAY`、`InputOption::VALUE_OPTIONAL`、`InputOption::VALUE_REQUIRED`、`InputOption::VALUE_NONE`，前三个值与自变量类似。

    *   `VALUE_NONE`:表示该选项为布尔标志(例如:`--verbose`)。
*   `description`:打印命令帮助时有用。

*   `defaultValue`:如果没有提供选项值。

所以我们的`getOptions`方法将是:

```
protected function getOptions(){
        return array(
            array('comments', 'c', InputOption::VALUE_NONE, 'Don\'t strip comments' , null),
            array('concat', null, InputOption::VALUE_NONE, 'Concat the minified result to one file' , null),
        );
    }
```

### 运行命令

当我们的`fire`方法被调用时，我们需要收集参数和选项。我们可以创建一个单独的函数来完成这项工作:

```
private function init(){
    // retrun an array
    $this->files = $this->argument('files');
    // return a string
    $this->output_path = $this->argument('output');
    // return true if passed, otherwise false
    $this->comments = $this->option('comments');
    // return true if passed, otherwise false
    $this->concat = $this->option('concat');
}
```

`argument`和`option`方法将一个键作为参数，并返回适当的值。

为了让我们的例子简洁明了，我们将使用这个简单的函数，并对其进行一点小的修改。

```
private function minify( $css, $comments ){
        // Normalize whitespace
        $css = preg_replace( '/\s+/', ' ', $css );

        // Remove comment blocks, everything between /* and */, unless preserved with /*! ... */
        if( !$comments ){
            $css = preg_replace( '/\/\*[^\!](.*?)\*\//', '', $css );
        }//if

        // Remove ; before }
        $css = preg_replace( '/;(?=\s*})/', '', $css );

        // Remove space after , : ; { } */ >
        $css = preg_replace( '/(,|:|;|\{|}|\*\/|>) /', '$1', $css );

        // Remove space before , ; { } ( ) >
        $css = preg_replace( '/ (,|;|\{|}|\(|\)|>)/', '$1', $css );

        // Strips leading 0 on decimal values (converts 0.5px into .5px)
        $css = preg_replace( '/(:| )0\.([0-9]+)(%|em|ex|px|in|cm|mm|pt|pc)/i', '${1}.${2}${3}', $css );

        // Strips units if value is 0 (converts 0px to 0)
        $css = preg_replace( '/(:| )(\.?)0(%|em|ex|px|in|cm|mm|pt|pc)/i', '${1}0', $css );

        // Converts all zeros value into short-hand
        $css = preg_replace( '/0 0 0 0/', '0', $css );

        // Shortern 6-character hex color codes to 3-character where possible
        $css = preg_replace( '/#([a-f0-9])\\1([a-f0-9])\\2([a-f0-9])\\3/i', '#\1\2\3', $css );

        return trim( $css );
    }//minify
```

现在，为了处理我们的参数(文件),我们将创建一个单独的方法来完成这项工作。

```
private function processFiles(){
        // array of minified css
        $css_result = [];

        foreach ( $this->files as $file ) {
            //read file content
            $file_content = file_get_contents( $file );
            //minify CSS and add it to the result array
            $css_result[] = $this->minify( $file_content, $this->comments );
        }//foreach

        // if the concat flag is true
        if( $this->concat ){
            // join the array of minified css
            $css_concat = implode( PHP_EOL, $css_result );
            // save to file
            file_put_contents($this->output_path . '/all.min.css', $css_concat);
        }//if
        else{
            foreach ($css_result as $key => $css) {
                //remove '.css' to add '.min.css'
                $filename = basename( $this->files[$key], '.css' ) . '.min.css';
                // save to file
                file_put_contents($this->output_path . '/' . $filename, $css);
            }//for
        }//else

    }//processFiles
```

最后，我们的`fire`方法将只调用这两个方法:

```
public function fire(){
    $this->init();
    $this->processFiles();
}
```

**提示:**你也可以使用`call`方法运行一个外部命令。

```
$this->call('command:name', array('argument' => 'foo', '--option' => 'bar'));
```

为了测试我们的命令，我们将把一些 css 文件复制到我们的`public/css`目录中，然后运行命令。

```
php artisan cssmin 'public/css' 'public/css/style.css' 'public/css/responsive.css'

php artisan cssmin 'public/css' 'public/css/style.css' 'public/css/responsive.css' --comments --concat
```

第一个命令将在`public/css`目录下创建两个文件(`style.min.css`、`responsive.min.css`)。

因为我们使用了`--comments`和`--concat`标志，所以我们将得到一个名为`all.min.css`的文件，其中包含两个带有注释的文件。

我们的命令描述性不强，没有给出任何消息或通知！

### 提高指挥能力

在我们继续之前，在最终的 GitHub 存储库中，我将为我们的命令创建一个新的标签，这样您就可以切换和测试每个标签。

为了使命令稍微冗长一些，Laravel 为我们提供了一些输出函数:

```
$this->line("This is a simple line message");
$this->info("This is an info");
$this->comment("This is a comment");
$this->question("This is a question");
$this->error("This is an error");
```

这将输出:

![Output](img/13725df6988f99e0bf0a2feda4d58a1b.png)

除了显示消息之外，您还可以向用户询问信息，例如:

```
$confirm = $this->confirm("'style.min.css' already exists, overwrite?", false);
$filename = $this->ask("Save the file as?", 'all.min.css');
$choice = $this->choice(
                        'Please select a level of minication:', 
                        [ 'minify all', 'leave comments' ], 
                        'default value'
                        );

$password = $this->secret('Type your password to confirm:');
```

*   `confirm`方法有两个参数，一个问题消息和一个默认值，如果用户输入的不是`y/n`的话。

*   `ask`方法将要求用户输入，而不仅仅是`y/n`，如果留空，则返回默认值。

*   `choice`方法会给用户一个编号列表供选择，如果留空，则返回默认值。

*   `secret`方法将提示用户一个问题并隐藏输入，但是用户输入将被返回。

事实上，Laravel 只是让 Symfony 的控制台 API 变得更简单、更详细，如果你想深入了解的话，还有很多。

让我们的命令更加详细，并让用户了解所执行任务的最新情况。

```
private function processFiles(){
        $css_result = [];

        foreach ( $this->files as $file ) {
            $this->comment("'{$file}'");

            $this->info("Loading file");            
            //read file content
            $file_content = file_get_contents( $file );

            $this->info("minifying");
            //minify CSS and add it to the result array
            $css_result[] = $this->minify( $file_content, $this->comments );
        }//foreach

        if( $this->concat ){
            $this->comment("Concatenating into one file");

            $css_concat = implode( PHP_EOL, $css_result );

            $this->info("Saving to '{$this->output_path}/all.min.css'");
            file_put_contents($this->output_path . '/all.min.css', $css_concat);
        }//if
        else{
            foreach ($css_result as $key => $css) {
                //remove '.css' to add '.min.css'
                $filename = basename( $this->files[$key], '.css' ) . '.min.css';

                $this->comment("Saving '{$filename}'");
                file_put_contents($this->output_path . '/' . $filename, $css);
            }//for
        }//else

    }//processFiles
```

我们的函数现在打印一些有用的消息来跟踪正在发生的事情。

![command log](img/b78128a70d8abe2d65cba309e68c7457.png)

**注意:**这将在 GitHub 库上被标记为我们命令的`v2`。

当创建一个应用程序时，我们习惯于转储可用路线的列表(`php artisan routes`)。

![routes](img/374660992343b8eb87e1d1a870d174b3.png)

Symfony 提供了一个功能，可以让你轻松地打印这样的表格。查看[文档](http://symfony.com/doc/current/components/console/helpers/tablehelper.html)中的示例。接下来我们将看到如何使用一些 Symfony 控制台助手。

### 使用 Symfony 控制台助手

为了说明一些 Symfony 助手的用法，我们将使用 [Progress 助手](https://uploads.sitepoint.com/wp-content/uploads/2014/06/1402500040command_progressbar.png)来让用户了解最新的作业进度。

在我们的`init`方法结束时，我们将需要来自`HelperSet`的进度，然后启动我们的进度条。

```
$this->progressbar = $this->getHelperSet()->get('progress');
$this->progressbar->start($this->output, count($this->files) );
```

`start`方法接受两个参数，`$this->output`是来自 Symfony 控制台的一个`ConsoleOuput`实例。第二个参数是最大步数。

每次我们在我们的`processFiles`方法中处理一个文件时，我们将进度条向前推进一步，当工作完成时，我们将结束进度条并打印一条通知消息。

```
private function processFiles(){
        $css_result = [];

        foreach ( $this->files as $file ) {
            //read file content
            $file_content = file_get_contents( $file );
            //minify CSS and add it to the result array
            $css_result[] = $this->minify( $file_content, $this->comments );
            // sleep for one second to see the effect
            //sleep(1);
            $this->progressbar->advance();
        }//foreach

        if( $this->concat ){
            $css_concat = implode( PHP_EOL, $css_result );
            file_put_contents($this->output_path . '/all.min.css', $css_concat);
        }//if
        else{
            foreach ($css_result as $key => $css) {
                //remove '.css' to add '.min.css'
                $filename = basename( $this->files[$key], '.css' ) . '.min.css';

                file_put_contents($this->output_path . '/' . $filename, $css);
            }//for
        }//else

        $this->progressbar->finish();
        $this->info('Done');
    }//processFiles
```

您可以对多个文件尝试该命令，或者取消对`sleep`函数行的注释来查看实际效果。

![progressbar](img/36d946dc2656a966dc3204ac2429e78c.png)

**注意:**这个版本在最终版本中将被标记为`v3`。

## 结论

在本文中，我们学习了如何创建和扩展 Laravel 命令。Laravel 有很多你可以探索的内置命令，你也可以在 GitHub 上查看我们的[最终库](https://github.com/sitepoint-examples/cssmin-laravelCommand)来测试最终结果。有问题吗？评论？想看更多 Artisan 命令教程吗？让我们知道！

## 分享这篇文章