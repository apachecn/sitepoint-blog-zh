# 更新火狐扩展的快捷方式

> 原文：<https://www.sitepoint.com/a-quick-and-dirty-way-to-update-your-firefox-extensions/>

![Firefox logo (refreshed!)](img/d177726e78e58a9af48970bf992ae30c.png)

作为一个火狐用户，当一个新的版本发布了，但是你最喜欢的扩展没有立即更新时，这是令人沮丧的。

但是从一个扩展开发者的角度来看，并不总是能够像 Firefox 本身一样提供更新。接受新版本需要时间，因为每一个新的上传都需要人工审核和批准，这个过程通常需要几个星期。

然而，在许多情况下，您会发现一个扩展在新的构建中会工作得非常好——或者至少足够好——并且现在它真正需要的是被告知这一点。只要掌握一点专业知识，您就可以自己完成这项工作，通过编辑扩展的安装文件来指定最新的构建版本。

## 自己动手

第一次运行新版本时，它会禁用任何不支持的扩展，但仍会安装它们。因此，假设您想要更新的扩展就是这种情况，您应该这样做:

1.  **找到你的主扩展文件夹**，它直接在你的配置文件文件夹里面([如何找到你的配置文件文件夹](http://kb.mozillazine.org/Profile_folder_-_Firefox))。
2.  **识别特定扩展名的文件夹**。该文件夹将以扩展的唯一 <abbr title="IDentifier">ID</abbr> 命名，如果你幸运的话，它将有一个直观的名称(例如，Firebug 的文件夹被称为*" Firebug @ software . Joe hewitt . com "*)。然而，该名称可能是一个 <abbr title="Globally Unique IDentifier">GUID</abbr> (一个<abbr title="hexadecimal">十六进制</abbr>值，用花括号括起来，例如*" { 3c 6 E1 eed-a07e-4c 80-9 cf3-66 ea 0 BF 40 b 37 } "*)，在这种情况下，您必须查看文件夹本身，以确定正确的扩展——每个文件夹中直接有一个名为 *"install.rdf"* 的文件，其中包含扩展的核心元数据
3.  **把这个文件夹复制到一个临时位置**，比如桌面，**然后从火狐的附加组件对话框里卸载扩展**；**重启火狐**完成卸载。
4.  In the copied folder, **open the *“install.rdf”* file** (making double-sure it’s the copy you just made, not the original), and look for a group of elements like this:

    ```
    <em:type>2</em:type>
    <em:targetApplication>
        <Description>
            <em:id>{ec8030f7-c20a-464f-9b0e-13a3a9e97384}</em:id>
            <em:minVersion>2.0</em:minVersion>
            <em:maxVersion>3.5.*</em:maxVersion>
        </Description>
    </em:targetApplication>
    ```

    在大多数情况下，只有一组这样的元素，但是如果扩展支持多个设备(比如 Flock 和 Firefox ),那么就会有多个组——每个设备一个组。您需要定位与 Firefox 对应的元素组，这可以通过将`<em:id>`识别为具有确切值`"{ec8030f7-c20a-464f-9b0e-13a3a9e97384}"`或者将`<em:maxVersion>`识别为该扩展支持的最后一个 Firefox 版本来实现。例如，如果您刚刚从 3.5 升级到 3.6，那么`<em:maxVersion>`元素很可能会有值`"3.5.*"`，这表明它最大的支持是 3.5 分支中的任何构建。那么，你会想要**改变最大版本以匹配你刚刚安装的火狐更新**——所以对于火狐 3.6 你会把它改成`"3.6.*"`。

5.  完成更改后，**保存并关闭文件，然后将文件夹移回其在 profile extensions 目录中的原始位置**。
6.  **再次重启 Firefox** 重新安装扩展，它现在将接受您指定的最新支持版本！

总的来说，我们所做的就是告诉扩展支持以后的构建。当然，这并不能保证它真的会起作用；或者如果有，它将正常工作；在极端情况下，它可能会使 Firefox 挂起或崩溃，在这种情况下，你别无选择，只能再次禁用它。

但是有可能它会工作得很好，虽然这只是一个临时的解决方案，但它将允许你在等待适当的更新时继续使用你最喜欢的扩展！

## 进一步阅读

如果这些激起了你更深入研究 Firefox 扩展的兴趣，你会很高兴地知道 SitePoint 已经出版了一本电子书和几篇后续文章，都是关于扩展开发的:

*   构建自己的 Firefox 扩展
*   在火狐扩展学校他们永远不会告诉你的 10 件事
*   [用一点 HTML 给你的 Firefox 扩展增添趣味](https://www.sitepoint.com/article/firefox-extensions-with-html/)

## 分享这篇文章