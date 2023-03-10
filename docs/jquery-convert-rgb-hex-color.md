# jQuery 将 RGB 转换为十六进制颜色值

> 原文：<https://www.sitepoint.com/jquery-convert-rgb-hex-color/>

两个 JavaScript 函数将 rgb 颜色值转换为十六进制颜色值，然后第二个函数将十六进制颜色值转换回 rgb。这对于处理 html 颜色和使用 jQuery 动态改变颜色非常有用。


## 将 RGB 转换为十六进制的函数

```
function rgb2hex(rgb){
 rgb = rgb.match(/^rgb((d+),s*(d+),s*(d+))$/);
 return "#" +
  ("0" + parseInt(rgb[1],10).toString(16)).slice(-2) +
  ("0" + parseInt(rgb[2],10).toString(16)).slice(-2) +
  ("0" + parseInt(rgb[3],10).toString(16)).slice(-2);
}
```

### 将 RGB 转换为十六进制的函数(版本 2)

```
function RGB2Color(r,g,b)
{
  return '#' + this.byte2Hex(r) + this.byte2Hex(g) + this.byte2Hex(b);
}
function byte2Hex (n)
{
  var nybHexString = "0123456789ABCDEF";
  return String(nybHexString.substr((n >> 4) & 0x0F,1)) + nybHexString.substr(n & 0x0F,1);
}
```

## 将十六进制转换为 RGB 的函数

```
function hexToRgb(h)
{
    var r = parseInt((cutHex(h)).substring(0,2),16), g = ((cutHex(h)).substring(2,4),16), b = parseInt((cutHex(h)).substring(4,6),16)
    return r+''+b+''+b;
}
function cutHex(h) {return (h.charAt(0)=="#") ? h.substring(1,7):h}

//usage
console.log(hexToRgb("#FFFFFF"));
```

## 将十六进制转换为 RGB 的函数(PHP)

```
function hex2rgb( $colour ) {
        if ( $colour[0] == '#' ) {
                $colour = substr( $colour, 1 );
        }
        if ( strlen( $colour ) == 6 ) {
                list( $r, $g, $b ) = array( $colour[0] . $colour[1], $colour[2] . $colour[3], $colour[4] . $colour[5] );
        } elseif ( strlen( $colour ) == 3 ) {
                list( $r, $g, $b ) = array( $colour[0] . $colour[0], $colour[1] . $colour[1], $colour[2] . $colour[2] );
        } else {
                return false;
        }
        $r = hexdec( $r );
        $g = hexdec( $g );
        $b = hexdec( $b );
        return array( 'red' => $r, 'green' => $g, 'blue' => $b );
}
```

## 分享这篇文章