# 辛纳特拉的电影，第二部分

> 原文：<https://www.sitepoint.com/moving-pictures-sinatra-part-ii/>

![mp_sinatra](img/572437c41d503a65f21c45a780b1c2bf.png)

在第一部分中，我们建立了一个 Sinatra 网站来从目录中读取图片。现在，我们将创建一个上传图片的表单，并进行测试以确保其正常工作。

您需要一个带有文件输入的表单来完成这项工作。让我们编写一个测试来确保这一点。

#### test/test.rb

```
...
def test_for_upload_form
  get '/upload'
  assert last_response.ok?
  assert last_response.body.include?("type=\"file\"")
end
...
```

该测试检查表单的输入之一是否是“文件”类型。还没有，所以测试应该会失败。

```
$ ruby test/test.rb
Run options: --seed 34613

# Running tests:

..F

Finished tests in 0.022114s, 135.6607 tests/s, 180.8809 assertions/s.

  1) Failure:
test_for_upload_form(MyTest) [test/test.rb:27]:
Failed assertion, no message given.

3 tests, 4 assertions, 1 failures, 0 errors, 0 skips
```

我的 27 号线是

```
assert last_response.ok?
```

如果你很好奇，启动应用程序，进入[/上传](http://127.0.0.1:4567/upload)页面。

![mp-get-upload](img/3078fbd23ce8c39b175008ec4781c124.png)

看起来我们需要一个路径处理器来上传。将它添加到 *main.rb* 文件中。

#### main.rb

```
get '/upload' do
  "Hello World"
end
```

你认为考试会通过还是会失败？运行它并找出答案。

```
$ ruby test/test.rb
Run options: --seed 10631

# Running tests:

F..

Finished tests in 0.022114s, 135.6607 tests/s, 226.1011 assertions/s.

  1) Failure:
test_for_upload_form(MyTest) [test/test.rb:28]:
Failed assertion, no message given.

3 tests, 5 assertions, 1 failures, 0 errors, 0 skips
```

以前的错误已经过去了，但现在又出现了新的错误。我们没有该页的表格。

#### main.rb

```
...
get '/upload' do
  erb :upload
end
...
@@upload
<!DOCTYPE html>
<html>
  <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="user-scalable=yes, width=device-width" />
<title>Upload Moving Pictures</title>
</head>
<body>
  <form action="/upload" enctype="multipart/form-data" method="post">
    <label for="file">File to upload:</label>
    <input id="file" type="file" name="file">
    <input id="submit" type="submit" name="submit" value="Upload picture!">
  </form>
</body>
</html>
...
```

如果你还记得的话，我们正在使用 Sinatra 的便捷的内嵌视图。现在我们有了一个允许我们上传图片的表单，你认为这次测试会通过吗？

```
$ ruby test/test.rb
Run options: --seed 6047

# Running tests:

...

Finished tests in 0.022980s, 130.5483 tests/s, 217.5805 assertions/s.

3 tests, 5 assertions, 0 failures, 0 errors, 0 skips
```

您有一个*上传*页面和能够选择文件的表单。下一步是什么？是的，上传文件到*公共/幻灯片 _ 图片*目录。让我们编写一个测试，它将发送到我们新的上传路由处理器，并给出上传发生的指示。

#### test/test.rb

```
...
def test_upload_file
  post '/upload'
  assert last_response.ok?
  assert last_response.body.include?("Uploaded")
end
...
```

运行测试。

```
$ ruby test/test.rb
Run options: --seed 59047

# Running tests:

...F

Finished tests in 0.023828s, 167.8697 tests/s, 251.8046 assertions/s.

  1) Failure:
test_upload_file(MyTest) [test/test.rb:33]:
Failed assertion, no message given.

4 tests, 6 assertions, 1 failures, 0 errors, 0 skips
```

响应不正常。出于好奇，继续启动服务器

```
$ ruby main.rb
```

转到[上传页面。](http://127.0.0.1:4567/upload)并提交表单。这是应用程序给我的:
![mp-post-upload](img/da65d74e68ccffba7308b111fc747598.png)
，建议将文本添加到 *main.rb* 文件中，但让我们将“Hello World”改为“Uploaded”

#### main.rb

```
post "/upload" do
  "Uploaded."
end
```

运行测试。通过了吗？酷毙了。恭喜，我们刚刚写了一个可怕的测试。测试名称为 *test_upload_file。然而我们甚至没有测试是否有任何东西被上传。你会如何测试？*

可以统计一下 *slideshow_picture* 目录下的文件数量。上传文件并检查文件数量是否增加了一个。让我们重写测试。

#### test/test.rb

```
...
def test_upload_file
  pictures_before = load_pictures.length
  post '/upload'
  pictures_after = load_pictures.length
  assert_equal (pictures_before + 1), pictures_after
end
...
```

让我们试试那个。运行测试。

```
$ ruby test/test.rb
Run options: --seed 51246

# Running tests:

..F.

Finished tests in 0.030883s, 129.5211 tests/s, 194.2816 assertions/s.

  1) Failure:
test_upload_file(MyTest) [test/test.rb:35]:
Expected: 2
  Actual: 1

4 tests, 6 assertions, 1 failures, 0 errors, 0 skips
```

好多了。这不应该是夜间飞行测试。我们没有任何代码将文件上传到`post '/upload'`路由处理器中。我们来补充一下。

### main.rb

```
post "/upload" do 
  File.open('public/slideshow_pictures/' + params['file'][:filename], "w") do |f|
    f.write(params['file'][:tempfile].read)
  end
  "Uploaded."
end
```

这里我们从表单中取出文件，`open`把它放在适当的目录下，并编写文件。你认为测试会通过吗？我们有写文件的代码。运行一下看看。

```
$ ruby test/test.rb
Run options: --seed 51246

# Running tests:

..F.

Finished tests in 0.030883s, 129.5211 tests/s, 194.2816 assertions/s.

  1) Failure:
test_upload_file(MyTest) [test/test.rb:35]:
Expected: 2
  Actual: 1

4 tests, 6 assertions, 1 failures, 0 errors, 0 skips
```

发生了什么事？我们发布了上传方法。等等，我们发文件了吗？我们需要发送一个文件参数到上传方法。

#### test.rb

```
...
post '/upload', {:file =>{:filename=>"cat.jpg"}}
...
```

这将创建一个名为*cat.jpg*的空文件，这次呢……测试会通过吗？

```
$ ruby test/test.rb
Run options: --seed 42496

# Running tests:

....

Finished tests in 0.078357s, 51.0484 tests/s, 76.5726 assertions/s.

4 tests, 6 assertions, 0 failures, 0 errors, 0 skips
```

太好了。世界之王！为了好玩，让我们重新测试一下，看看有多棒。

```
$ ruby test/test.rb
Run options: --seed 25680

# Running tests:

F...

Finished tests in 0.083972s, 47.6349 tests/s, 71.4524 assertions/s.

  1) Failure:
test_upload_file(MyTest) [test/test.rb:35]:
Expected: 3
  Actual: 2

4 tests, 6 assertions, 1 failures, 0 errors, 0 skips
```

现在就像是永别了国王！发生了什么事？如果你在*public/slide show _ pictures*目录中查找，已经有一个 cat.jpg 的*。当我们在测试中上传另一个 cat.jpg*文件时，我们正在改写它。这意味着我们没有增加文件的数量，所以测试失败。
你会如何[删除](http://www.ruby-doc.org/core-2.0/File.html#method-c-delete)那个文件？一个选项是查看文件是否已经[存在。如果有，就删除它。](http://www.ruby-doc.org/core-2.0/File.html#method-c-exist-3F)

### test/test.rb

```
...
def test_upload_file
  if File.exists?("public/slideshow_pictures/cat.jpg")
    File.delete("public/slideshow_pictures/cat.jpg")
  end
  pictures_before = load_pictures.length
  post '/upload', {:file =>{:filename=>"cat.jpg"}}
  pictures_after = load_pictures.length
  assert_equal (pictures_before + 1), pictures_after
end
...
```

重新运行测试。

```
$ ruby test/test.rb
Run options: --seed 12414

# Running tests:

....

Finished tests in 0.069093s, 57.8930 tests/s, 86.8395 assertions/s.

4 tests, 6 assertions, 0 failures, 0 errors, 0 skips
```

如果你再次运行测试，你应该得到同样的结果。现在我们有了一个可以上传图片并将图片展示出来的网站。

## 让我们回顾一下我们所做的。

*   我们写了，现在可以识别一个没有价值的测试。
*   我们学习了如何查看文件是否存在。
*   我们学会了如何删除文件。

在本系列的下一篇(也是最后一篇)文章中，我们将添加一些 CSS，检查特定的文件类型，并结束我们的基本图像显示应用程序。

## 分享这篇文章