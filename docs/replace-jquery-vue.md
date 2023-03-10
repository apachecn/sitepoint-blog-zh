# 如何用 Vue 替换 jQuery

> 原文：<https://www.sitepoint.com/replace-jquery-vue/>

*想从头学起 Vue.js？使用 SitePoint Premium 获得涵盖基础知识、项目、技巧和工具&的 Vue 书籍全集。[现在就加入，每月仅需 14.99 美元](https://www.sitepoint.com/premium/products/Z2lkOi8vbGVhcm5hYmxlL1Byb2R1Y3QvMzE3?utm_source=blog&utm_medium=articles)。*

我敢打赌，有很多开发人员在承担构建简单应用的任务时，仍然会使用 jQuery T2。很多时候，我们需要给页面添加一些交互性，但是使用 JavaScript 框架似乎有些矫枉过正——需要额外的千字节、样板文件、构建工具和模块捆绑器。包含来自 CDN 的 jQuery 似乎是显而易见的。

在这篇文章中，我想试着让你相信，使用 [Vue.js](https://vuejs.org/) (从这里开始简称为 Vue)，即使对于相对基础的项目，也不一定是一件令人头痛的事情，并且会帮助你更快地写出更好的代码。我们举一个简单的例子，用 jQuery 编写代码，然后一步一步地在 Vue 中重新创建。

## 我们正在建造的东西

对于本文，我们将使用 Sparksuite 中的这个[开源模板构建一个基本的在线发票。希望这能使另一个待办事项列表焕然一新，并提供足够的复杂性来展示使用 Vue 之类的东西的优势，同时仍然易于遵循。](https://github.com/sparksuite/simple-html-invoice-template)

![Screenshot of template](img/cb12bd191a6ada3154dc1494aa0cf0f0.png)

我们将通过提供项目、单价和数量输入，并在其中一个值发生变化时让*价格*列自动重新计算，来实现这种交互。我们还将添加一个按钮，在发票中插入新的空行，以及一个在我们编辑数据时会自动更新的*总计*字段。

我已经修改了模板，现在单个(空)行的 HTML 如下所示:

```
<tr class="item">
  <td><input value="" /></td>
  <td>$<input type="number" value="0" /></td>
  <td><input type="number" value="1" /></td>
  <td>$0.00</td>
</tr> 
```

## jQuery

所以，首先，让我们看看如何用 jQuery 来做这件事。

```
$('table').on('mouseup keyup', 'input[type=number]', calculateTotals); 
```

我们给表本身附加了一个监听器，当*单位成本*或*数量*值改变时，它将执行`calculateTotals`函数:

```
function calculateTotals()  {
  const subtotals = $('.item').map((idx, val)  => calculateSubtotal(val)).get();
  const total = subtotals.reduce((a, v)  => a + Number(v),  0);
  $('.total td:eq(1)').text(formatAsCurrency(total));
} 
```

该函数查找表中的所有项目行，并对它们进行循环，将每一行传递给一个`calculateSubtotal`函数，然后对结果求和。然后将此`total`插入发票上的相关位置。

```
function calculateSubtotal(row) {
  const $row = $(row);
  const inputs = $row.find('input');
  const subtotal = inputs[1].value * inputs[2].value;

  $row.find('td:last').text(formatAsCurrency(subtotal));

  return subtotal;
} 
```

在上面的代码中，我们获取了对行中所有`<input>`的引用，并将第二个和第三个相乘得到小计。然后将该值插入到该行的最后一个单元格中。

```
function formatAsCurrency(amount) {
  return `$${Number(amount).toFixed(2)}`;
} 
```

我们还有一个小助手函数，我们用它来确保小计和总计都被格式化为两位小数，并以货币符号为前缀。

```
$('.btn-add-row').on('click', () => {
  const $lastRow = $('.item:last');
  const $newRow = $lastRow.clone();

  $newRow.find('input').val('');
  $newRow.find('td:last').text('$0.00');
  $newRow.insertAfter($lastRow);

  $newRow.find('input:first').focus();
}); 
```

最后，我们有一个用于添加行按钮的点击处理程序。我们在这里做的是选择最后一行项目并创建一个副本。克隆行的输入设置为默认值，并作为新的最后一行插入。我们也可以对用户好一点，把焦点放在第一次输入上，准备让他们开始输入。

下面是完整的 jQuery 演示:

在 [CodePen](https://codepen.io) 上通过 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )查看笔 [jQuery 发票](https://codepen.io/SitePoint/pen/YaBwJQ/)。