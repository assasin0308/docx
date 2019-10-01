# centos6.5 php5.3升级至php5.6

[TOC]



0. 注意: **php56w**版本只有mongdb的扩展, **没有mongo扩展,** 与现有版本不兼容. 此版本安装的是**php56**版

1. 首先查看已安装的版本

   ```
   yum list installed | grep php
   ```

   可以看到都是5.3的

   ```
   php.x86_64          5.3.3-49.el6        @base                                   
   php-bcmath.x86_64   5.3.3-49.el6        @base                                   
   php-cli.x86_64      5.3.3-49.el6        @base                                   
   php-common.x86_64   5.3.3-49.el6        @base                                   
   php-fpm.x86_64      5.3.3-49.el6        @base                                   
   php-gd.x86_64       5.3.3-49.el6        @base                                   
   php-mbstring.x86_64 5.3.3-49.el6        @base                                   
   php-mcrypt.x86_64   5.3.3-4.el6         @epel                                   
   php-mysql.x86_64    5.3.3-49.el6        @base                                   
   php-pdo.x86_64      5.3.3-49.el6        @base                                   
   php-pear.noarch     1:1.9.4-5.el6       @base                                   
   php-pecl-igbinary.x86_64
   php-pecl-mongo.x86_64
   php-pecl-redis.x86_64
   php-php-gettext.noarch
   php-process.x86_64  5.3.3-49.el6        @base                                   
   php-tcpdf.noarch    6.2.11-1.el6        @epel                                   
   php-tcpdf-dejavu-sans-fonts.noarch
   php-tidy.x86_64     5.3.3-49.el6        @base                                   
   php-xml.x86_64      5.3.3-49.el6        @base                                   
   phpMyAdmin.noarch   4.0.10.15-1.el6     @epel   
   ```





2. 升级之前需要写在以下旧版, 不卸载直接安装新版会提示失败.

   ```
   yum remove php php-common
   其他可删可不删
   ```

   提示删除如下内容

   ```
   Removed:
     php.x86_64 0:5.3.3-49.el6                                 php-common.x86_64 0:5.3.3-49.el6                                
   
   Dependency Removed:
     php-bcmath.x86_64 0:5.3.3-49.el6                  php-cli.x86_64 0:5.3.3-49.el6       php-fpm.x86_64 0:5.3.3-49.el6      
     php-gd.x86_64 0:5.3.3-49.el6                      php-mbstring.x86_64 0:5.3.3-49.el6  php-mcrypt.x86_64 0:5.3.3-4.el6    
     php-mysql.x86_64 0:5.3.3-49.el6                   php-pdo.x86_64 0:5.3.3-49.el6       php-pear.noarch 1:1.9.4-5.el6      
     php-pecl-igbinary.x86_64 0:1.2.1-1.el6            php-pecl-mongo.x86_64 0:1.4.4-1.el6 php-pecl-redis.x86_64 0:2.2.8-1.el6
     php-php-gettext.noarch 0:1.0.11-12.el6            php-process.x86_64 0:5.3.3-49.el6   php-tcpdf.noarch 0:6.2.11-1.el6    
     php-tcpdf-dejavu-sans-fonts.noarch 0:6.2.11-1.el6 php-tidy.x86_64 0:5.3.3-49.el6      php-xml.x86_64 0:5.3.3-49.el6      
     phpMyAdmin.noarch 0:4.0.10.15-1.el6              
   
   ```

   

3. 更新一下yum源

   ```
   centos6.x
   # rpm -Uvh http://ftp.iij.ad.jp/pub/linux/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
   # rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
   
   centos7.x
   # yum install epel-release
   # rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
   
   
   
   //下边yum源中php版本较少, 只有php5.5w, php5.6w,没有php5.5和php5.6, 带后缀w的不制止php-pecl-mongo扩展, 因此对当前项目不可用.
   Centos 5.x
   rpm -Uvh http://mirror.webtatic.com/yum/el5/latest.rpm
   
   CentOs 6.x
   rpm -Uvh http://mirror.webtatic.com/yum/el6/latest.rpm
   
   CentOs 7.X
   rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm
   rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
   
   ```

   如果想删除上面安装的包，重新安装

    rpm -qa | grep webstatic

    rpm -e  上面搜索到的包即可

4. 运行 yum install

   ```
   yum --enablerepo=remi --enablerepo=remi-php56 install php php-bcmath php-cli php-common php-fpm php-gd php-mbstring php-mcrypt php-mysql php-pdo php-process php-tidy php-xml
   ```

   ```
   yum --enablerepo=remi --enablerepo=remi-php56 install php-pecl-igbinary php-pecl-mongo php-pecl-redis  phpMyAdmin.noarch
   ```





`参考链接:`

[Centos6.5下安装PHP5.6](https://blog.csdn.net/xiaosongbk/article/details/53431292)

 [CentOS 6.5 升级 PHP 到5.6](https://www.cnblogs.com/savokiss/p/6259816.html)

 [Centos下Yum安装PHP5.5,5.6,7.0](http://www.blogjava.net/nkjava/archive/2015/01/20/422289.html)

