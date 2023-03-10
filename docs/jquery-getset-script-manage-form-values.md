# 管理表单值的 jQuery 获取/设置脚本

> 原文：<https://www.sitepoint.com/jquery-getset-script-manage-form-values/>

使用这个出色的 jQuery 脚本管理表单值。这将节省您大量的时间，并有助于保持您的代码更干净，更容易操作和引用表单值。

## 特征

*   获取/设置表单输入的函数
*   集成所有输入类型，包括复选框和单选按钮
*   干净且易于使用

## 使用

```
//GET
$.field('myInputName');
//returns input value

//SET
$.field('myInputName','myValue');
//sets input value and returns jQuery selector of input
```

## 代码

```
(function(){
   /**
   * This jQuery Extension adds the ability to access form fields by the shortcut property .field()
   * which gets and sets input field values by name using the .find() method.
   * @param string inputName
   * @param mixed value (optional)
   */
	$.fn.field = function( inputName, value){

		if(typeof inputName!='string' ){
			return false;
		}

		var $inputElement = $(this).find('[name='+inputName+']');

		/*
		 *  Get request, return the input fields value.
		 */
		if(typeof value==='undefined' && $inputElement.length>=1)
		{
			switch($inputElement.attr('type')){
				case 'checkbox':
					return $inputElement.is(':checked');
					break;
				case 'radio':
					var result;
					$inputElement.each(function(i,val){
						if($(this).is(':checked'))
							result = $(this).val();
					});
					return result;
					break;
				default:
				   return $inputElement.val();
				   break;
			}
		}

		 /*
		 *  Set request, set the input field to the value provided
		 *  and return a reference to the jQuery selector for the input
		 */
		else
		{
			switch($inputElement.attr('type')){
				case 'checkbox':
					$inputElement.attr({checked: value});
					break;
				case 'radio':
					$inputElement.each(function(i){
					   if($(this).val()==value)
					   {
						  $(this).attr({checked: true});
					   }
					});
					break;
				case undefined:
				   $(this).append('<input type="hidden" name="'+inputName+'" value="'+value+'">' );
				   break;
				default:
				   $inputElement.val(value);
				   break;
			}
			return $inputElement;
		}
	}
})();
```

## 分享这篇文章