---
title: 一个小白的自学建站史（菜鸟建站入门）
date: 2017-03-07 22:14:49
tags: 经验
---

# 接触

### 内网的尝试

可能有点特别，我刚开始接触与网站相关是在一个关于DVWA这个漏洞系统的搭建上。那时破晓团队的创始人之一K0r4dji到我们学校来有一个讲座，他讲了很多看起来特别高大上的东西。演讲后有一个面基会，我就去参加并找到他，并表示想学一些网络安全技术，希望他能帮我引进门。他就给我介绍了很多东西，其中就包括了DVWA这个漏洞平台，于是百度安装方法，找到了一篇FreeBuf上的文章：[新手指南：手把手教你如何搭建自己的渗透测试环境](http://www.freebuf.com/sectool/102661.html)接触到了xampp这款软件。但是安装了软件之后一脸懵逼，完全不懂，还好教程上边有详细的介绍，一步一步，虽然不理解，但是总归是成功安装了，在电脑上输入127.0.0.1/dvwa/login.php，成功进入！接下来问题就来了，我要如何让别人访问这个漏洞系统呢？教程写着：ip-address-of-windows-xp-machine/dvwa/login.php，于是我傻乎乎的在别的电脑上输入进去，结果不行，一番苦思冥想，终于尝试把“ip-address-of-windows-xp-machine”替换为我电脑内网的IP，成功了，有一点点小激动，也有了一些明悟，IP地址似乎就是相当于一台电脑的标志，每一台处于同一内网环境的电脑都有与其唯一对应的IP地址，通过这个IP可以访问这台电脑（服务器）的数据。之后我又尝试了用自己所学的HTML知识写了网页放到xampp\htdocs目录，然后访问该电脑的IP，果然可以打开网站，仿佛明白的从小到大一直在访问的网站的一点工作方法。

### 公网服务器被黑
<!--more-->
之后偶然的一个机会得知了服务器运营商的存在，比如腾讯云、阿里云、百度云等等，于是我就在腾讯云上试用了一台服务器，用了windows系统，有5天时间，建议通过windows系统自带的远程桌面连接，方法为：

按组合键windows+R，并在运行中输入"mstsc"；

在跳出来的界面输入你的服务器公网IP地址，并按提示输入用户名和密码即可。
第一件事就是在上边安装了xampp，然后把xampp\htdocs目录下文件替换为DVWA，接着把IP地址放到了一个CTF交流群里，当天晚上，有个人在群里问我，这是你的网站吗？我说是，他说你改一个文件给我看看，我就改了，然后我的服务器就被黑客拿下了，还留了后门……这之后才知道DVWA是不能乱放的，不过起码到此为止，我已经可以建立一个外网可以访问的网站了。

# 深入

### 有了模样

我在阿里云的首页上看到有域名注册，于是尝试着注册了我的第一个域名：**_**_**.top（这个域名现在已经不用了，所以打码了），记得是1块钱首年，嘿嘿，但还是不知道要怎么才能使得访问域名就能访问服务器。因为一直自学，我花费了好几天时间，才知道有域名解析这种东西，于是点击域名管理，找到我的域名后点击解析，点击添加记录，主机记录用了www，记录值就填上了我腾讯云服务器的IP地址，然后访问www.**_**_**.top ，成功访问到了我的网站。再之后我开始自己手写网站，命名为1.html，放到服务器里，结果在我访问服务器的时候，浏览器直接列出了我的网站目录（xampp默认开启了目录列表，不然应该是403禁止访问），要手动点击1.html才能打开网站，这可别扭死了！如下图为列出目录
![](https://blog-10039692.file.myqcloud.com/1488433467435_4319_1488433466589.jpg)
下图为403
![](https://blog-10039692.file.myqcloud.com/1488433487113_5678_1488433485867.jpg)
然后尝试各种姿势百度，百度了好长好长时间（估计这种人尽皆知的东西都没人懒得说），才看到了被一句带过的index.html，于是把网页重命名为index.html（修改配置文件也是可以修改默认主页的），成功了！至此，我的网站终于看起来像是一个网站了！如图为我当时建立网站之一：
![](https://blog-10039692.file.myqcloud.com/1488432964695_8706_1488432964443.jpg)

### 一服多站

之后随着对CTF的了解，萌生了建立一个CTF资源站的想法，然后又想建立一个自己主页（这也算是一个小时候的梦想吧），但是服务器只有一台，IP只有一个，如果都放在同一个目录下用同一个域名的话总觉得别扭，这个要怎么解决呢？又一次和朋友聊天的时候他推荐给我一个软件叫phpstudy，果断放弃xampp使用phpstudy，在phpstudy有一个功能叫做站点域名管理，如下图
![](https://blog-10039692.file.myqcloud.com/1488434278819_4925_1488434277635.jpg)![](https://blog-10039692.file.myqcloud.com/1488434289682_3677_1488434288559.jpg)
于是把需要的域名都通过A记录解析到我的服务器上，再添加到域名管理里边，成功实现了一服多站。
随着研究的深入，后来知道：phpstudy是基于apache（也可以选择nginx或iis作为phpstudy的基本程序），通过修改apache的站点配置文件（目录在PHPstudy/Apache/conf/vohsts.conf）也可以实现同样效果，相信聪明的你和我一样一看就懂了，如图：
![](https://blog-10039692.file.myqcloud.com/1488436133365_8283_1488436133876.jpg)
至此，我已经可以较为完善的拥有一个网站服务器了

# 建站经验

### 备案

备案对于国内网站来说是必不可少的一个环节，在腾讯云官网首页上就有备案的链接，如图：
![](https://blog-10039692.file.myqcloud.com/1488436460607_8841_1488436459506.jpg)
按照要求一步步备案就好了，这里通常耗时较长，我的第一次备案差不多用了俩月可能还多，备案的过程中域名是不允许有解析的，也就是说，只要你要备案，你的网站必须是关闭的！所以对于有经验的站长来说，一般都是先备案，后建站，不然的话网站运行着好好的突然关了，不知道的人还以为你的网站倒闭了呢！或许有人不信邪，我就不备案咋了？不备案的话刚开始应该是没什么问题的，但使用一段时间之后，国内的服务商就会把你服务器的端口关掉，网站就用不了了，所以记住一句话：要建站，先备案！除非你用的是港服或者外服。

### SSL

在访问例如腾讯云、百度等网站的时候，会在Chrome浏览器的网址栏看到这样一个绿色的小锁![](https://blog-10039692.file.myqcloud.com/1488437662385_3300_1488437662130.jpg) ，感觉特别好，但是访问我自己的网站却只有一个灰色的感叹号![http://dz.gl](https://blog-10039692.file.myqcloud.com/1488437772942_1273_1488437772290.jpg) ，于是就想，我的网站能不能也有这样一个绿色小锁呢？又是一番各种姿势百度，知道了这个是https协议，需要有网站安全证书，到百度搜了一番，都好贵，舍不得花钱啊~但是峰回路转，发现腾讯云有免费的SSL证书，在管理中心-左上角云产品-SSL证书管理里边，如图：
![](https://blog-10039692.file.myqcloud.com/1488438092663_448_1488438101157.jpg)
点击申请证书，按提示操作之后就可以拥有自己的证书了，不过，这个证书只支持2级域名
![](https://blog-10039692.file.myqcloud.com/1488438268450_1105_1488438279405.jpg)
之后在找到对应的证书并且在服务器上安装即可，我用的是Apache，所以把对应SSL证书(有三个文件)上传到服务器某目录下（例如我创建了PHPstudy/SSL文件夹并上传到该目录下），按照网上的教程修改配置文件，具体方法如下：

1.  首先是确认ssl功能是否开启：找到PHPstudy/Apache/conf/httpd.conf这个配置文件中的LoadModule ssl_module modules/mod_ssl.so这句话是否前面有注释符#，如果有的话删掉注释符并保存；
在PHPstudy/Apache/conf/httpd.conf中添加这样一句话：Include conf/vhost-ssl.conf 并保存；
在PHPstudy/Apache/conf目录下创建vhost-ssl.conf文件，然后编辑（建议使用notepad++编辑，极不推荐记事本）之如下：
```
 <VirtualHost *:443>
  ServerName www.ctftools.com //这里改为你自己的域名，如果只有一个网站可以把这行删掉
  DocumentRoot  "C:\WEB\CTFtools" //这里是你的网站目录
  SSLEngine on
  SSLProtocol  all -SSLv2 -SSLv3
  SSLCipherSuite AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL
  SSLCertificateFile "C:\phpStudy\ssl\downssl\1.crt" //证书公钥，大小为2.01KB左右
  SSLCertificateKeyFile "C:\phpStudy\ssl\downssl\3.key" //证书私钥
  SSLCertificateChainFile "C:\phpStudy\ssl\downssl\2.crt" //证书链，大小为1.88KB左右
 </VirtualHost>
 //有几个站点就写几个这样的一段代码
 <VirtualHost *:443>
  ServerName www.dr34m.cn
  DocumentRoot "C:\WEB\myhome"
  SSLEngine on
  SSLProtocol  all -SSLv2 -SSLv3
  SSLCipherSuite AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL
  SSLCertificateFile "C:\phpStudy\ssl\homessl\1.crt"
  SSLCertificateKeyFile "C:\phpStudy\ssl\homessl\3.key"
  SSLCertificateChainFile "C:\phpStudy\ssl\homessl\2.crt"
 </VirtualHost>
```
2.  重启phpstudy，然后访问https:// 再加上你的站点，是不是可以访问了？
 **注：**如果遇到重启后无法开启Apache，通常是你的配置文件有问题，要有耐心，慢慢查找配置文件的错误。还有一种可能是你的证书公钥和证书链两个文件反了，颠倒过来即可。

 ## 301重定向

 刚开始是在访问腾讯云的时候，发现无论输入的是`qcloud.com`，还是`www.qcloud.com`，或者是`http://www.qcloud.com`，最后访问的都是`https://www.qcloud.com`，这个可就挺好玩的了，刚开始以为是做的跳转，在`qcloud.com`目录下建立一个自动跳转到`www.qcloud.com`的网页，后来知道是做的301重定向，做301重定向的好处就是对搜索引擎友好。为什么要做301重定向呢？一方面，301重定向可以使例如`qcloud.com`跳转到`www.qcloud.com`，这样能增加`www.qcloud.com`的权重；另一方面，我们之前做的SSL证书用的是443端口，并且网址前缀是https，用301重定向可以使得例如`http://www.qcloud.com`跳转到`https://www.qcoloud.com`，没有这步跳转，那我们的SSL证书就白用了，301重定向的办法很多，我用到了其中两种，具体如下：

 ### php做重定向

 这种是用在例如`qcloud.com`跳转到`www.qcloud.com`上的，当然，有些网站的权重是放在没有www的网址上，把有www的跳转到没有www的网址上，也是同样的方法

1.  在PHPstudy\WWW目录下新建文件index.php（对于这种情况就不需要在站点域名配置中添加**非**权重网址了，但是权重网址还是要有的）；
2.  编辑该文件如下
```php
<?php

 $the_host = $_SERVER['HTTP_HOST'];

 switch($the_host)
 {
 //下边的就是带www的网址作为权重网址的例子
     case 'ctftools.com': //这里改为你的非权重网址
     {
         header('HTTP/1.1 301 Moved Permanently'); //301是永久重定向的意思
         header('Location:https://www.ctftools.com/');  //这里改为你的权重网址
     }
     break;
//下边的就是不带www的网址作为权重网址的例子
     case 'www.dz.gl':
     {
         header('HTTP/1.1 301 Moved Permanently');
         header('Location:http://dz.gl');
     }
     break;
//下边则是不符合以上所有情况的例子，学过编程的应该都知道这些语法
     default:
     {
         header('HTTP/1.1 302 Moved Permanently'); //302则是临时重定向的意思
         header('Location:http://error.ctftools.com/404.html');  //这里可以定向到你的404页面
     }
 }
?>
```

 ### .htaccess做重定向

 上边的php方法对于权重网站重定向来说无疑是一种简单的方法，但是对于http向https跳转则没办法了，这就需要.htaccess做重定向（这种方法同样适用于权重网站跳转），方法如下：

1.首先打开`PHPstudy/Apache/conf/httpd.conf`并检查`LoadModule rewrite_module modules/mod_rewrite.so`这句话前面有没有注释符#，有则删掉;
在**网站目录**下创建.htaccess文件，这种以点开头的文件在windows下不好创建，通常使用dos命令来创建，或者用notepad++，新建一个文件，在保存的时候类型选择所有文件，文件名改为.htaccess，如图：![](https://blog-10039692.file.myqcloud.com/1488441538912_436_1488441538316.jpg)
编辑该文件如下：
```
<IfModule mod_rewrite.c>
RewriteEngine on
RewriteCond %{SERVER_PORT} !^443$
RewriteRule ^(.*)$ https://www.ctftools.com/$1 [L,R=301]  //改为你的站点
</IfModule>
```
2. 这时候直接访问http:// 加上你的站点，是不是直接就能跳转到https:// 加上你的站点了？

# 在服务器上安装wordpress

**wordpress是一个著名的博客系统，安装主要用到了mysql(phpstudy集成了)，具体安装方法如下：**

### 配置MySQL

1.  首先修改mysql的密码(针对忘了密码或者不知道密码的童鞋)，如图修改即可；
![](https://blog-10039692.file.myqcloud.com/1488443780553_6823_1488443779593.jpg)
2.  接着访问：[http://你的IP/phpmyadmin](http://xn--ip-0p3cm89l/phpmyadmin) 或者点击phpstudy上边的MySQL管理器—MySQL-front（如图）
![](https://blog-10039692.file.myqcloud.com/1488443755039_5831_1488443754438.jpg)
3.  新建一个数据库：如图，以在服务器端MySQL-front中操作为例：
右击左侧localhost—新建—数据库
![](https://blog-10039692.file.myqcloud.com/1488444028336_2727_1488444027827.jpg)
![](https://blog-10039692.file.myqcloud.com/1488444073489_993_1488444072473.jpg)

### 安装wordpress

1.  到[wordpress官网](https://cn.wordpress.org/)下载wordpress并上传到你的服务器某目录下；
2.  访问"你的目录所绑定域名/wp-admin/install.php"，会出现如图：
![](https://blog-10039692.file.myqcloud.com/1488444539486_5449_1488444539273.jpg)
3.  按图操作：
![](https://blog-10039692.file.myqcloud.com/1488444576677_3585_1488444575741.jpg)
4.  如果数据库信息没有问题就会出现
![](https://blog-10039692.file.myqcloud.com/1488444591766_7065_1488444590814.jpg)
5.  接着按提示输入你的站点信息：
![](https://blog-10039692.file.myqcloud.com/1488444713598_3604_1488444713356.jpg)
6.  这样就可以拥有自己的博客或者论坛了
关于windows建站经验就写到这里，以后我或许会改用linux，届时还会和大家分享，感谢阅读