# JavaScript 利息贷款计算器算法

> 原文：<https://www.sitepoint.com/javascript-interest-loan-calculator-algorithm/>

它使用%利息的计算方法，使用对数来计算本金支付/总利息，以及在给定借款金额和每月支付金额的情况下还清贷款需要多长时间

```
//FORMULA FOR CALUCLATING INTEREST RATE
//p = x*(1 - (1+r)^-n)/r

var interest = 15, rate = interest/100, principal = 1000, payment = 100, noofpay = 12;

var nper1 = Math.log((1-((principal/payment) * (rate/noofpay))));
var nper2 = Math.log((1+(rate/noofpay)));
nper = -(nper1/nper2);
interestpaid=payment*nper-principal;
nper = -Math.round((nper1/nper2));
nyear=Math.floor(nper/12);
nmonth=nper%12;
if (nper>0)
{
    if (nmonth==0)
    {
        period=nyear+" Year(s)";
    }
    else
    {
        period=nyear+" Year(s) and "+nmonth+" Month(s)";
    }
}
else
{
    period="Invalid Values";
    interestpaid=0;
}

console.log("Montly Payments: " + period + ", Total Interest Paid: " + interestpaid.toFixed(2));
```

传入带有参数的函数:

```
//the price calculation formula
//@return the price and length of time
function calculate(interest, principal, payment)
{
    //get data
    var calcElem = $('#calc');

    //FORMULA FOR CALUCLATING INTEREST RATE
    //p = x*(1 - (1+r)^-n)/r

    var rate = interest/100, noofpay = 12;

    var nper1 = Math.log((1-((principal/payment) * (rate/noofpay))));
    var nper2 = Math.log((1+(rate/noofpay)));
    nper = -(nper1/nper2);
    interestpaid=payment*nper-principal;
    nper = -Math.round((nper1/nper2));
    nyear=Math.floor(nper/12);
    nmonth=nper%12;
    if (nper>0)
    {
        if (nmonth==0)
        {
            period=nyear+" Year(s)";
        }
        else
        {
            period=nyear+" Year(s) and "+nmonth+" Month(s)";
        }
    }
    else
    {
        period="Invalid Values";
        interestpaid=0;
    }

    //console.log("Montly Payments: " + period + ", Total Interest Paid: " + interestpaid.toFixed(2));

    var priceData = {
        price : '$'+this.formatCurrency(interestpaid+owe),
        interest : '$'+this.formatCurrency(interestpaid),
        time  : period
    }
    return priceData;
}
```

## 分享这篇文章