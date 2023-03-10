# Ruby 的路径名 API

> 原文：<https://www.sitepoint.com/rubys-pathname-api/>

[![Pathname docs](img/f8c92456f8e3ddf9a5e48f5bfe464f37.png)](http://www.ruby-doc.org/stdlib-1.9.3/libdoc/pathname/rdoc/Pathname.html)

Ruby 的标准库是有用类的宝库，`Pathname`类当然是其中之一。在 Ruby 1.9 中引入的，`Pathname`表示文件系统上的路径，提供了对分散在少数其他类中的功能的方便访问，如`File`、`FileTest`和`Dir`。

既然 Ruby 1.8 的生命已经[正式走到了尽头](https://www.ruby-lang.org/en/news/2013/06/30/we-retire-1-8-7/)，1.9 的[也即将迎来它的五岁生日](https://www.ruby-lang.org/en/news/2009/01/30/ruby-1-9-1-released/)，是时候放弃传统支持，拥抱`Pathname`所提供的优雅和易用性了。

为了说明为什么`Pathname`是一个非常需要的附加功能，让我们考虑一下操纵路径信息的更传统的方式。这是我们在 1.8 天内的工作方式:

```
CONFIG_PATH = File.join(File.dirname(__FILE__), '..', 'config.yml')
```

注意类方法的使用，这在一切都是对象的语言中很少见。但是有什么选择呢？面向对象要做的事情是将功能移近它所操作的数据。在这种情况下，我们只是简单地操作字符串，但是将`join`和`dirname`添加到`String`中并没有什么意义。对于这种特定的功能来说，字符串是一种过于普通的数据类型。

输入`Pathname`:

```
CONFIG_PATH = Pathname.new(__FILE__).dirname.join('..', 'config.yml')
```

通过将字符串包装在一个`Pathname`对象中，我们得到了一个用于操作路径的干净的 API。这段代码看起来已经很像惯用的 Ruby 了。与 Ruby 的普通字符串的一个重要区别是`Pathname`实例是不可变的。没有方法可以改变`Pathname`实例本身。相反，所有像`dirname`、`join`或`basename`这样的方法都返回一个新的`Pathname`实例，保持现有实例不变。

## 路径名 API

`Pathname`上的方法大致分为两类:要么它们简单地操纵路径字符串而不访问文件系统，要么它们调用操作系统对文件或目录本身执行检查和操作。关于八十多种方法的完整列表，你可以查看官方的[路径名 API 文档](http://ruby-doc.org/stdlib-2.1.0/libdoc/pathname/rdoc/Pathname.html)。值得注意的是，这些文件也可以从终端通过`ri Pathname`获得，或者通过`help 'Pathname'`在 irb 中获得。

让我们来看看一些你可能会觉得有用的方法。

### 文件类型和权限检查

```
pn = Pathname.new('/usr/bin/ruby')
pn.file?       # => true
pn.directory?  # => false

pn.absolute?   # => true
pn.relative?   # => false

pn.executable? # => true
pn.readable?   # => true
pn.writable?   # => false

pn.root?       # => false
```

其中大多数与`FileTest`中的方法相对应

### 文件系统导航和查询

```
pn = Pathname.getwd # current working directory
pn.children   # => [ #<Pathname...>, ... ]
pn.each_child {|ch| ... }
pn = pn.parent

Pathname.glob('**/*.rb') # like Dir['**/*.rb'] but returns Pathnames
```

### 路径名操作

```
pn = Pathname.new('lib/mylib/awesome.rb')
pn.dirname                   # => #<Pathname:lib/mylib>
pn.basename                  # => #<Pathname:awesome.rb>
pn.extname                   # => ".rb"
pn.expand_path('/home/arne') # => #<Pathname:/home/arne/lib/mylib/awesome.rb>
```

### 使用实际文件

```
pn = Pathname.new(ENV['HOME']).join('.bashrc')
pn.size
pn.read
pn.open {|io| ... }
pn.each_line {|line| ... }
pn.rename('/tmp/foo')
pn.delete
```

## `Pathname()`和`to_path`

创建`Pathname`实例有两种方法:通过上面的显式构造，或者通过`Pathname()`转换方法。虽然大写的标识符通常是为类和常量保留的，但是 Ruby 有许多内置的方法可以将任意值转换成特定的类型，这是根据它们转换成的类来命名的。

```
Array(1)                  # => [1]
Array(nil)                # => []
Array([1,2])              # => [1,2]
String(7)                 # => "7"
URI("http://example.com") # => #<URI::HTTP:0x1c0e URL:http://example.com>
```

这些与许多对象可以实现的转换挂钩密切相关，比如`to_ary`或`to_str`。同样，对象可以实现`to_path`来指示它们如何被转换成路径名。为了提高效率，pathname 构造函数是用 C 实现的，但是它大概会被翻译成 Ruby 的样子:

```
class Pathname
  def initialize(path)
    path = path.to_path if path.respond_to? :to_path
    @path = String(path)
  end
end
```

假设您正在编写一些脚本来帮助您管理您的音乐收藏。一个`AudioFile`可能看起来像这样:

```
class AudioFile
  def initialize(path)
    @path = path
  end

  def to_path
    @path
  end

  # ...
end
```

许多内置方法会将它们的文件名参数包装在对`Pathname()`的调用中，因此您同样可以传入您的`AudioFile`实例。

```
af = AudioFile.new('...')
File.open(af) do |io|
  #...
end
```

编写库代码时，重复使用这种模式以获得最大的灵活性:

```
def read_configuration(config_file)
  parse_configuration(Pathname(config_file).read)
end
```

现在这段代码的调用者可以作为`config_file`传入

*   一根绳子
*   路径名
*   响应`to_path`的对象
*   任何可以用`String()`转换的对象

## 构建音乐收藏浏览器

为了展示使用`Pathname`是多么强大和直观，我们将编写一个小 API 来浏览我们的音乐收藏。这些文件由按每个专辑的目录分组的歌曲组成，这些目录又按艺术家分组。比如:`~/Music/Arsenal/Oyebo Soul/09_How Come.flacc`。

我们的目标是一个易于使用的 API，如下所示:

```
mc = MusicCollection.new('~/Music') # => #<MusicCollection 'Music'>
mc.artists.first                    # => #<Artist 'Arsenal'>
mc.artists.first.albums.first       # => #<Album 'Oyebo Soul'>
mc.songs.count                      # => 120
mc.songs.first.path                 # => #<Pathname: ...>
mc.songs.first.play
```

`Song`类本质上是我们上面的`AudioFile`类:

```
class Song
  attr_reader :path
  alias to_path path

  def initialize(path)
    @path = Pathname(path).expand_path
  end

  def name
    String(path.basename(path.extname))
  end

  def play(options = {})
    exec(options.fetch(:player, 'mplayer'), String(path))
    # OS X users can use player: '/usr/bin/afplay'
  end
end
```

我们立即调用`expand_path`来规范化我们的输入。这将把`~`扩展到用户的主目录，并将解析任何相关的文件名(比如`.`和`..`)。这样，我们就一定会有一个绝对的文件名。

现在我们需要为`Album`、`Artist`以及最后的`MusicCollection`本身添加类。由于所有这些都是围绕着`Pathname`的包装类，我们可以将公共管道拉进一个基类。

```
class PathnameWrapper
  attr_reader :path
  alias to_path path

  def initialize(path)
    @path = Pathname(path).expand_path
  end

  def name
    # ...
  end

  def to_s
    # ...
  end
end

class Song < PathnameWrapper
  def play
    # ...
  end
end
```

现在我们可以添加一个`Album`并提供来回导航方式。我们还将在基类中添加`files`和`directories`助手，以及一个`to_proc`类方法。当用`&`将一个对象作为一个块传入时，Ruby 将使用`to_proc`为该块寻找一个实现。通过这种方式，我们可以简化集合中所有元素的类型转换。

```
class PathnameWrapper
  # ...

  def directories
    path.children.select(&:directory?)
  end

  def files
    path.children.select(&:file?)
  end

  def self.to_proc
    ->(path) { new(path) }
  end
end

class Album < PathnameWrapper
  EXTENSIONS = %w[.flacc .m4a .mp3 .ogg .wav]

  def songs
    files.select do |file|
      EXTENSIONS.any? {|ext| ext == file.extname }
    end.map(&Song)
  end
end

class Song < PathnameWrapper
  # ...

  def album
    Album.new(path.dirname)
  end
end
```

总结一下，我们引入`Artist`和`MusicCollection`。

```
class MusicCollection < PathnameWrapper
  def artists
    directories.map(&Artist)
  end

  def albums
    artists.flat_map(&:albums)
  end

  def songs
    albums.flat_map(&:songs)
  end
end

class Artist < PathnameWrapper
  def albums
    directories.map(&Album)
  end
end

class Album < PathnameWrapper
  # ...

  def artist
    Artist.new(path.dirname)
  end
end

class Song < PathnameWrapper
  # ...

  def artist
    album.artist
  end
end
```

现在我们可以在歌曲、专辑和艺术家之间来回浏览。

这结束了我们的`MusicCollection` API 的基础，以及我们对`Pathname`类的介绍。很好地掌握 Ruby 的标准库有助于使您的代码更加优雅、正确和简洁。这里我们放大了一个小组件，突出了它的许多用途。向前迈进，明智地使用`Pathname`。

## 分享这篇文章