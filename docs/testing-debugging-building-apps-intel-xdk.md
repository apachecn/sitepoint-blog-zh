# 使用 XDK 测试、调试和构建应用

> 原文：<https://www.sitepoint.com/testing-debugging-building-apps-intel-xdk/>

在英特尔 XDK 系列的第二部分[中，我们研究了它支持的框架，进行了一些编码，并在仿真器中运行了我们的应用。在第三部分中，我们将完成该应用，并了解如何使用 XDK API 桥。最后，我们将学习如何测试、调试和构建应用程序。](https://www.sitepoint.com/coding-first-cross-platform-app-intel-xdk/)

你可以在这里找到我们将在[上工作的最终代码。](https://github.com/sitepoint-examples/Intel-XDK-introduction-p3)

## 让我们重新编码

该应用程序目前有两个选项卡，链接到两个不同的视图。在第一个视图(选定的视图)中，我们将显示一些照片，如果单击其中一张照片，一个新视图将显示该照片的附加信息。

我们必须创建一个容器来填充照片列表，点击时会有响应。App Framework 提供了一个特殊的方法，用于在元素被选中时执行函数。使用`data-load`并给出一个函数名作为参数。您的 HTML 应该是这样的:

```
<div class="panel" title="Photos" id="main_pg" data-load="displayData"  selected="selected">  
        <div id="photoList"></div>        
    </div>
```

带有`id="photoList"`的新`div`将作为每个图像的父图像。你注意到`data-load`的财产了吗？选择该视图时，执行`displayData`功能。这个函数仍然不存在，但是我们很快会创建它。用`id="content"`在`div`内创建另一个面板:

```
<div class="panel" title="Photo" id="photo">
     <h3>&nbsp;</h3>
     <img id="photoImg" src="#">
     <p>&nbsp;</p>
     <div>
         <button id="emailButton">Send with e-mail</button>
     </div>
</div>
```

现在 html 完成了，让我们写一些 Javascript。在`img`文件夹中，我已经添加了一些图像，我将引用这些本地图像。在这个示例应用程序中，我们使用本地数据，但它可以交换到一个 Rest API，只需很少的更改。

在`data.js`文件中，我添加了:

```
var data = [
    {
        id: 0,
        title: "This is the title",
        url: "img_1.jpeg",
        description: "Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
    },
    {
        id: 1,
        title: "This is the title",
        url: "img_2.jpeg",
        description: "Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
    },
    {
        id: 2,
        title: "This is the title",
        url: "img_3.jpeg",
        description: "Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
    },
    {
        id: 3,
        title: "This is the title",
        url: "img_4.jpeg",
        description: "Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
    }
];
```

打开`app.js`并创建一个获取上面数据的函数。

```
function getData(){

    return data;

}
```

现在我们有了数据，我们需要显示它。

```
function displayData(){

    $("#photoList").html('');

    var data = getData();

    $.each(data, function(index, photo){

        var img = $.create("img", {src: "images/" + photo.url, alt: "something"});
        img.attr("data-id", photo.id);
        img.attr("style", "width: 100%; margin: 2px auto; display: block;");

        var imgContainer = $.create("div",{});
        imgContainer.append(img);

        $("#photoList").append(imgContainer);

    });
}
```

这个函数选择保存图像的容器，并确保它是空的。然后，它从`getData`函数中检索数据。`displayData`函数不知道数据存储在哪里，也不知道`getData`函数从哪里检索数据。

这是将应用程序逻辑与数据存储分离的第一步，也是可扩展的。它给了我们不使用 API 进行开发的自由，最后，编写一个适配器来检索数据或编辑数据(如果局部发生了变化)。

`$.each`遍历所提供的数据，并用`id="photoList"`将任何新图像附加到容器中。

现在，在模拟器中测试它，您将有望看到如下内容:

![Emulating with Intel XDK](img/ee209eb79f8745301c73f54a1c376bcf.png)

现在我们已经列出了照片，我们需要显示它的数据。之前我们创建了一个新的面板，我们将使用它来显示特定照片的更多信息。

```
$("#photoList").on("click", "img", function (){

    var id = $(this).attr('data-id');

    $("#photo h3").html(getData()[id].title);
    $("#photo img").attr("src", "images/" + getData()[id].url);
    $("#photo p").html(getData()[id].description);

    $.ui.loadContent("#photo",false,false,"pop");

});
```

这是一个类似 JQuery 的事件处理方法。它选择了`#photoList`元素，如果点击了`img`元素，它就执行匿名函数。

然后，它选择`h3`和`p`标签，并再次使用`getData()`函数添加本地数据的*描述*和*标题*。这个函数返回一个数组，我们可以使用它的 id 来搜索给定的元素。

最后我们展示面板，`$.ui.loadContent("#photo",false,false,"pop");`允许我们从一个面板过渡到另一个面板。如果你想了解更多关于这个功能的信息，请阅读[这篇文章](http://app-framework-software.intel.com/api.php#%24_ui_loadContent)。

再次测试应用程序。点击其中一张图片，看看会发生什么。

![Now images can be clicked in our app](img/c472b66140d9f80f03816aa9e1069d3e.png)

我对这种风格不满意。我将这几行代码添加到`style.css`中，这样看起来更好:

```
#photo h3{
    text-align: center;
    margin: 5px;
}

#photo img{
    width: 100%;
    margin: 0 auto;
    display: block;
}

#photo button{
    display: block;
    margin: 0 auto;
    margin-top: 15px;
}
```

最后，我们需要通过电子邮件将照片发送给某人。为此，我们将使用英特尔 XDK API。

将此添加到`app.js`:

```
$("#emailButton").on("click", function(){

    var src = $("#photoImg").attr("src");

    console.log(src);

    intel.xdk.device.sendEmail(
        'Hi, Check out this photo:' + src, // The body raw
        "aleksanderkoko@gmail.com",     // To the email that you want to send
        "Something cool that i found",  // The subject
        true,                           // Is Html
        "",                             // CCed
        ""                              // BCCed
    );

});
```

这是一个事件处理器。当按钮被点击时，匿名功能被执行。

在该函数中，我们首先从照片中选择`src`属性。这是一个问题，因为`src`属性指向本地存储的照片。如果您将应用程序转换为 Rest API，那么这将不再是一个问题。这仅用于演示目的。

`intel.xdk.device.sendEmail()`很简单。我们指定要发送的文本、主题、收件人，以及这是 HTML 还是纯文本电子邮件。快速阅读[文档](https://software.intel.com/en-us/node/493039)，了解关于这个特殊功能的更多细节。

英特尔 XDK API 还有许多其他功能可供使用。二维码阅读器、定向锁功能、短信发送、脸书物件等等。查看[函数文档](https://software.intel.com/en-us/node/492826)了解更多信息。

## 在设备上测试和调试

我有一台 Android 设备，因此可以使用应用程序预览应用程序轻松测试我的应用程序。通过点击*测试*选项卡并选择您想要使用的方法，我使用的是 WIFI 模式。这意味着我必须将我的笔记本电脑和 android 设备连接到同一个 WIFI。使用 WIFI 模式时，打开应用预览应用后，选择*本地应用*。您也可以选择移动模式，从服务器上提取项目。最后从列表中选择想要的项目，点击*启动*按钮。

如果你在 iPhone 上运行这个应用程序，它会看起来像一个 iOS7 应用程序，在 Android 上它会看起来像一个 Android 应用程序。

尝试通过电子邮件共享照片。对我来说，它给了我 3 个选项:蓝牙、驱动和 Gmail。我选择了 Gmail，然后选择了发送按钮。

![The resulting email](img/283414773f2db2117009c8e442fbe81d.png)

这可能不是最好看的电子邮件，但至少它有效，是一个好的起点。

您有两种调试选择。调试在测试选项卡中进行测试时，这里使用了 [Weinre](http://people.apache.org/~pmuellr/weinre-docs/latest/) 工具，但没有提供太多信息。第二种方法是通过 debug 选项卡进行调试。这只支持 Android 设备，但它是一个比 Weinre 更深入的调试工具。选择最适合你的选项。

您必须将这行代码添加到`body`标签之前，才能让 Weinre 工作。

```
<script src="http://debug-software.intel.com/target/target-script-min.js#ShkWrJQLqFOnYaUo-mmbXo6ZvNRwCQHyvboazA1qGFA"></script>
```

## 构建应用程序

build 选项卡是最简单的部分，尽管这两个平台略有不同。您可以配置不同的选项，如应用程序名称、版本、应用程序权限、启动图标、闪屏、推送通知等。

XDK 还允许您在其他几个平台上构建应用，如 Windows 8。

请注意，在 iOS 上，你不需要安装 Mac OS X，但你需要一个 IOS 开发者账户。建立后，你仍然需要上传到每个商店。借助 XDK，您可以从编码到构建，整个开发周期中只缺少发布任务。

![The Intel XDK Build tab](img/955d41b19f133114ebd4e49111246958.png)

## 包扎

在这个由三部分组成的系列中，我们涵盖了很多信息。如果您想进一步了解文档，请深入阅读 XDK 应用编程接口和应用框架。请随意抓取我们编码的项目，并使用它做您想做的事情。我的建议是增加一个 Rest API 连接，填充第二个选项卡，增加更多的社交分享按钮。

如果你有任何意见，请在下面的评论区告诉我，我会很高兴收到你的来信。

你对英特尔 XDK 公司有什么看法？

## 分享这篇文章