# 用 Grunt 编写一个非常棒的构建脚本

> 原文：<https://www.sitepoint.com/writing-awesome-build-script-grunt/>

Grunt 对于 web 开发来说是一个很棒的构建系统，但是设置起来很棘手。在本指南中，您将学习如何配置 Grunt 来构建一个现代化的 web 项目。完成后，您的 Gruntfile 将能够:

*   将文件从源目录复制到构建目录
*   移除生成文件
*   编译手写笔文件并为其添加供应商前缀
*   编译 CoffeeScript
*   缩小 CSS 和 JavaScript
*   编玉
*   修改源文件时自动构建它们
*   运行开发服务器

## 入门指南

如果你还没有这样做，安装 [Node.js](http://nodejs.org/) 和 [NPM](https://npmjs.org/) 。还需要通过运行`npm install -g grunt-cli`来安装 Grunt 命令行界面。这允许您从系统的任何地方运行`grunt`命令。

用下面的内容创建一个`package.json`。

```
json 
{
  "name": "grunt_tutorial",
  "description": "An example of how to set up Grunt for web development.",
  "author": "Landon Schropp (http://landonschropp.com)",
  "dependencies": {
    "grunt": "0.x.x",
    "grunt-autoprefixer": "0.2.x",
    "grunt-contrib-clean": "0.5.x",
    "grunt-contrib-coffee": "0.7.x",
    "grunt-contrib-connect": "0.4.x",
    "grunt-contrib-copy": "0.4.x",
    "grunt-contrib-cssmin": "0.6.x",
    "grunt-contrib-jade": "0.8.x",
    "grunt-contrib-jshint": "0.6.x",
    "grunt-contrib-stylus": "0.8.x",
    "grunt-contrib-uglify": "0.2.x",
    "grunt-contrib-watch": "0.5.x"
  },
  "engine": "node >= 0.10"
}
```

该文件将您的项目定义为 NPM 包，并声明项目的依赖项。每个依赖项都有一个版本号。例如，`grunt-contrib-copy: "0.4.x"`告诉 NPM 安装最新的 0.4 版本的`grunt-contrib-copy`包。在你的控制台中运行`npm install`来安装依赖项。

## 复制

一个好的构建脚本总是将源代码与构建文件分开。这种分离允许您在不影响源代码的情况下销毁构建，并防止您意外编辑构建。

首先，让 Grunt 将文件从一个`source`目录复制到一个`build`目录。创建一个`Gruntfile.js`文件，并将以下内容粘贴到其中:

```
javascript
module.exports = function(grunt) {

  // configure the tasks
  grunt.initConfig({

    copy: {
      build: {
        cwd: 'source',
        src: [ '**' ],
        dest: 'build',
        expand: true
      },
    },

  });

  // load the tasks
  grunt.loadNpmTasks('grunt-contrib-copy');

  // define the tasks
};
```

我们来分析一下。在 Node 中，当你`require`一个模块时，调用`modules.exports`函数并返回结果。通过在 Gruntfile 中设置`modules.exports`,您告诉 Node 返回一个定义 Grunt 配置的函数。`grunt.initConfig`是一个接受一个参数的方法:一个对象，它的属性配置单个的普通任务。

在 Grunt 配置中，您已经为一个`copy`任务添加了配置。这个任务有一个名为`build`的子任务。在 Grunt 中，一些被称为[多任务](http://gruntjs.com/creating-tasks#multi-tasks)的任务可以有几个子任务，这些子任务可以被单独调用。对于`copy`，你不需要这个特性，但是仍然要求至少有一个子任务。

在`build`子任务中是 Grunt 的[文件数组格式](http://gruntjs.com/configuring-tasks#files-array-format)。这种格式是 Grunt 允许您为任务提供源文件的方式之一。`cwd`指向源文件相对的目录，src 指定源文件。`'**'`是一个 [globbing 模式](http://gruntjs.com/configuring-tasks#globbing-patterns)，它告诉 Grunt 匹配任何文件。`dest`是 Grunt 输出任务结果的地方。您已经将它设置为`"build"`来告诉 grunt 将内容复制到构建目录。如果有`source/index.html`文件，这个配置会输出`build/index.html`。最后，将`expand`参数设置为`true`来启用所有这些选项。

`grunt.loadNpmTasks("grunt-contrib-copy");`告诉 Grunt 从`grunt-contrib-copy`包中加载任务。这给了我们一个`copy`命令，您可以通过在控制台中键入`grunt copy`来运行它。

## 干净的

现在您已经有了一个`build`目录，是时候编写一个任务来清除它了。在复制配置之后，添加以下内容:

```
javascript
clean: {
  build: {
    src: [ 'build' ]
  },
},
```

就像`copy`一样，您有一个带有任务配置的`clean`目标。将`clean`配置的`src`设置为`"build"`，删除`build`目录。

在`grunt.loadNpmTasks("grunt-contrib-copy");`之后，加载`clean`任务，这将允许你从控制台运行`grunt clean`。

```
javascript
grunt.loadNpmTasks('grunt-contrib-clean');
```

## 建设

如果您有一个在复制新的源文件之前删除旧版本的`build`任务，那不是很好吗？再加一个吧！

```
javascript
// define the tasks
grunt.registerTask(
  'build', 
  'Compiles all of the assets and copies the files to the build directory.', 
  [ 'clean', 'copy' ]
);
```

`registerTask`方法创建一个新任务。第一个参数`"build"`定义了任务的名称。第二个是任务描述。最后一个是将要运行的任务的数组。`build`任务运行`clean`任务，后面跟着`copy`任务。

## 唱针

Stylus 是一种可以编译成 CSS 的漂亮的语言。它从几个方面增强了 CSS，包括添加变量、嵌套和函数。

```
javascript
stylus: {
  build: {
    options: {
      linenos: true,
      compress: false
    },
    files: [{
      expand: true,
      cwd: 'source',
      src: [ '**/*.styl' ],
      dest: 'build',
      ext: '.css'
    }]
  }
},
```

这与其他任务配置略有不同。仍然有一个`build`子任务，但是它现在有两个属性:`options`和`files`。`options`指定我们希望任务如何运行。我们添加了两个选项:`compress`决定 CSS 输出是否应该被压缩，`linenos`添加源手写笔文件中选择器的行号。

`files`采用与之前相同的文件数组映射格式。这将在以`.styl`结尾的`source`目录中的所有文件上运行任务。`ext`将输出文件的扩展名改为`.css`。

现在`stylus`任务将 CSS 文件输出到`build`目录，没有理由再将手写笔文件复制到`build`目录。让我们修改`copy`配置来防止这种情况。

```
javascript
copy: {
  build: {
    cwd: 'source',
    src: [ '**', '!**/*.styl' ],
    dest: 'build',
    expand: true
  },
},
```

路径开头的`!`防止 grunt 包含匹配模式的文件。不要忘记将`"stylus"`添加到`build`任务中。

```
javascript
grunt.registerTask(
  'build', 
  'Compiles all of the assets and copies the files to the build directory.', 
  [ 'clean', 'copy', 'stylus' ]
);
```

## 自动贴合

[Autoprefixer](https://github.com/ai/autoprefixer) 是一个在 手写笔文件编译成 CSS 后，给 CSS3 属性 ***添加厂商前缀的插件。对于像 [Nib](http://visionmedia.github.io/nib/) 和 [Compass](http://compass-style.org/) 这样的库来说，它是一个很好的替代品。***

继续添加`autoprefixer`配置。

```
javascript
autoprefixer: {
  build: {
    expand: true,
    cwd: 'build',
    src: [ '**/*.css' ],
    dest: 'build'
  }
},
```

注意到一种模式？该配置与其他任务非常相似。一个显著的区别是`cwd`和`dest`都被设置为`"build"`。这使得`autoprefixer`将文件输出到它从中读取文件的同一个文件夹中，这将替换原始文件。

和以前一样，您还需要加载 Autoprefixer 任务。

```
javascript
grunt.loadNpmTasks('grunt-autoprefixer');
```

与其将所有的 CSS 任务都推到`build`中，不如为样式表创建一个新任务并添加到构建中。

```
javascript
// define the tasks
grunt.registerTask(
  'stylesheets', 
  'Compiles the stylesheets.', 
  [ 'stylus', 'autoprefixer' ]
);

grunt.registerTask(
  'build', 
  'Compiles all of the assets and copies the files to the build directory.', 
  [ 'clean', 'copy', 'stylesheets' ]
);
```

## CSS 缩小

向客户端传递大量庞大的 CSS 文件确实会降低网站的加载速度。幸运的是，`grunt-contrib-cssmin`包缩小了 CSS 文件，并将它们合并成一个文件。还是那句话，先说配置。

```
javascript
cssmin: {
  build: {
    files: {
      'build/application.css': [ 'build/**/*.css' ]
    }
  }
},
```

这个配置没有使用文件数组格式，而是使用 Grunt 的[文件对象格式](http://gruntjs.com/configuring-tasks#files-object-format)，它将几个文件映射到一个目的地。`build`目录中的所有 CSS 文件将被缩小并输出到`build/application.css`。

加载包并将 CSS 缩小添加到`stylesheets`任务中。

```
javascript
grunt.loadNpmTasks('grunt-contrib-cssmin');
```

```
javascript
grunt.registerTask(
  'stylesheets', 
  'Compiles the stylesheets.', 
  [ 'stylus', 'autoprefixer', 'cssmin' ]
);
```

## 咖啡脚本

CoffeeScript 是一种可以编译成 JavaScript 的奇妙语言。它有干净漂亮的语法，包含了类，隐藏了很多 JavaScript 丑陋的方面。

将 CoffeeScript 添加到项目中很容易！首先，添加配置。

```
javascript
coffee: {
  build: {
    expand: true,
    cwd: 'source',
    src: [ '**/*.coffee' ],
    dest: 'build',
    ext: '.js'
  }
},
```

这会拉入源 CoffeeScript 文件，将它们的扩展名改为`.js`，并将它们输出到`build`目录。接下来，加载`grunt-contrib-coffee`包。

```
javascript
grunt.loadNpmTasks('grunt-contrib-coffee');
```

添加一个`scripts`任务，并将其添加到`build`任务中。

```
javascript
grunt.registerTask(
  'scripts', 
  'Compiles the JavaScript files.', 
  [ 'coffee' ]
);

grunt.registerTask(
  'build', 
  'Compiles all of the assets and copies the files to the build directory.', 
  [ 'clean', 'copy', 'stylesheets', 'scripts' ]
);
```

同样，您需要向`copy`添加一个异常，这样 CoffeeScript 文件就不会被复制到`build`目录中。

```
javascript
copy: {
  build: {
    cwd: 'source',
    src: [ '**', '!**/*.styl', '!**/*.coffee' ],
    dest: 'build',
    expand: true
  },
},
```

## 把…弄成难看ˌ将…丑化ˌ糟蹋

像`cssmin`， [UglifyJS](https://github.com/mishoo/UglifyJS) 一样，将 JavaScript 文件缩小并组合成一个文件。配置如下:

```
javascript
uglify: {
  build: {
    options: {
      mangle: false
    },
    files: {
      'build/application.js': [ 'build/**/*.js' ]
    }
  }
},
```

默认情况下，UglifyJS 会将脚本中的变量和函数的名称替换为较短的名称。如果您的项目代码是自包含的，这很方便，但是如果它与另一个项目共享，这可能会导致问题。将 mangle 设置为`false`会关闭此行为。

像`cssmin`任务一样，这个任务也使用 files 对象格式。

加载包并将`"uglify"`添加到`scripts`任务中。

```
javascript
grunt.loadNpmTasks('grunt-contrib-uglify');
```

```
javascript
grunt.registerTask(
  'scripts', 
  'Compiles the JavaScript files.', 
  [ 'coffee', 'uglify' ]
);
```

## 清理

当你运行`grunt build`时，除了`build/application.css`和`build/application.js`，其他所有的 CSS 和 JavaScript 文件都挂在`build`目录下。因为您不需要它们，所以添加子任务将它们移除到`clean`配置中。

```
javascript
clean: {
  build: {
    src: [ 'build' ]
  },
  stylesheets: {
    src: [ 'build/**/*.css', '!build/application.css' ]
  },
  scripts: {
    src: [ 'build/**/*.js', '!build/application.js' ]
  },
},
```

当运行一个任务时，如果你没有指定一个子任务，Grunt 会运行所有的子任务。如果你从控制台运行`grunt clean`，它将运行`clean:build`、`clean:stylesheets`和`clean:scripts`。这不是问题，因为如果`clean`任务不能删除一个文件，它就会忽略它。

注意`build/application.css`和`build/application.js`是如何从`stylesheets`和`scripts`子任务中被排除的。你不想在你努力工作之后删除那些假的！

更新任务以使用适当的子任务。

```
javascript
// define the tasks
grunt.registerTask(
  'stylesheets', 
  'Compiles the stylesheets.', 
  [ 'stylus', 'autoprefixer', 'cssmin', 'clean:stylesheets' ]
);

grunt.registerTask(
  'scripts', 
  'Compiles the JavaScript files.', 
  [ 'coffee', 'uglify', 'clean:scripts' ]
);

grunt.registerTask(
  'build', 
  'Compiles all of the assets and copies the files to the build directory.', 
  [ 'clean:build', 'copy', 'stylesheets', 'scripts' ]
);
```

## 翡翠

Jade 是一种模板语言，让编写 HTML 变得有趣。使用`grunt-contrib-jade`包将 Jade 添加到您的项目中。

```
javascript
jade: {
  compile: {
    options: {
      data: {}
    },
    files: [{
      expand: true,
      cwd: 'source',
      src: [ '**/*.jade' ],
      dest: 'build',
      ext: '.html'
    }]
  }
},
```

像`stylus`和`coffee`任务一样，`jade`使用文件数组格式进行配置。注意到`options`里面的`data`物体了吗？在编译 Jade 文件时，这个对象被传递给每个模板。这对于创建单独的开发和生产版本或者生成动态内容是很方便的。

和前面一样，您需要为`copy`任务添加一个异常，以防止 Jade 文件被复制。

```
javascript
copy: {
  build: {
    cwd: 'source',
    src: [ '**', '!**/*.styl', '!**/*.coffee', '!**/*.jade' ],
    dest: 'build',
    expand: true
  },
},
```

不要忘记加载`grunt-contrib-jade`并将其添加到“构建”中。

```
javascript
grunt.loadNpmTasks('grunt-contrib-jade');
```

```
javascript
grunt.registerTask(
  'build', 
  'Compiles all of the assets and copies the files to the build directory.', 
  [ 'clean:build', 'copy', 'stylesheets', 'scripts', 'jade' ]
);
```

## 看

你的 Gruntfile 真的开始发光了，但是如果你不需要每次改变都运行`grunt build`不是很好吗？有了`grunt-contrib-watch`，就不需要了！让我们配置一个任务，它将观察您的源代码的变化并自动构建它们。

```
javascript
watch: {
  stylesheets: {
    files: 'source/**/*.styl',
    tasks: [ 'stylesheets' ]
  },
  scripts: {
    files: 'source/**/*.coffee',
    tasks: [ 'scripts' ]
  },
  jade: {
    files: 'source/**/*.jade',
    tasks: [ 'jade' ]
  },
  copy: {
    files: [ 'source/**', '!source/**/*.styl', '!source/**/*.coffee', '!source/**/*.jade' ],
    tasks: [ 'copy' ]
  }
},
```

`stylesheets`、`scripts`和`jade`子任务观察手写笔、CoffeeScript 和 Jade 文件的变化，并运行它们各自的任务。`copy`任务监视应用程序中所有剩余的文件，并将它们复制到构建目录中。

同样，您需要加载这个繁重的任务。

```
javascipt
grunt.loadNpmTasks('grunt-contrib-watch');
```

## 开发服务器

没有开发服务器，任何 web 开发环境都是不完整的。`grunt-contrib-connect`包是一个全功能的静态文件服务器，非常适合您的项目。

```
javascript
connect: {
  server: {
    options: {
      port: 4000,
      base: 'build',
      hostname: '*'
    }
  }
}
```

您已经将服务器配置为在端口 4000 上托管`build`目录。默认情况下，Connect 将只在`localhost`上托管站点，这限制了您在计算机之外访问服务器。将`hostname`设置为`"*"`允许从任何地方访问服务器。

和以前一样，您还需要加载 NPM 任务。

```
javascript
grunt.loadNpmTasks('grunt-contrib-connect');
```

如果您尝试从命令行运行`grunt connect`，服务器会运行，然后立即停止。这是因为默认情况下，grunt connect 任务不会无限期运行。您将在下一节学习如何解决这个问题。

## 默认

如果你有一个将所有其他任务结合在一起的任务，那不是很好吗？一个`default`任务非常适合这个。

```
javascript
grunt.registerTask(
  'default', 
  'Watches the project for changes, automatically builds them and runs a server.', 
  [ 'build', 'connect', 'watch' ]
);
```

任务运行“构建”来创建初始构建。然后，它启动连接服务器。最后，它运行`watch`来观察文件的变化并构建它们。由于`watch`一直运行到它被杀死，连接服务器将无限期运行。在你的控制台中运行`grunt`并导航到 [http://localhost:4000](http://localhost:4000) 查看你的项目！

## 结论

我们已经在本教程中介绍了很多，还有很多咕噜可以做的。要获得 Grunt 可用插件的完整列表，请查看 Grunt 插件网站。开心的哼哼！

## 分享这篇文章