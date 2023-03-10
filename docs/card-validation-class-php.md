# 用 PHP 创建信用卡验证类

> 原文：<https://www.sitepoint.com/card-validation-class-php/>

虽然 PayPal 等在线支付方式在过去几年中变得非常流行，但大多数在线商店仍然使用某种商业系统来接受来自其网站的信用卡支付。在您实际将客户的信用卡号加密到数据库或将它们转发到商家服务器之前，最好实现您自己的信用卡验证例程。

在本文中，我们将通过开发一个 PHP 类来存储信用卡的详细信息，并使用 Mod 10 算法来验证其号码。要实现我们将在本文中创建的类，您应该能够访问运行 PHP 4.1.0 或更高版本的 Apache web 服务器。

##### 信用卡验证

当我们说“验证信用卡号”时，我们实际上是什么意思？很简单，这意味着我们通过一种称为 Mod 10 算法的特殊算法来运行信用卡号。

该算法针对数字处理一些简单的数字数据验证例程，并且该算法的结果可用于确定信用卡号是否有效。有几种不同类型的信用卡可以用来购物，但是它们都可以使用 Mod 10 算法进行验证。

除了通过 Mod 10 算法，信用卡号还必须通过几个不同的格式规则。六种最流行的信用卡的规则列表如下:

*   **万事达卡:**必须有 51 到 55 的前缀，长度必须是 16 位。
*   **签证:**必须以 4 为前缀，长度必须为 13 或 16 位数字。
*   **美国运通:**前缀必须是 34 或 37，长度必须是 15 位。
*   **Diners Club:** 必须有 300 到 305、36 或 38 的前缀，长度必须是 14 位。
*   **Discover:** 前缀必须是 6011，长度必须是 16 位。
*   **JCB:** 前缀必须是 3、1800 或 2131，长度必须是 15 或 16 位。

如前所述，在本文中，我们将创建一个 PHP 类，该类将保存信用卡号码的详细信息，并公开一个指示信用卡号码是否有效的函数(即，它是否通过了 Mod 10 算法)。然而，在我们创建这个类之前，让我们看看 Mod 10 算法是如何工作的。

##### 模 10 算法

Mod 10 算法需要三个步骤来确定信用卡号是否有效。我们将使用有效的信用卡号码 378282246310005 来演示这些步骤:

***第一步***

数字被反转，每第二个数字的值加倍，从第二位数字开始:

378282246310005

变得…

500013642282873

并且每第二个数字的值加倍:

5 0 0 0 1 3 6 4 2 2 8 2 8 7 3

x2 x2 x2 x2 x2 x2 x2

——————————————-

0 0 6 8 4 4 14

***两步***

将每第二个数字乘以 2 得到的数值相加(即，在上面的例子中，7 乘以 2 得到 14，即 1 + 4 = 5)。这些相加的结果被加到没有相乘的每个数字的值上(即第一个数字、第三个数字、第五个数字等等):

5 + (0) + 0 + (0) + 1 + (6) + 6 + (8) + 2 + (4) + 8 + (4) + 8 + (1 + 4) + 3

= 60

***三步***

当对第二步的结果应用模运算时，余数必须等于 0，才能通过 Mod 10 算法。模数运算符只返回除法的余数，例如:

10 MOD 5 = 0 (5 两次进入 10，余数为 0)

20 MOD 6 = 2 (6 三次进入 20，余数为 2)

43 MOD 4 = 3 (4 除以 43 十次，余数为 3)

因此，对于我们的测试信用卡号码 378282246310005，我们将模数 10 应用于第二步的结果，如下所示:

60 MOD 10 = 0

模数运算返回 0，表示信用卡号有效。

既然我们已经理解了 Mod 10 算法，那么用 PHP 创建自己的版本来验证信用卡号码就非常容易了。现在让我们创建我们的信用卡类。

##### 创建 CCreditCard 类

现在让我们创建一个 PHP 类，我们可以用它来存储和验证信用卡的详细信息。我们的类将能够保存持卡人的姓名、卡的类型(mastercard、visa 等)、卡号以及到期月份和日期。

创建一个名为 class.creditcard.php 的新 PHP 文件。当我们完成下面的步骤时，将显示的每段代码复制粘贴到文件中并保存它。

我们从定义几个卡类型常量开始。这些值将用于表示我们的类将要验证的卡的类型:

```
<?php  

 define("CARD_TYPE_MC", 0);  

 define("CARD_TYPE_VS", 1);  

 define("CARD_TYPE_AX", 2);  

 define("CARD_TYPE_DC", 3);  

 define("CARD_TYPE_DS", 4);  

 define("CARD_TYPE_JC", 5);
```

接下来，我们有我们的类声明。我们班叫`CCreditCard`。请注意，在类名的前面特意多了一个“C ”:在类名前面加上“C”是一种常见的编程习惯，实际上表明它是一个类。

我们还定义了五个成员变量，它们将在内部用于保存信用卡的名称、类型、号码、到期月份和年份:

```
class CCreditCard  

 {  

 // Class Members  

 var $__ccName = '';  

 var $__ccType = '';  

 var $__ccNum = '';  

 var $__ccExpM = 0;  

 var $__ccExpY = 0;
```

接下来，我们有我们的类的自定义构造函数。构造函数是一个与其所在的类同名的函数。它不返回值。它的特殊之处在于，每当我们创建该类的新实例时，它都会自动执行。

每当我们想要创建我们的`CCreditCard`类的新实例时，我们必须显式地向它的构造函数传递五个参数:持卡人的姓名、卡类型、号码和到期日期。因为我们已经创建了自己的自定义构造函数(PHP 实现了一个默认的构造函数，如果我们不显式地创建一个，它就不接受任何参数)，所以每次实例化该类时，我们都必须为这五个参数传递值。如果我们忽略它们，PHP 将会产生一个错误。

```
 // Constructor   

 function CCreditCard($name, $type, $num, $expm, $expy)   

 {
```

如果传递给构造函数的`$name`变量的值为空，那么我们使用`die()` 函数来终止我们的类的实例化，并输出一条错误消息，告诉用户他们必须向构造函数传递一个名称:

```
 // Set member variables   

 if(!empty($name))   

 {   

 $this->__ccName = $name;   

 }   

 else   

 {   

 die('Must pass name to constructor');   

 }
```

我们的`CCreditCard`类是灵活的:它接受几种不同的方式来指定被存储的卡的类型。例如，如果我们想将万事达卡的详细信息添加到我们的`CCreditCard`类的新实例中，那么我们可以为构造函数的`$type` 变量传递以下值:“mc”、“mastercard”、“m”或“1”。

我们确保已经传入了有效的卡类型，并将我们的 classes `$__ccType`变量的值设置为我们之前定义的常量卡类型值之一:

```
 // Make sure card type is valid   

 switch(strtolower($type))   

 {   

   case 'mc':   

   case 'mastercard':   

   case 'm':   

   case '1':   

     $this->__ccType = CARD_TYPE_MC;   

     break;   

   case 'vs':   

   case 'visa':   

   case 'v':   

   case '2':   

     $this->__ccType = CARD_TYPE_VS;   

     break;   

   case 'ax':   

   case 'american express':   

   case 'a':   

   case '3':   

     $this->__ccType = CARD_TYPE_AX;   

     break;   

   case 'dc':   

   case 'diners club':   

   case '4':   

     $this->__ccType = CARD_TYPE_DC;   

     break;   

   case 'ds':   

   case 'discover':   

   case '5':   

     $this->__ccType = CARD_TYPE_DS;   

     break;   

   case 'jc':   

   case 'jcb':   

   case '6':   

     $this->__ccType = CARD_TYPE_JC;   

     break;   

   default:   

     die('Invalid type ' . $type . ' passed to constructor');   

 }
```

如果传入了无效的卡类型，那么将调用 switch 语句的默认分支，导致我们的脚本以`die()`函数终止。

我们可以利用 PHP 对正则表达式的内置支持，通过使用`ereg_replace`函数从信用卡号中去除所有非数字字符:

```
 // Don't check the number yet,    

 // just kill all non numerics    

 if(!empty($num))    

 {    

   $cardNumber = ereg_replace("[^0-9]", "", $num);    

   // Make sure the card number isnt empty    

   if(!empty($cardNumber))    

   {    

     $this->__ccNum = $cardNumber;    

   }    

   else    

   {    

     die('Must pass number to constructor');    

   }    

 }    

 else    

 {    

   die('Must pass number to constructor');    

 }
```

我们通过确保到期月份和年份都是有效的数值来结束我们的`CCreditCard` 构造函数:

```
 if(!is_numeric($expm) || $expm < 1 || $expm > 12)    

 {    

   die('Invalid expiry month of ' . $expm . ' passed to constructor');    

 }    

 else    

 {    

   $this->__ccExpM = $expm;    

 }    

 // Get the current year    

 $currentYear = date('Y');    

 settype($currentYear, 'integer');    

 if(!is_numeric($expy) || $expy < $currentYear || $expy    

 > $currentYear + 10)    

 {    

   die('Invalid expiry year of ' . $expy . ' passed to constructor');    

 }    

 else    

 {    

   $this->__ccExpY = $expy;    

 }    

 }
```

在我们的`CCreditCard`类中，设置信用卡详细信息值的唯一方法是通过构造函数。为了检索特定于类的变量的值(`$__ccName`、`$__ccType`等)，我们创建了几个函数，如下所示:

```
 function Name()    

 {    

   return $this->__ccName;    

 }    

 function Type()    

 {    

   switch($this->__ccType)    

     {    

     case CARD_TYPE_MC:    

       return 'mastercard [1]';    

       break;    

     case CARD_TYPE_VS:    

       return 'Visa [2]';    

       break;    

     case CARD_TYPE_AX:    

       return 'Amex [3]';    

       break;    

     case CARD_TYPE_DC:    

       return 'Diners Club [4]';    

       break;    

     case CARD_TYPE_DS:    

       return 'Discover [5]';    

       break;    

     case CARD_TYPE_JC:    

       return 'JCB [6]';    

       break;    

     default:    

       return 'Unknown [-1]';    

   }    

 }    

 function Number()    

 {    

   return $this->__ccNum;    

 }    

 function ExpiryMonth()    

 {    

   return $this->__ccExpM;    

 }    

 function ExpiryYear()    

 {    

   return $this->__ccExpY;    

 }
```

这些函数允许我们检索类中包含的变量的值。例如，如果我创建了一个名为`$cc1`的`CCreditCard`类的实例，那么我可以使用`$cc1->ExpiryMonth()`来检索它的到期月份。

使用信用卡时的一个常见功能是将您从该用户那里获取的详细信息显示给他们作为确认。例如，如果用户输入了信用卡号码 4111111111111111，那么您可能只想向他们显示部分号码，例如 4111111111111xxxx。我们的`CCreditCard` 类包含一个名为`SafeNumber`的函数，它接受两个参数。第一个是用来屏蔽数字的字符，第二个是要屏蔽的数字个数(从右开始):

```
 function SafeNumber($char = 'x', $numToHide = 4)     

 {     

   // Return only part of the number     

   if($numToHide < 4)     

   {     

     $numToHide = 4;     

   }     

   if($numToHide > 10)     

   {     

     $numToHide = 10;     

   }     

   $cardNumber = $this->__ccNum;     

   $cardNumber = substr($cardNumber, 0, strlen($cardNumber) - $numToHide);     

   for($i = 0; $i < $numToHide; $i++)     

   {     

     $cardNumber .= $char;     

   }     

   return $cardNumber;     

 }
```

如果我们有一个名为`$cc1` 的`CCreditCard`类的实例，并且这个类中存储的信用卡号是 42424242424242，那么我们可以像这样屏蔽最后 6 位:`echo $cc1->SafeNumber('x', 6)`。

我们的`CCreditCard`类中包含的最后一个函数叫做`IsValid`，针对我们类的信用卡号实现 Mod 10 算法，返回 true/false。

首先将两个变量(`$validFormat`和`$passCheck`)设置为假:

```
 function IsValid()     

 {     

   // Not valid by default     

   $validFormat = false;     

   $passCheck = false;
```

接下来，我们要确保信用卡号的格式正确。我们使用 PHP 的`ereg`函数来做这件事。每张卡必须匹配的正则表达式是不同的:

```
 // Is the number in the correct format?     

 switch($this->__ccType)     

 {     

   case CARD_TYPE_MC:     

     $validFormat = ereg("^5[1-5][0-9]{14}$", $this->__ccNum);     

     break;     

 case CARD_TYPE_VS:     

     $validFormat = ereg("^4[0-9]{12}([0-9]{3})?$", $this->__ccNum);     

     break;     

 case CARD_TYPE_AX:     

     $validFormat = ereg("^3[47][0-9]{13}$", $this->__ccNum);     

     break;     

 case CARD_TYPE_DC:     

     $validFormat = ereg("^3(0[0-5]|[68][0-9])[0-9]{11}$", $this->__ccNum);     

     break;     

 case CARD_TYPE_DS:     

     $validFormat = ereg("^6011[0-9]{12}$", $this->__ccNum);     

     break;     

 case CARD_TYPE_JC:     

     $validFormat = ereg("^(3[0-9]{4}|2131|1800)[0-9]{11}$", $this->__ccNum);     

     break;     

   default:     

   // Should never be executed     

   $validFormat = false;     

 }
```

此时，如果信用卡号格式正确，`$validFormat`将为真(`ereg`返回真/假)，否则为假。

我们现在实现了 Mod 10 算法的 PHP 版本，使用的步骤与我们之前描述的完全相同:

```
 // Is the number valid?      

 $cardNumber = strrev($this->__ccNum);      

 $numSum = 0;      

 for($i = 0; $i < strlen($cardNumber); $i++)      

 {      

   $currentNum = substr($cardNumber, $i, 1);      

 // Double every second digit      

 if($i % 2 == 1)      

 {      

   $currentNum *= 2;      

 }      

 // Add digits of 2-digit numbers together      

 if($currentNum > 9)      

 {      

   $firstNum = $currentNum % 10;      

   $secondNum = ($currentNum - $firstNum) / 10;      

   $currentNum = $firstNum + $secondNum;      

 }      

 $numSum += $currentNum;      

}
```

`$numSum`变量将包含 Mod 10 算法第二步中所有变量的总和，我们在前面已经描述过了。PHP 的模数运算符的符号是'【T1]'，所以我们根据`$numSum`的模数是否为零，将 true/false 赋给`$passCheck` 变量:

```
 // If the total has no remainder it's OK      

 $passCheck = ($numSum % 10 == 0);
```

如果`$validFormat` 和`$passCheck`都为真，那么我们返回 true，表示卡号有效。如果不是，我们返回 false，表示卡号格式不正确，或者 Mod 10 检查失败:

```
 if($validFormat && $passCheck) return true;      

   else return false;      

  }      

 }      

?>
```

这就是我们`CCreditCard`班的全部内容！现在让我们看一个使用 HTML 表单、PHP 和我们的`CCreditCard`类实例的简单验证示例。

##### 使用我们的信用卡类

创建一个名为 testcc.php 的新文件，并将其保存在与 class.creditcard.php 文件相同的目录中。将以下代码输入 testcc.php:

```
<?php include('class.creditcard.php'); ?>      

<?php      

 if(!isset($submit))      

 {      

 ?>      

   <h2>Validate Credit Card</h2>      

   <form name="frmCC" action="testcc.php" method="post">      

   Cardholders name: <input type="text" name="ccName"><br>      

   Card number: <input type="text" name="ccNum"><br>      

   Card type: <select name="ccType">      

   <option value="1">mastercard</option>      

   <option value="2">Visa</option>      

   <option value="3">Amex</option>      

   <option value="4">Diners</option>      

   <option value="5">Discover</option>      

   <option value="6">JCB</option>      

   </select><br>      

   Expiry Date: <select name="ccExpM">       

   <?php      

     for($i = 1; $i < 13; $i++)      

     { echo '<option>' . $i . '</option>'; }      

   ?>       

   </select>      

   <select name="ccExpY">      

   <?php      

     for($i = 2002; $i < 2013; $i++)      

     { echo '<option>' . $i . '</option>'; }      

   ?>       

   </select><br><br>      

   <input type="submit" name="submit" value="Validate">      

   </form>      

   <?      

   }      

   else      

   {      

   // Check if the card is valid      

   $cc = new CCreditCard($ccName, $ccType, $ccNum, $ccExpM, $ccExpY);      

   ?>      

   <h2>Validation Results</h2>      

   <b>Name: </b><?=$cc->Name(); ?><br>      

   <b>Number: </b><?=$cc->SafeNumber('x', 6); ?><br>      

   <b>Type: </b><?=$cc->Type(); ?><br>      

   <b>Expires: </b><?=$cc->ExpiryMonth() . '/' .      

   $cc->ExpiryYear(); ?><br><br>      

   <?php      

     echo '<font color="blue" size="2"><b>';       

     if($cc->IsValid())      

     echo 'VALID CARD';      

     else      

     echo 'INVALID CARD';      

     echo '</b></font>';      

   }      

?>
```

在您的浏览器中运行脚本，看看会发生什么…

这是我的浏览器的两个截图。第一个显示 HTML 表单，第二个显示表单提交后的输出:

![728_image1](img/d161145bdff7559700611ed813795989.png)
*输入卡片详细信息*

![728_image2](img/9f177d67893389ae19f0d39230a38978.png)
*卡片验证的结果*

##### 结论

在本文中，我们看到了如何利用 PHP 的面向对象特性(最显著的是类)来创建信用卡存储和验证类。我们详细讨论了这个类的组件，最后我们创建了一个测试脚本，在这个脚本中我们实例化了 CCreditCard 类并验证了一个样本卡号。

如果你想建立一个电子商务网站来处理/存储访问者的信用卡信息，那么你应该学习我们刚刚制作的课程，并根据你的需要进行定制。您可能希望向它添加其他函数来比较 CCreditCard 对象，将卡的详细信息格式化为 XML 字符串，将卡的详细信息加密到数据库中，甚至实时处理支付。

## 分享这篇文章