# jQuery 将复选框值列表存储在 div 中

> 原文：<https://www.sitepoint.com/jquery-store-list-checkbox-values-div/>

假设你想在一个文本区域中存储一个列表，这样当一个复选框被选中时，它会将项目添加到列表中。同样，如果在中取消选中该复选框，则会从列表中删除该项目。这就是你做这种疯狂事情的方式！；——)

### 演示

[https://jsfiddle.net/jquery4u/W37rB/show/](https://jsfiddle.net/jquery4u/W37rB/show/)

```
/*when a user selects interest in an addtional service, add this to the additionalServices div*/
$('input[type="checkbox"]').bind('change', function() {
	var alsoInterested = '';
	$('input[type="checkbox"]').each(function(index, value) {
		if (this.checked) {
			/*add*/ /*get label text associated with checkbox*/
			alsoInterested += ($('label[for="'+this.name+'"]').html() + ', ');
		}
	});
	if (alsoInterested.length > 0) {
		alsoInterested = 'I am also interested in booking: ' + alsoInterested.substring(0,alsoInterested.length-2) + '.';
	} else {
		alsoInterested = 'I am not interested in additional services.';
	}

	$('#additionalServices').html(alsoInterested);
	//console.log($('#additionalServices').html());
});
```

代码基于以下 HTML:

```
 <form method="get" id="myForm" action="">
	<input type="checkbox" id="bannanasChk" name="bannanasChk" value="N">
	<label for="bannanasChk" class="light">Bannanas</label>

	<input type="checkbox" id="carChk" name="carChk" value="N">
	<label for="carChk" class="light">Car Monkeys</label>

	<input type="checkbox" id="apesChk" name="apesChk" value="N">
	<label for="apesChk" class="light">Apes</label>

	<input type="checkbox" id="repairChk" name="repairChk" value="N">
	<label for="repairChk" class="light">Car Repair</label>

	<input type="submit" value="Submit">
</form> 

## 分享这篇文章

```