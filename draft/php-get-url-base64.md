[转]PHP base64_encode  在URL地址参数编码上使用

因为我使用公钥，使用php的 openssl 加密之后的资料 base64_encode()字符串，然后放入请求 url 中当作参数，urlencode(base64_encode())，然后用 urldecode() 传过来的参数，出现`+`和 `=`丢失的情况，导致 openssl 解密base64_decode()的字符串失败。 

> 我们知道Base64是网络上最常见的用于传输8Bit字节代码的编码方式之一，看好是编码，并不是加密。
编码过程不解释了，Base64要求把每三个8Bit的字节转换为四个6Bit的字节（3*8 = 4*6 = 24），然后把6Bit再添两位高位0，组成四个8Bit的字节，也就是说，转换后的字符串理论上将要比原来的长1/3。

> 格式是大小写字母、数字、“=”号、“+”号和“/”号
但“=”等号最多只有两个
正则匹配就是 【 [a-zA-Z0-9=+/]+ 】
所以看到有大小写字母的字符串并且有一个或两个等号结束的。基本可以判断是base64编码
base64不适合直接放在URL里作为参数传输,发现base64编码中有“/” “=”符号。为解决此问题，可采用一种用于URL的改进Base64编码，它不在末尾填充'='号，并将标准Base64中的“+”和“/”分别改成了“_”和“-”，这样就免去了在URL编解码和数据库存储时所要作的转换。

通过下面的函数，完美解决base64编码url的问题了：

```
//url base64编码
function urlsafe_b64encode($string) {
    $data = base64_encode($string);
    $data = str_replace(array('+','/','='),array('-','_',''),$data);
    return $data;
}

//url base64解码
function urlsafe_b64decode($string) {
    $data = str_replace(array('-','_'),array('+','/'),$string);
    $mod4 = strlen($data) % 4;
    if ($mod4) {
        $data .= substr('====', $mod4);
    }
    return base64_decode($data);
}
```

##### References
1. [PHP BASE64_ENCODE 在URL地址参数编码上使用](https://www.cnblogs.com/duoshou/articles/9223175.html)