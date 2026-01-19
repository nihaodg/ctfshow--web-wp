# ctfshow-web

## 信息收集

### web1

#### 网页源码

![image-20251126084417694](ctfshow-web.assets/image-20251126084417694.png)

​     打开容器发现只有一句话

![image-20251126084448749](ctfshow-web.assets/image-20251126084448749.png)



我们通过右键(F12)查看源代码发现flag  

![image-20251126084620894](ctfshow-web.assets/image-20251126084620894.png)

```
ctfshow{ecfd0f13-b849-40ca-82f5-06d839bd7bb1}
```





### web2

#### js拦截

![image-20251126084834205](ctfshow-web.assets/image-20251126084834205.png)

**容器打开后得到**

![image-20251126084852432](ctfshow-web.assets/image-20251126084852432.png)

通过测试我们发现这个好像禁用了我们的**右键和F12**

但是我们可以通过**ctrl+u**的方式去打开这个源代码

![image-20251126085127455](ctfshow-web.assets/image-20251126085127455.png)

```
ctfshow{3ee5befd-007a-4a73-b3ae-8356f3a554d1}
```





### web3

#### 抓包

![image-20251126085454342](ctfshow-web.assets/image-20251126085454342.png)



我们通过抓包得到flag

![image-20251126085445593](ctfshow-web.assets/image-20251126085445593.png)

```
ctfshow{59a92ec9-13e0-4bc8-8321-5680e256c797}
```





### web4

#### robots泄露

![image-20251126085927546](ctfshow-web.assets/image-20251126085927546.png)

![image-20251126090003724](ctfshow-web.assets/image-20251126090003724.png)

根据提示得到存在robots目录

```
尝试访问 robots.txt
```

![image-20251126090100389](ctfshow-web.assets/image-20251126090100389.png)

得到信息flag在

```
flagishere.txt
```

![image-20251126090143061](ctfshow-web.assets/image-20251126090143061.png)

```
ctfshow{3ed08ac9-1b48-4e69-9d58-6e31a4055f30}
```





### web5

#### phps源码泄露

![image-20251126090222753](ctfshow-web.assets/image-20251126090222753.png)

发现是关于phps文件泄露知识

```
在原网页的请求上加上/index.phps
```

我们访问后会下载一个文件

打开文件就可以得到flag

![image-20251126090629646](ctfshow-web.assets/image-20251126090629646.png)

```
ctfshow{68e0a1e7-4b06-4a22-86df-a81fcc7db5a7}
```





### web6

#### 压缩包泄露

![image-20251126092858329](ctfshow-web.assets/image-20251126092858329.png)

```
发现 有源代码我们尝试，

www.zip
```

![image-20251126094021008](ctfshow-web.assets/image-20251126094021008.png)

```
flag在www.zip
```

![image-20251126094055537](ctfshow-web.assets/image-20251126094055537.png)

```
flag{flag_here}
```





### web7

#### git泄露

![image-20251126094253488](ctfshow-web.assets/image-20251126094253488.png)

```
版本控制很重要，但不要部署到生产
```



开发使用git进行版本管理 最后忘记删除git文件夹了

我们尝试访问/.git/

![image-20251126094454875](ctfshow-web.assets/image-20251126094454875.png)

得到flag

```
ctfshow{70eb7245-1011-4c9c-a0cf-33a776057bba}
```





### web8

#### svn泄露

根据题目提示‘考察信息svn泄露**svn泄露**

```

```

![image-20251126100835447](ctfshow-web.assets/image-20251126100835447.png)

![image-20251126101021516](ctfshow-web.assets/image-20251126101021516.png)

```
ctfshow{8bfda819-c075-43c5-bd19-b49cc1cd9fd2}
```



### web9

#### vimhevi文件丢失泄露

![image-20251126102958919](ctfshow-web.assets/image-20251126102958919.png)

**在Linux生产环境上使用vim或者vi做修改时，会生成一个swp为后缀的文件**

```
会以源文件加一个.swp后缀进行保存，我们可以尝试访问这些文件来尝试获取一些文件。
在 Linux 生产环境中，使用 vim 或 vi 编辑器时，会生成一个以 .swp 为后缀的临时文件。这个文件是 swap 文件（交换文件），用于在编辑过程中保存未保存的更改，以防止意外情况（如系统崩溃、终端断开等）导致数据丢失。
```

/index.php.swp



访问后会下载文件

![image-20251126103430131](ctfshow-web.assets/image-20251126103430131.png)

```
ctfshow{5dc806a2-3dff-4e02-b368-948901070a2b}
```



### web10

#### cookie信息

![image-20251126105046838](ctfshow-web.assets/image-20251126105046838.png)

我们根据提示得到cookie

大概率就是用bp抓包然后得到flag

![image-20251126105152991](ctfshow-web.assets/image-20251126105152991.png)

```
ctfshow{3d56b1dd-d2c2-4b5e-8755-1622256eba30
```



### web11

#### 域名解析

![image-20251126105345596](ctfshow-web.assets/image-20251126105345596.png)

我们发现说用户名有记录

```
flag.ctfshow.com
```

我们可用域名解析来拿到信息

但是一般情况

widows

```
nslookup -type=txt flag.ctfshow.com
```

但是好像没有用了，题目给了我们答案，知道过程就行了

```
flag{just_seesee}
```





### web12

#### 公开信息泄露

![image-20251126110540498](ctfshow-web.assets/image-20251126110540498.png)

打开网页发现最底下有一个number

```
Help Line Number : 372619038
```

那就要找管理员页面，通过我们的爆破发现应该是存在/admin，这个路径

![image-20251126111220392](ctfshow-web.assets/image-20251126111220392.png)

访问

```
url/admin

把账号和admin放进去
和密码
372619038

尝试是对的

```

![image-20251126111421952](ctfshow-web.assets/image-20251126111421952.png)

```
ctfshow{5d2bc4d7-9225-43a4-9340-569bf0b9c24e}
```





### web13

#### 文档敏感信息泄露

![image-20251126111628860](ctfshow-web.assets/image-20251126111628860.png)

```
技术文档里面不要出现敏感信息，部署到生产环境后及时修改默认密码
```

![image-20251126112404053](ctfshow-web.assets/image-20251126112404053.png)

点击之后得到账号密码

![image-20251126112438709](ctfshow-web.assets/image-20251126112438709.png)

然后去访问地址

![image-20251126112517146](ctfshow-web.assets/image-20251126112517146.png)

然后得到flag

```
ctfshow{905fb5b2-af04-41bf-87ea-5f546aef4752}
```





### web14

#### editor信息配置

![image-20251126113122009](ctfshow-web.assets/image-20251126113122009.png)

查看源码，在网页中搜索发现editor是个目录

![image-20251126113339583](ctfshow-web.assets/image-20251126113339583.png)

![image-20251126113358394](ctfshow-web.assets/image-20251126113358394.png)

发现是一个编辑器

点击上传图片发现存在文件空间

![image-20251126114503183](ctfshow-web.assets/image-20251126114503183.png)

![image-20251126114737025](ctfshow-web.assets/image-20251126114737025.png)

![image-20251126114950151](ctfshow-web.assets/image-20251126114950151.png)

最终我们在var/www/html/nothinghere/fl000g.txt这个路径找的到了flag，我们在url后面添加nothinghere/fl000g.txt，就得到了flag：ctfshow{0c219eff-1197-4c03-86dc-ef4b28793e2f}

![image-20251126114825565](ctfshow-web.assets/image-20251126114825565.png)

```
ctfshow{923ab060-8847-4fb4-b662-5b30a7969b4f}
```



### web15

#### qq号泄露

![image-20251126140635543](ctfshow-web.assets/image-20251126140635543.png)

![image-20251126141328725](ctfshow-web.assets/image-20251126141328725.png)

通过信息发现了qq信息泄露

我们通过目录扫描发现是一个admin的路径

![image-20251126141857396](ctfshow-web.assets/image-20251126141857396.png)

但是密码不对

但是忘记密码可以让我们去登入

![image-20251126141940624](ctfshow-web.assets/image-20251126141940624.png)

于是就去QQ上搜索了一下QQ号，只是发现了一个账号，地点是陕西西安

输入后得到新密码然后登陆，拿到flag

![image-20251126142033667](ctfshow-web.assets/image-20251126142033667.png)

```
ctfshow{ee3140bc-d23c-4f94-879f-ba8e321eb6b5}
```



### web16 

#### 探针信息泄露

![image-20251126135214539](ctfshow-web.assets/image-20251126135214539.png)

打开后是这样

![image-20251126135239051](ctfshow-web.assets/image-20251126135239051.png)

​       php探针是用来探测空间、服务器运行状况和PHP信息用的，探针可以实时查看服务器硬盘资源、内存占用、网卡流量、系统负载、服务器时间等信息

我们可以去访问探针

- 根据提示用到php探针知识点
- 输入默认探针tz.php

访问/tz.php，发现phpinfo查看到flag

![image-20251126135620212](ctfshow-web.assets/image-20251126135620212.png)

![image-20251126140205166](ctfshow-web.assets/image-20251126140205166.png)

![image-20251126140226564](ctfshow-web.assets/image-20251126140226564.png)

```
ctfshow{784365b8-4f8d-4f3e-90ea-efb9fe029622}
```



### web17

#### 备份的sql文件泄露

![image-20251126142242347](ctfshow-web.assets/image-20251126142242347.png)

python dirsearch.py -u https://c172e072-fe1c-43c9-a95c-4d718878d640.challenge.ctf.show/ 会发现一个backup.sql的文件 加上去访问就可以了

![image-20251126142404574](ctfshow-web.assets/image-20251126142404574.png)

```
ctfshow{13c03fe5-4c46-45c7-8ef0-f7fe34be20b3}
```



### web18

#### 源码泄露

![image-20251126142444251](ctfshow-web.assets/image-20251126142444251.png)

![image-20251126142933065](ctfshow-web.assets/image-20251126142933065.png)

```
将
window.confirm("\u4f60\u8d62\u4e86\uff0c\u53bb\u5e7a\u5e7a\u96f6\u70b9\u76ae\u7231\u5403\u76ae\u770b\u770b")
复制到控制台去打印
```

![image-20251126143152836](ctfshow-web.assets/image-20251126143152836.png)

然后让我110.php 看看

![image-20251126143246051](ctfshow-web.assets/image-20251126143246051.png)

得到flag

```
ctfshow{c9e25671-43ff-48c4-adf5-ae1884296d3d}
```





### web19

#### 密钥泄露

![image-20251126143333827](ctfshow-web.assets/image-20251126143333827.png)

查看源码得到

![image-20251126143604164](ctfshow-web.assets/image-20251126143604164.png)

```
$u = $_POST['username'];
    $p = $_POST['pazzword'];
    if(isset($u) && isset($p)){
        if($u==='admin' && $p ==='a599ac85a73384ee3219fa684296eaa62667238d608efa81837030bd1ce1bf04'){
            echo $flag;
        }
```

我们知道这个都是POST请求

我们就用hackbar去弄一下

```
username=admin&pazzword=a599ac85a73384ee3219fa684296eaa62667238d608efa81837030bd1ce1bf04
```

![image-20251126143731149](ctfshow-web.assets/image-20251126143731149.png)

```
也可通过解码工具，解码a599ac85a73384ee3219fa684296eaa62667238d608efa81837030bd1ce1bf04，此密文为AES加密 根据网页源代码可知
密钥为key = "0000000372619038";
偏移量为iv = "ilove36dverymuch";
模式为CBC
填充为ZeroPadding
编码为Hex
通过在线解码工具，可解的密码为i_want_a_36d_girl
输入账户admin，密码i_want_a_36d_girl 即可登陆成功，获取flag
```

```
ctfshow{2b45f620-7022-48c4-ba11-35670973be44}
```





### web20

#### mdb文件泄露

![image-20251126143850982](ctfshow-web.assets/image-20251126143850982.png)

```
mdb文件是早期asp+access构架的数据库文件，文件泄露相当于数据库被脱裤了。 根据提示‘mdb文件是早期asp+access构架的数据库文件 直接查看url路径添加/db/db.mdb 下载文件通过txt打开或者通过EasyAccess.exe打开搜索flag ’ 在url后加入/db/db.mdb然后用notepad++打开搜索flag即可得到答案
```

![image-20251126144143641](ctfshow-web.assets/image-20251126144143641.png)

```
flag{ctfshow_old_database}
```

