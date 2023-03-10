# 如何向吞咽任务传递命令行参数

> 原文：<https://www.sitepoint.com/pass-parameters-gulp-tasks/>

我是 Gulp.js 的超级粉丝，最近的调查显示几乎有 44%的前端开发人员在使用 Gulp 任务(T3)。

Gulp 的简单是它最吸引人的特点之一。你在你的`gulpfile.js`中写一个任务函数:

```
gulp.task('doSomething', () => {

  // do something

}); 
```

然后用`gulp doSomething`从命令行执行该任务。任务可以是基本的，也可以是复杂的，并且包含更多的子任务。

然而，不可能在命令行上传递该任务可以使用的参数，例如

```
gulp doSomething --option1 "my string" --option2 123 --option3 
```

*(其中`option3`等同于`true` )*

参数将被传递给 Gulp 应用程序本身，而不是您的任务。Gulp 对这些值一无所知，因此它们在`gulpfile.js`中不可用，也不能在您的任务函数中检查或使用。

## 吞咽任务需要论据吗？

通常不会——否则，向任务传递参数的功能在很多年前就应该添加了！Gulp 任务是用 JavaScript 编写的，因此您可以在代码中设置默认值。

您还可以分析环境变量，例如 **NODE_ENV** 。例如，您可以检查该值是否在实时服务器上设置为`production`或类似的值。然后，该设置可用于确定在任务运行时是否缩小 JavaScript 源文件，例如

```
// is this a development build?
const devBuild = ((process.env.NODE_ENV || 'development').trim().toLowerCase() === 'development');

// Gulp plugins
const
  stripdebug = require('gulp-strip-debug'),  // remove debugging code
  uglify = require('gulp-uglify');           // minify

// build JavaScript
gulp.task('js', () => {

  let jsbuild = gulp.src('src/js/*')
    .pipe(some-plugin1())
    .pipe(some-plugin2());

  // production server tasks
  if (!devBuild) {
    jsbuild = jsbuild
      .pipe(stripdebug())
      .pipe(uglify());
  }

  return jsbuild.pipe(gulp.dest('build/js/'));

}); 
```

在运行`gulp js`任务之前，您现在可以在 Linux/Mac 上设置`export NODE_ENV=production`或者在 Windows 上设置`set NODE_ENV=production`。然后，它会在缩小您的 JavaScript 文件之前删除`console.log`和`debugger`语句。

最后，如果您想让一个任务做一些稍微不同的事情，您可以创建一个新任务。任务可以链接在一起，根据需要按顺序运行，例如:

```
gulp.task('doSomething1', () => {

  return gulp.src('src/*')
    .pipe(some-plugin1())
    .pipe(gulp.dest('build/'));

});

// run doSomething1 first
gulp.task('doSomething2', [doSomething1], () => {

  // do something else
  return gulp.src('src/*')
    .pipe(some-plugin2())
    .pipe(gulp.dest('build/'));

}); 
```

运行`gulp doSomething1`将执行第一个任务。运行`gulp doSomething2`将按顺序执行这两个任务，因为`doSomething1`被定义为任务名称后面可选数组中的一个依赖项。

## 我们应该考虑争论吗？

当有更好的选择时，应该避免争论。在 Gulp 的下一个版本中，您的`--option1`参数可能会成为一个有效的命令行选项，并产生意想不到的后果。

也就是说，总会有边缘情况…

### 1.密码和安全性

通常应该避免将 id 和密码等凭证硬编码到`gulpfile.js`中。考虑以下任务，该任务使用 [vinyl-ftp](https://www.npmjs.com/package/vinyl-ftp) 插件通过 FTP 将文件部署到服务器:

```
gulp.task('deploy', () => {

  let
    ftp = require('vinyl-ftp'),
    conn = ftp.create({
      host      : 'mysite.com',
      user      : 'myuserid',
      password  : 'mypassword',
      parallel  : 5
    }),
    glob = [
      'build/**/*'
    ],
    src = {
      base      : 'build/',
      buffer    : false
    },
    remotePath = '/public_html/';

  return gulp.src(glob, src)
    .pipe(conn.newerOrDifferentSize(remotePath))
    .pipe(conn.dest(remotePath));

}); 
```

(不可否认，FTP 不是一个很好的部署方法，但它仍然被许多开发者使用，并且可能是一些主机上的唯一选择。)

这种方法有几个问题:

1.  FTP 主机、用户 ID、密码和路径被硬编码到文件中。如果代码存储在公共的 GitHub 存储库中，任何人都可以查看、克隆和运行，这将导致安全问题。
2.  任何开发者都可以在任何时间从任何设备上运行`gulp deploy`。对于想要控制何时进行部署的大型团队来说，这不太可能是可取的。
3.  如果凭证发生变化，您必须手动更新`gulpfile.js`以确保部署任务仍然有效。

### 2.不同的源、生成或任务位置

Gulp 可以用于除了典型的网站任务之外的其他事情。例如，您可能有一些常规任务，如擦除文件夹、创建数据库、传输文件等。像数据库或文件夹名这样的硬编码会降低这些任务的有用性。

### 3.复杂的任务

想象一下一个涉及几十个插件的复杂任务。如果将其分成多个子任务不切实际，那么在运行任务之前不直接编辑`gulpfile.js`就很难添加配置选项。

你也许能想到更进一步的边缘案例*(欢迎评论！)*

## 如何向 Gulp.js 任务传递参数

Node.js 中的`process.argv`属性返回一个包含流程、脚本和所有命令行参数的数组。例如，`gulp task1 --a 123 --b "my string" --c`返回下面的数组*(值可能因操作系统和设置而异)*:

```
[
'/usr/bin/nodejs',
'/home/user/.node_modules_global/bin/gulp',
'task1',
'--a',
'123',
'--b',
'my string',
'--c'
] 
```

这个数组可以在`gulpfile.js`中解析。下面的代码创建了一个名为`arg`的包含参数值的对象:

```
// fetch command line arguments
const arg = (argList => {

  let arg = {}, a, opt, thisOpt, curOpt;
  for (a = 0; a < argList.length; a++) {

    thisOpt = argList[a].trim();
    opt = thisOpt.replace(/^\-+/, '');

    if (opt === thisOpt) {

      // argument value
      if (curOpt) arg[curOpt] = opt;
      curOpt = null;

    }
    else {

      // argument name
      curOpt = opt;
      arg[curOpt] = true;

    }

  }

  return arg;

})(process.argv); 
```

该函数在`process.argv`数组中循环。当它遇到一个前面有一个或多个破折号的值时，它会在设置为`true`的`arg`对象中创建一个新的命名值。当遇到不带破折号的值时，它会将以前命名的值(如果可用)设置为该字符串。

当我们运行`gulp task1 --a 123 --b "my string" --c`时，`arg`对象被设置为:

```
{
  "a": "123",
  "b": "my string",
  "c": true
} 
```

因此，我们可以根据需要检查和使用这些值。

假设`arg`被设置在`gulpfile.js`的顶部，我们可以重写我们的 FTP 部署任务，这样我们就可以通过:

*   作为`--user`或`--u`参数的用户 ID
*   作为`--password`或`--p`参数的密码

```
gulp.task('deploy', () => {

  let
    ftp = require('vinyl-ftp'),
    conn = ftp.create({
      host      : 'mysite.com',
      user      : arg.user || arg.u,      // command line option
      password  : arg.password || arg.p,  // command line option
      parallel  : 5
    }),
    glob = [
      'build/**/*'
    ],
    src = {
      base      : 'build/',
      buffer    : false
    },
    remotePath = '/public_html/';

  return gulp.src(glob, src)
    .pipe(conn.newerOrDifferentSize(remotePath))
    .pipe(conn.dest(remotePath));

}); 
```

只有当我们使用适当的 FTP 凭证运行任务时，部署才会发生，例如

```
gulp deploy --u myuserid --p mypassword 
```

## 包扎

正如我们所看到的，通过一点点定制代码，就可以将参数传入吞咽任务。尽管您的任务通常不需要接收参数，但我们看到在某些情况下它是有用的。这绝对是您工具箱中的一项好技术。

参数解析代码可用于任何 Node.js 命令行过程。然而，[指挥官](https://www.npmjs.com/package/commander)模块提供了相当多的复杂性，如果你在非吞咽项目中需要它的话。

我希望你觉得这是有用的。当然，仅仅因为您可以传递参数来吞咽任务，并不意味着您*应该这样做！*如果你对此有任何更好的使用案例，请在评论中告诉我。

*这篇文章由[蒂姆·塞韦里安](https://www.sitepoint.com/author/tseverien/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章