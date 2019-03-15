---
title: wordpress发送邮件设置以及常见问题解决
date: 2017-03-09 14:26:16
tags: 经验
---

关于wordpress的搭建在我的另一篇文章"[一个小白的自学建站史（菜鸟建站入门）](https://blog.ctftools.com/2017/03/post5)"的文末已经有了较为详细的解答，需要的朋友可以参考下。本文主要针对于wordpress的邮件设置，以下所有均在wordpress4.7.3环境下进行。

# 发送邮件设置

### 开启WP-Mail-SMTP插件

wordpress原来的邮件设置是有些鸡肋的，通过main()函数来发送，且不说能不能发送成功，就算发送成功了，一般也很难进入别人的收件箱，或者连垃圾箱都进不去，所以在这里介绍一个著名的插件：WP-Mail-SMTP，我想多数人的wordpress都是使用这个插件。具体使用方法：

1.  首先打开wordpress后台，点击左侧插件，点击安装插件，在右侧搜索插件WP-Mail-SMTP，如图：
<!--more-->
![](https://blog-10039692.file.myqcloud.com/1489036656329_6693_1489036653721.jpg)
2.  之后点击“现在安装”，然后启用，接着点击“设置(Settings)”，填入相关信息，如图：
![](https://blog-10039692.file.myqcloud.com/1489038136450_9116_1489038133881.jpg)
![](https://blog-10039692.file.myqcloud.com/1489038144780_5977_1489038141615.jpg)
3.  接着点击下面保存设置，然后可以发送一封测试邮件，试试是否可用，不可用接着往下看。

### 开启OpenSSL

有些人在配置插件时选择SSL加密，却发现无法使用，配置为无加密就又可用了，这种情况通常是未开启OpenSSL，开启后就可解决问题，开启方法如下：

1.  首先打开php目录下的"php.ini"，找到"extension=php_openssl.dll"看看前面有没有注释符";"，有则去掉并保存；若是没有这句话则加上这句话之后保存；
2.  把php目录下的"ssleay32.dll"、"libeay32.dll"，以及php/ext目录下的"php_openssl.dll"拷贝到C:\Windows\System32目录下；
3.  重启Apache（或者IIS）。
注：Linux开启方法请自行百度。

### 解决Wordpress重设链接无效问题

很多朋友在wordpress重设密码时点击邮件中的链接会遇到会遇到重设链接无效的问题，仔细观察会看到链接最后有个"&gt;"，删掉就正常了，这是wordpress共有的BUG，很久以前就有了，不知道为什么现在还存在，但既然存在，那就有解决的办法，具体如下：

1.  编辑wordpress目录下的wp-login.php，搜索："rawurlencode($user_login)"，约在332行，将其所在行修改为：
```php
$message .= network_site_url("wp-login.php?action=rp&amp;amp;key=$key&amp;amp;login=" . rawurlencode($user_login), 'login');
```
如图：
![](https://blog-10039692.file.myqcloud.com/1489039931247_5787_1489039928640.jpg)
并保存，其实就是把前面的"<"和后边的">"删掉；
2.  打开wordpress目录下的wp-includes/pluggable.php，搜索network_site_url，约在1791行，修改为：
```php
$message .= network_site_url("wp-login.php?action=rp&amp;amp;key=$key&amp;amp;login=" . rawurlencode($user-&gt;user_login), 'login') . "\r\n\r\n";
```
如图：
![](https://blog-10039692.file.myqcloud.com/1489040354613_3175_1489040351578.jpg)
并保存，其实还是去掉了那对尖括号。
至此，wordpress已经可以发送邮件了，同时也支持了用户的自动注册。