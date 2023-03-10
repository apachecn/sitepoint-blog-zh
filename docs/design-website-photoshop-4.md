# 用 Photoshop 文章设计一个网站

> 原文：<https://www.sitepoint.com/design-website-photoshop-4/>

##### 把所有的放在一起

现在是时候把我们刚刚分割的所有图片放到一个 HTML 文档中，来创建我们的页面模板了。

1.  创建一个有 5 行 3 列的表格。确保将

    标签放在表格之前，将``标签放在表格之后，以确保它在页面上居中。

3.  然后，在第一行中，使第一个单元格位于第三栏的左侧，这样您就可以将 header.jpg 图像放置在该单元格中。这也将确定模板的宽度。
4.  在标题下方的行中，将每个单元格的行高设为 18 像素，以与 blue_square.gif 图像相对应，并将单元格的宽度设为 16 像素。对第四行进行同样的操作。
5.  现在，将 blue_square.gif 图像放在第二行和第四行的外部单元格中。将 gray_bar.gif 图像放入第三行的第一个单元格(左侧),并对第三列重复此步骤。
6.  将两个单元格的背景颜色都设置为#CCCCCC。将第二行和第四行的第二列的背景色设置为#374256。将第三行中间的列(您的内容将位于该列)的背景色设为#CCCCCC。
7.  在第五行中，选择第一个单元格，并将其设置为 col span 3，这样就可以将 footer.jpg 放置在其中。
8.  最后，一定要指定第一行、第二行、第四行和第五行的行高，不要指定第三行，这样它可以随着内容的增加而增加。

就是这样！您的页面应该是这样的:

由于页面最初的白色背景与设计不太协调，我创建了一个背景图片平铺在界面后面。

要制作这些简单的扫描线，在 Photoshop 中创建一个宽 1 像素、高 2 像素的图像。选择一个 1px 乘 1px 的正方形，并给它涂上颜色来赞美你的设计。然后，向下移动选区，用另一种颜色填充新的正方形。将整个图像保存为双色 gif 图像，并将其命名为 bg.gif。只需将此 gif 放在新模板的 body 标签中，它就会平铺在整个页面上。要查看最终结果，[点击这里](http://www.webmasterbase.com/examples/ps-design/eBook_interface.html)。

请注意，由于放置了 gray_bar 图像，文本在内容区域中似乎是缩进的。您需要将这些单元格的背景颜色更改为与 gray_bar 图像相同的颜色(#CCCCCC)，这样当页面变长时，它看起来仍然一样。

为了创建放置文本链接的空间，我只是将表格中该单元格的背景颜色设置为与 blue_square 图像(#374256)相同的颜色。文本链接上不断变化的颜色翻转效果是用 CSS(层叠样式表)创建的。

**Go to page:** [1](https://sitepoint.com/design-website-photoshop) | [2](https://sitepoint.com/design-website-photoshop-2/) | [3](https://sitepoint.com/design-website-photoshop-3/) | [4](https://sitepoint.com/design-website-photoshop-4/)

## 分享这篇文章