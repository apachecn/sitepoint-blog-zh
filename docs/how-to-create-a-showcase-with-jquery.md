# 如何使用 jQuery 创建展示

> 原文：<https://www.sitepoint.com/how-to-create-a-showcase-with-jquery/>

橱窗是向访问者展示您的最新产品组合或展示最新产品或文章的绝佳方式。许多网站使用这种技术将信息塞进一个区域，这样你的访问者就不会错过你的信息。

本教程将向您展示如何利用 jQuery 创建这样的展示。

jQuery 让动画变得简单。本教程将指导您设置 HTML、CSS 和相关的 jQuery 代码来创建 showcase。

![](img/d302bd934a68c2b737c29e67a9b6e300.png "jqueryslider")

### HTML

viewport div 是您希望向用户显示的内容的窗口。我们稍后将使用 CSS 来确保视口只显示您想要的内容。在 div 内部，我们将封装另外两个 div；一个用于部分，另一个用于当视窗滚动到视图中时将弹出的标签。这两个都在另一个 div: scrollable 中，它是我们用来将所有内容滚动到视图中的元素。sections div 将包含另外四个 div，它们将代表我们的节，labels div 将包含另外四个 div，它们将代表我们的标签。最后，我们将创建一行简单的按钮，以便我们可以选择将某个部分滚动到视图中。

```
<div id="viewport"> 

		<div id="scrollable"> 

			<div id="sections"> 

				<div id="section1" class="section"> 
				<h1>Uluru</h1> 
				</div> 

				<div id="section2" class="section"> 
				<h1>The Eiffel Tower</h1> 
				</div> 

				<div id="section3" class="section"> 
				<h1>Empire State Building</h1> 
				</div> 

				<div id="section4" class="section"> 
				<h1>The Great Wall of China</h1> 
				</div> 

			</div> 

			<div id="label"> 

				<div class="label"> 
				<p>Also known as Ayre's rock</p> 
				</div> 

				<div class="label"> 
				<p>In the world's most romantic city</p> 
				</div> 

				<div class="label"> 
				<p>Site of the last hour of King Kong's life</p> 
				</div> 

				<div class="label"> 
				<p>This can be seen from space</p> 
				</div> 

			</div> 
		</div> 
	</div> 

	<div id="menu"> 

	<div id="scroll1" class="button"></div> 
	<div id="scroll2" class="button"></div> 
	<div id="scroll3" class="button"></div> 
	<div id="scroll4" class="button"></div> 

	</div>
```

### CSS

我们将从视口本身开始。我们要设置视窗的尺寸，确保其中的任何内容都不会流出，我们将添加一个 5px 的粗实线边框。

```
#viewport 
{ 
	overflow:hidden; 
	border:5px solid; 
	height:300px; 
	width:600px; 
}
```

部分和标签 div 非常相似。宽度将由您想要的节数乘以每个节的宽度来确定。在本例中，我们的部分是 600px 宽，我们将使用其中的四个部分，因此#content 和# label divs 的宽度将是 2400px (4 x 600px)。宽度和高度都是我们需要的样式部分。

```
#sections
{ 
	width:2400px; 
	height:300px; 
}
```

标签 div 略有不同，因为我们要求它在我们单击按钮时滑出视图，并且它不需要为 300 像素，因为这将在它进入视图时模糊整个视口。我们将它设置为 100 像素高。我们还需要确保标签在页面第一次加载时出现，所以我们将 bottom 属性设置为 100 像素，这样它就出现在它所在的 div 底部上方 100 像素处。我们还需要将位置设置为相对位置，这样我们就可以使用底部属性。

```
#label 
{ 
	position:relative; 
	width:2400px; 
	height:100px; 
	bottom:100px; 
}
```

内容和标签都包装在另一个标识为 scrollable 的 div 中，这将是我们用来移动其中所有内容的元素。这需要设置为内容 div 的尺寸，因为它包含了所有可以在视窗中看到的内容。我们需要设置相对位置，因为我们将通过 jQuery 利用 left 属性将内容滚动到视图中。
我们现在来看看各个部分的 div，为我们的目的设计它们的样式。每个被分类为 section 的 div 都需要彼此向左浮动，否则内容将流出包含它的 div，流到下一行。我们需要它们彼此相邻，这样当我们改变#scrollable 的左边位置时，相关的部分就会出现。我们的部分将是 600 像素宽，300 像素高，但我们会添加一些 10 像素的填充，这样所有的东西都不会与视窗的两侧齐平。我们的高度和宽度将需要减少 20 像素，以迎合这一点。

```
.section 
{ 
	float:left; 
	padding:10px; 
	width:580px; 
	height:280px; 
}
```

单个标签 div 宽 600 像素，高 100 像素，填充 10 像素。我们还需要让它们向左浮动，这样它们就可以并排出现，而不会堆叠在一起。我们还将标签的背景颜色设置为灰色，文本颜色设置为白色。

```
.label 
{ 
	float:left; 
	padding:10px; 
	height:80px; 
	width:580px; 
	background-color:#777; 
	color:white; 
}
```

我们将在背景中添加一些图像。我使用了维基共享的一些图片，将它们缩小到 600 像素乘以 300 像素，并将其设置为背景图片。

```
#section1 
{ 
	background-image:url('../image/uluru.jpg'); 
} 

#section2 
{ 
	background-image:url('../image/eiffeltower.jpg'); 
} 

#section3 
{ 
	background-image:url('../image/empirestate.jpg'); 
} 

#section4 
{ 
	background-image:url('../image/greatwall.jpg'); 
}
```

我们需要做的最后一点样式是菜单部分，它将允许我们滚动到各个部分。你可以用任何你想要的方式来做这件事，但是在这个例子中，我们将只使用一些简单的 div，它将是 30px 乘 30px，有一个灰色的背景颜色，并且通过设置 5px 的边距来彼此间隔 5px。这些按钮都将被包装在另一个 div 中，我们不需要设计这个 div 的样式，但是它包含了我们所有的按钮。

```
.button 
{ 
	height:30px; 
	width:30px; 
	background-color:#777; 
	float:left; 
	margin:5px; 
}
```

所有的 css 都完成了，现在我们准备开始使用 jQuery 了。

### jQuery

#### 排队事件

我们将首先检查我们的展示“小部件”将做什么。当我们点击一个按钮时，我们希望我们的标签从视图中消失，新的部分出现在视图中，然后标签再次弹出。我们将滚动可滚动的 div，所以我们只需要关心滚动，它里面的所有东西都会被拖动。

所以事情的顺序是:

1.  隐藏标签
2.  滚动视口
3.  显示标签

有许多方法可以实现这一点，但是我们将介绍 jQuery 提供的队列函数。排队的原则是将事件添加到对象中，然后将它们出队或执行它们。我们将创建一个函数，它对三个函数进行排队，以隐藏标签，滚动视口，然后显示标签。将事件排队需要将事件附加到 DOM 中的对象。您可以创建自定义队列，或者，如果没有指定，您可以使用默认队列(称为' fx ')。一旦在 fx 中对事件进行排队，第一个函数将会执行。但是，需要显式调用下一个函数来执行。

主函数 navigate 将设置排队事件。我们还将添加一个函数来清除队列，这样事件就不会积压，从而导致队列变得更大，需要更长的时间才能完成。如果没有参数，clearQueue()将清除默认 fx 队列中的事件。

```
function navigate(position) 
	{ 
		$('#scrollable').clearQueue(); 

		$('#scrollable').queue(hideLabel); 
		$('#scrollable').queue(scroll(position)); 
		$('#scrollable').queue(showLabel); 
	}
```

#### 动画元素

接下来，我们将通过使用 animate()方法来定义这些函数，并利用回调来使下一个事件出队。

要隐藏标签，我们需要将标签的底部位置移动到 0px，使其从视口中消失，我们将指定这需要四分之一秒或 250 毫秒。我们还需要确保队列中的下一个事件执行，因此我们创建一个内联函数来将下一个事件出队。

```
function hideLabel() 
	{ 
		$('#label').animate( 
			{bottom:'0px'}, 
			250, 
			null, 
			function() 
			{ 
				$('#scrollable').dequeue(); 
			}); 
	}
```

接下来，我们需要将可滚动的 div 滚动到相关的位置。由于每个部分是 600 像素，我们需要将每个部分的 div 向左移动 600 像素。要在视口中显示第一部分，left 属性需要为 0px，这是页面加载时的默认状态，要查看第二部分，我们需要将 left 设置为-600px，即第三部分；-1200px 等等。这一转变将需要 500 毫秒或半秒钟。我们还需要让队列中的下一个事件出队，因此我们再次创建一个匿名函数来完成这项工作:

```
function scroll(position) 
	{ 
		position = position + "px"; 

		$('#scrollable').animate( 
			{left:position}, 
			500, 
			null, 
			function() 
			{ 
				$('#scrollable').dequeue(); 
			}); 
	}
```

最后一个函数需要再次显示标签，所以我们将底部的 css 属性设置回 100px，并确保这发生在 250 毫秒内。我们不需要触发队列中的下一个事件，因为这是序列中的最后一个事件。

```
function showLabel() 
	{ 
		$('#label').animate( 
			{bottom:'100px'}, 
			250); 
	}
```

### 绑定事件

我们需要做的下一件事是将 navigate 事件附加到页面上的相关元素。在我们的例子中，这是我们之前定义的四个按钮。附加这些事件的最佳方式是通过 jQuery。在我们这样做之前，需要完全加载文档，所以我们使用 ready()函数来包装代码。

我们使用 jQuery 的 click 函数实例化一个匿名函数，这个匿名函数反过来用一个适当的位置值触发 navigate 函数。

```
$(document).ready(function() 
		{ 

			$('#scroll1').click(function() 
				{ 
					navigate(0); 
				} 
			); 

			$('#scroll2').click(function() 
				{ 
					navigate('-600'); 
				} 
			); 

			$('#scroll3').click(function() 
				{ 
					navigate('-1200'); 
				} 
			); 

			$('#scroll4').click(function() 
				{ 
					navigate('-1800'); 
				} 
			);
		}
	);
```

这就是创建滚动展示“窗口小部件”所需的全部内容，但我们将只添加几行 jQuery 来增加标签的不透明度，并使其在鼠标经过时淡入，在鼠标移出标签时淡出。这段代码可以简单地添加到 ready()函数中，确保它在文档完全加载之前不会被执行。我们将绑定事件，因为正如您稍后将看到的，我们将需要在某些情况下解除绑定。淡入和淡出的两个函数定义为:

```
function fadeOut() 
	{ 
		$('#label').stop().fadeTo(250,0.7); 
	} 

	function fadeIn() 
	{ 
		$('#label').stop().fadeTo(250,1); 
	}
```

我们还将添加一段代码，将不透明度初始设置为 0.7。这是 jQuery 真正擅长的地方。有许多浏览器之间的 css 不透明属性不一致，但我们不必担心这一点。我们需要做的就是指定 W3C 标准的不透明度属性。jQuery 完成剩下的工作。同样，这可以添加到 ready()函数中。

```
$('#label').css("opacity", "0.7");
```

### 整理

您会注意到，当使用按钮选择新的部分时，如果您将鼠标快速移动到标签上，动画会突然停止。这是因为我们已经将一个事件绑定到 label 元素，并使用 stop()来阻止事件排队并使 UI 不可用。我们可以分离该事件，并在滚动完成后重新附加它，以免造成中断。在我们的导航函数中，我们将在创建队列之前解除事件绑定:

```
function navigate(position) 
	{ 
		$('.label').unbind('mouseover', fadeIn); 
		$('.label').unbind('mouseout', fadeOut); 

		$('#scrollable').clearQueue(); 

		$('#scrollable').queue(hideLabel); 
		$('#scrollable').queue(scroll(position)); 
		$('#scrollable').queue(showLabel); 
	}
```

现在事件已经分离，如果鼠标进入标签区域，动画将不会停止。一旦所有的动画完成，我们需要重新连接它。一个明智的做法是在 showLabel()函数中调用一个匿名回调函数，因为这是队列中的最后一个函数，只有当它完成时才会执行。

```
function showLabel() 
	{ 
		$('#label').animate( 
			{bottom:'100px'}, 
			250, 
			function() 
			{ 
				$('.label').bind('mouseover', fadeIn); 
				$('.label').bind('mouseout', fadeOut); 
			}); 
	}
```

我们应该做的最后一件事是，如果我们试图选择一个我们已经在的部分，确保没有动画被调用。我们可以通过字符串变量来表示我们在哪个页面上，然后在 navigate()函数中测试这个变量，以确定我们是否应该执行转换。我们将创建一个在任何函数范围之外的变量，这样任何地方都可以访问它，这个变量叫做 currentPage，并在开始时将其设置为 1。我们将修改 navigate()函数以接受第二个参数；通知函数哪个部分被调用的页面。可以用 currentPage 测试这个值，看看动画是否应该出现。如果当前页面没有被调用，我们可以制作动画，然后将当前页面设置为被调用的页面。navigate()函数应该更新成这样(注意，我们已经在上面声明了 currentPage 变量):

```
var currentPage;

	function navigate(position, page) 
	{ 
		if(page != currentPage) 
		{ 
			currentPage = page; 

			$('.label').unbind('mouseover', fadeIn); 
			$('.label').unbind('mouseout', fadeOut); 

			$('#scrollable').clearQueue(); 

			$('#scrollable').queue(hideLabel); 
			$('#scrollable').queue(scroll(position)); 
			$('#scrollable').queue(showLabel); 
		} 
	}
```

还需要更改按钮的绑定，以便将页面值发送到 navigate():

```
$(document).ready(function() 
		{ 

			$('#scroll1').click(function() 
				{ 
					navigate(0,1); 
				} 
			); 

			$('#scroll2').click(function() 
				{ 
					navigate('-600',2); 
				} 
			); 

			$('#scroll3').click(function() 
				{ 
					navigate('-1200',3); 
				} 
			); 

			$('#scroll4').click(function() 
				{ 
					navigate('-1800',4); 
				} 
			); 

			$('.label').bind('mouseover', fadeIn); 
			$('.label').bind('mouseout', fadeOut); 

			$('#label').css("opacity", "0.7"); 
		} 
	);
```

### 摘要

在本教程中，您学习了如何创建一个简单的展示窗口小部件。您使用 HTML 和 CSS 来设置样式，最重要的是使用溢出属性来限制视口的视图。使用 jQuery 对事件进行排队和出列使您能够异步执行事件，绑定和取消绑定事件使您能够对 UI 进行更多的控制，以防止不必要的动画。这个例子很容易扩展到创建更多部分。您只需要确保#content、#scrollable 和# label div 的宽度扩展到包含您想要的额外部分(记住——每个部分 600px ),当然，在#content div 中添加一个额外部分，在#labels div 中添加一个相关标签。

你可以在这里下载本教程的源代码[。](https://www.sitepoint.com/wp-content/uploads/2011/12/scrollingviewpoint.zip)

## 分享这篇文章