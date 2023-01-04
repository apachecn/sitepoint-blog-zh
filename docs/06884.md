# 用 Ruby: FFI 检测人脸

> 原文：<https://www.sitepoint.com/detecting-faces-with-ruby-ffi-in-a-nutshell/>

在过去的几年里，我变得非常喜欢 Ruby。不用太在意类型转换或内存管理，就能简单地完成工作，真是令人耳目一新。语言的强大表现力，整个 Gem 系统的易用性，以及一个很好的交互 shell，真的会让你感觉像在家里一样。既然你正在读 RubySource.com T2，你很可能已经有了同样的感觉。

但是 Ruby 在很多领域都落后了。Rails 给了 Ruby 很多正面的宣传(和一些负面的宣传),但也塑造了 Ruby 的生态系统，将它强力推向 web 开发。这可以被看作是福也可以被看作是祸。
一方面，Ruby 在 web 领域确实很棒。另一方面，在科学编程或游戏开发等领域，它不是 Python 等语言的有力竞争对手。虽然，我很肯定这种情况会慢慢改变，但现在有一些选项可供你选择:

*   [Swig](http://www.swig.org) 包装 C/C++接口
*   [RubyInline](https://github.com/seattlerb/rubyinline) 用于在 Ruby 中内嵌外来代码
*   [C/C++扩展](https://www.sitepoint.com/extending-ruby-with-c/)扩展 Ruby
*   [生成](http://apidock.com/ruby/Kernel/spawn)调用程序目录
*   在 Ruby 中加载库并绑定。这就是我将在本文中讨论的内容。

但是首先让我来介绍一下 FFI 的优点和缺点，以帮助您决定它是否是适合您工作的工具。

## 为什么你应该考虑使用 FFI

与用 C/C++编写扩展或在 subshell 中调用程序相比，FFI 有一些好处:

*   有了 FFI，您通常甚至不必编写 C/C++代码。调用维护良好的库中已经可用的方法可以真正节省时间。
*   FFI 在任何 Ruby 环境下都差不多，可能是 [MRI](http://www.ruby-lang.org) 、 [Rubinius](http://rubini.us) 甚至是 [JRuby](http://jruby.org) 都没有修改。
*   这对您的最终用户来说更容易。他们不需要开发头文件或 C/C++编译器。

## 什么时候 FFI 可能不是合适的工具

*   您只是在开发一个小脚本，它需要在您的系统上 grep 程序的一些输出。无论如何，只要生成一个子进程，像在 shell 中一样直接调用该程序，然后 grep 输出。当然，这有点不方便，但是当它为您节省大量时间并且不应该分发给成千上万的用户时，为什么不呢？但是要非常小心用户提交的参数！
*   您试图包装的库利用了许多编译时或预处理器特性。你也许可以在 Ruby 中实现其中的一些，但是有时候这违背了最初的目的。
*   如果您需要编写大量定制的 C/C++代码，并且每一毫秒都很重要，那么您可能想多做一点，编写一个 C/C++扩展，尽管创建您自己的库并连接这个库可能仍然是一个更好的选择。稍后我会告诉你怎么做。
*   你很难使用带有复杂回调接口的 FFI。FFI 支持回调，但并不总是容易实现。
*   大多数 FFI 系统看不到使用`#define`创建的常量。你可以很容易地在 Ruby 中重新定义它们，但是这可能是一项乏味的工作，如果它们在较新版本的库中发生了变化，你也必须改变你的代码。

## 基本示例

没有什么比一些简短但有用的例子更能帮助你对一项技术有所了解。

也许我们想使用 like to 的一个函数。可能需要检索当前进程的进程 ID。
现在，当你熟悉 Ruby 的时候，你会知道有一个方便的全局变量`$$`可以给你这个答案。为了这个简单的例子，让我们绕道通过 FFI 和 [LIBC 的“getpid”函数](http://www.gnu.org/software/libc/manual/html_mono/libc.html#Process-Identification)来做:

```
require 'ffi'

module MyLibcWrapper
  extend FFI::Library
  ffi_lib FFI::Library::LIBC
  attach_function :getpid, [], :int
end

puts MyLibcWrapper.getpid
puts $$
```

首先，我们必须要求 FFI Gem(如果你运行的是旧的 Ruby 版本，可能之前还有 rubygems)。然后我们创建一个扩展了`FFI::Library`模块的模块。现在，我们可以使用这个模块中的方法绑定到 LIBC 库，最后将 getpid 函数附加到我们的模块中。请注意，`attach_function`的参数是一个符号(表示外部库中函数的名称)，然后是该函数的参数类型数组(getpid
不带参数)，最后是该函数的返回类型(int)。
现在该函数可以作为我们模块的静态函数来访问。正如您希望看到的(记住`gem install ffi`)当您运行代码时，两种方法返回相同的进程 ID。

## 高级示例

但是一个更复杂的例子呢？毕竟我们在这里讨论的是 C/C++，所以很有可能你不得不使用某种结构并将指针传递给内存地址。也许你需要弄清楚你的程序运行在什么系统上。幸运的是，有另一个 [LIBC 函数“uname”](http://www.gnu.org/software/libc/manual/html_mono/libc.html#Platform-Type)提供了这种信息，所以让我们包装一下:

```
require 'ffi'

module MyLibcWrapper
  extend FFI::Library
  ffi_lib FFI::Library::LIBC

  # define a FFI Struct to hold the data that we can retrieve
  class UTSName < FFI::Struct
    layout :sysname   , [:char, 65],
           :nodename  , [:char, 65],
           :release   , [:char, 65],
           :version   , [:char, 65],
           :machine   , [:char, 65],
           :domainname, [:char, 65]

    def to_hash
      Hash[members.zip values.map(&:to_s)]
    end
  end

  # takes a pointer, returns an integer
  attach_function :uname, [:pointer], :int

  def self.uname_info
    uts = UTSName.new # create a place in memory to hold the data
    raise 'uname info unavailable' if uname(uts) != 0 # retrieve data
    uts.to_hash
  end
end

puts MyLibcWrapper.uname_info
# => {:sysname=>"Linux", :nodename=>"picard", :release=>"3.0.0-32-generic", :version=>"#50-Ubuntu SMP Thu Feb 28 22:32:30 UTC 2013", :machine=>"x86_64", :domainname=>"(none)"}
```

我将对本机函数的调用包装在 Ruby 方法中，因为作为 Ruby 开发人员，我们更习惯于 hashmaps 而不是 structs，我认为将这种转换隐藏在包装器模块中会很好。

## 简单的自定义示例

到现在为止，你可能还记得你几年前写的一个很酷的 C/C++程序，它能在一瞬间完成这个令人敬畏的事情，你真的想在你最新的 Ruby web 服务中使用它来创建下一个颠覆性的杀手级应用程序！

让我带你看一下把你自己的 C/C++程序变成共享库的过程。

因为这个例子只是为了教你如何把你的程序变成一个共享库，所以我决定让它非常简单。假设您想要计算阶乘，那么您的程序可能如下所示:

```
extern "C" unsigned long factorial(int n); //offer a C-compatible interface

unsigned long factorial(int n){
  unsigned long f = 1;
  for (int c=1; c<=n; c++) f *= c;
  return f;
}
```

通常，方法签名在单独的头文件中，但为了简单起见，我跳过了这一步。还要注意，您不需要创建 main 方法，因为我们正在编译一个库。

假设你把你的源文件命名为`factorial.c`，你可以像这样[编译](https://help.ubuntu.com/community/CompilingEasyHowTo):

```
g++ -shared -fPIC -Wall -o libfactorial.so factorial.c
```

但是，您可以添加一个 main 方法并编译它两次:一次使用，一次不使用-shared 标志。这是一个简单的方法来检查你的库，而不需要把它嵌入到某个地方。

现在是红宝石部分。我们已经知道了模式:

```
require 'ffi'

module MyAwesomeLib
  extend FFI::Library
  ffi_lib './libfactorial.so' # load library from the same folder
  # this time we take an integer and return an unsigned integer
  attach_function :factorial, [:int], :uint
end

puts MyAwesomeLib.factorial(6)
# => 720
```

这很简单，对吧？现在，您已经拥有了将 Ruby 应用程序的某些部分外包到一个共享的 C/C++库中所需的所有工具，并从两种语言的最佳部分中获益。

## 毫秒内检测人脸

现在，您已经了解了在 Ruby 中编写自己的库并使用它们的基本知识，我想兑现我在标题中做出的承诺。但是为什么要人脸检测呢？一方面，人脸检测是一个有趣的话题，有大量的应用程序使用它，可能你甚至不知道它。从数码相机上的人像向导到智能手机，再到安全摄像头后运行的跟踪程序，再到浏览器中运行的增强现实应用程序。

另一方面，我最近在研究一个非常相似的问题。我必须在 Rails 应用程序中检测上传图像中的某些模式，但由于人脸检测更容易被更多的受众接受，所以我选择了这个作为例子。

不幸的是，人脸检测可能是一个非常复杂的问题，尽管在较新的 Ruby 版本中，它甚至可能以可接受的速度(在一些非实时设置中)可行，但毫无疑问，在全球最常用的计算机视觉库 [OpenCV](http://opencv.org) 的帮助下，这项任务将更加轻松。所以让我们开始吧。

不过你需要[安装 OpenCV](https://help.ubuntu.com/community/OpenCV) ，几乎每个平台都有包和说明。此外，您将需要文件`lbpcascade_frontalface.xml`。这个文件是 debian 包`opencv-doc`的一部分，可以在`/usr/share/doc/opencv-doc/examples/lbpcascades/lbpcascade_frontalface.xml.gz`(在 Ubuntu 上)或者[和 Google](https://www.google.com/search?q=lbpcascade_frontalface.xml+filetype:xml) 中找到。

一旦您获得了这些必需品，您就可以创建您的源文件`faces.cpp`:

```
#include <opencv2/imgproc/imgproc.hpp>
#include <opencv2/objdetect/objdetect.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <iostream>
#include <stdio.h>
#include <unistd.h>
using namespace std;
using namespace cv;
char buf[4096];
extern "C" char* detect_faces(char* input_file, char* output_file);

int main(int argc, char** argv) {
  if(argc<2){
    fprintf(stderr, "usage:n%s <image>n%s <image> <outimg>n", argv[0], argv[0]);
    exit(-1);
  }
  printf("%s", detect_faces(argv[1], argc<3 ? NULL : argv[2]));
  exit(0);
}

char* detect_faces(char* input_file, char* output_file) {
  CascadeClassifier cascade;
  if(!cascade.load("lbpcascade_frontalface.xml")) exit(-2); //load classifier cascade
  Mat imgbw, image = imread((string)input_file); //read image
  if(image.empty()) exit(-3);
  cvtColor(image, imgbw, CV_BGR2GRAY); //create a grayscale copy
  equalizeHist(imgbw, imgbw); //apply histogram equalization
  vector<Rect> faces;
  cascade.detectMultiScale(imgbw, faces, 1.2, 2); //detect faces
  for(unsigned int i = 0; i < faces.size(); i++){
    Rect f = faces[i];
    //draw rectangles on the image where faces were detected
    rectangle(image, Point(f.x, f.y), Point(f.x + f.width, f.y + f.height), Scalar(255, 0, 0), 4, 8);
    //fill buffer with easy to parse face representation
    sprintf(buf + strlen(buf), "%i;%i;%i;%in", f.x, f.y, f.width, f.height);
  }
  if(output_file) imwrite((string)output_file, image); //write output image
  return buf;
}
```

这次我提供了一个 main 方法，这样你就可以不用 FFI 直接玩这个程序了。我决定使用一个简单的 char 缓冲区，并用代表检测到的人脸的字符串填充它。每条线以“x；y；宽度；身高”。在我看来，这种方法非常通用，可以让你返回几乎任何你想要的东西，而不必为 C/C++函数中动态大小的多维数组的乐趣而烦恼。

当然，您必须解析返回的字符串，这可能会多花几毫秒的时间，但这一点点开销是可以接受的。如果你觉得这对你很重要，请随意研究他们的 [GitHub Repo](https://github.com/ffi/ffi) 并通读[附加示例](https://github.com/ffi/ffi/tree/master/samples)。您甚至可以找到如何处理回调和其他更高级主题的示例。

但是现在，我们来编译吧！您可能希望创建一个小的 makefile 或构建脚本:

```
LIBS="-lopencv_imgproc -lopencv_highgui -lopencv_core -lopencv_objdetect"
g++ -I/usr/local/include/opencv -I/usr/local/include/opencv2 -L/usr/lib -L/usr/local/lib -fpic -Wall -c "faces.cpp" $LIBS
//create shared library
g++ -shared -I/usr/local/include/opencv -I/usr/local/include/opencv2 -o libfaces.so faces.o -L/usr/local/lib $LIBS
//create executable (in case you want to play with it directly)
g++ -I/usr/local/include/opencv -I/usr/local/include/opencv2 -o faces faces.o -L/usr/local/lib $LIBS
```

一旦编译完成，我们就可以运行它`./faces image_with_faces.jpg detected_faces.jpg`，希望它能为我们检测出一些面孔。

最后，最后一步是用一些 Ruby 代码包装它:

```
require 'ffi'

module Faces
  extend FFI::Library
  ffi_lib File.join(File.expand_path(File.join(File.dirname(__FILE__))), 'libfaces.so')
  attach_function :detect_faces, [:string, :string], :string

  def self.faces_in(image)
    keys = [:x,:y,:width,:height]
    detect_faces(image, nil).split("n").map do |e|
      vals = e.split(';').map(&:to_i)
      Hash[ keys.zip(vals) ]
    end
  end
end

p Faces.faces_in('test.jpg')
```

现在你可以从你的 Ruby 脚本中检测人脸了，是不是很酷？例如，您现在可以编写一个定制的 Rails 验证器，确保用户的个人资料图像只包含一张脸，并在验证失败时指示他们上传更真实的个人资料图像。

你也可以学习更多的 OpenCV，从简单的人脸检测转向更高级的话题，比如人脸识别。然后，您可以编写一个脚本，根据照片中的人物对您的度假照片进行排序，或者利用用户的网络摄像头对用户进行身份验证(请不要这样做！).无论你在想什么，我都强烈建议你去探索和建立一些新的东西，当你这样做的时候，请让我知道。

我真诚地希望我能给你一些启发，让你在下一个很酷的项目中超越 Ruby 的界限！如果你喜欢这篇文章，请随时告诉你的朋友，博客，推特，并传播这个消息:)

## 分享这篇文章