Linux命令

### 查看系统版本

```
命令1: cat /etc/redhat-release
命令2: lsb_release -a  
```

### yum 安装httpd

```
命令：yum install httpd
启动：systemctl restart httpd
```

###yum安装mysql(mariadb)

```
1、清除老版本
rpm -qa mariadb-libs
yum remove mariadb-libs
```

 ```
2、配置仓库
cat /etc/yum.repos.d/Mariadb.repo
内容：
# MariaDB 10.1 CentOS repository list - created 2017-02-23 08:53 UTC
# http://downloads.mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.2/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
 ```

```
3、使用yum安装
yum install mariadb-server mariadb-devel 或者 yum -y install MariaDB-server MariaDB-client
systemctl start mariadb #启动服务
systemctl enable mariadb #设置开机启动
4 进行MariaDB的相关简单配置,使用mysql_secure_installation命令进行配置
首先是设置密码，会提示先输入密码
Enter current password for root (enter for none):<–初次运行直接回车
设置密码:
Set root password? [Y/n] <– 是否设置root用户密码，输入y并回车或直接回车
New password: <– 设置root用户的密码
Re-enter new password: <– 再输入一次你设置的密码
其他配置:
Remove anonymous users? [Y/n] <– 是否删除匿名用户，回车 y
Disallow root login remotely? [Y/n] <–是否禁止root远程登录,回车, n
Remove test database and access to it? [Y/n] <– 是否删除test数据库，回车 y 
Reload privilege tables now? [Y/n] <– 是否重新加载权限表，回车 y
5. 添加用户，设置权限
创建用户命令
mysql>create user username@localhost identified by 'password';
直接创建用户并授权的命令
mysql>grant all on *.* to username@localhost indentified by 'password';
授予外网登陆权限 
mysql>grant all privileges on *.* to username@'%' identified by 'password';
授予权限并且可以授权
mysql>grant all privileges on *.* to username@'hostname' identified by 'password' with grant option;
FLUSH PRIVILEGES;
newhua@news
```



### yum安装php7

```
1、更新yum源
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
```

```
2、卸载之前安装
yum -y remove php*
```

```
3、安装php72w和各种扩展
yum -y install php72w
```

```
默认扩展
yum install php72w-common php72w-fpm php72w-opcache php72w-gd php72w-mysqlnd php72w-mbstring php72w-pecl-redis php72w-pecl-memcached php72w-devel php72w-cli php72w-common php72w-dba php72w-devel php72w-embedded php72w-enchant  php72w-fpm php72w-gd
php72w-imap  php72w-interbase php72w-intl php72w-ldap php72w-mbstring php72w-mcrypt
php72w-odbc php72w-mysql php72w-mysqlnd php72w-opcache php72w-pdo php72w-pdo_dblib
php72w-pear  php72w-pecl-apcu php72w-pecl-imagick php72w-pecl-memcached php72w-pecl-mongodb php72w-pecl-redis php72w-pecl-xdebug php72w-pgsql php72w-phpdbg  php72w-process  php72w-pspell php72w-recode php72w-snmp php72w-soap  php72w-tidy  php72w-xml  php72w-xmlrpc
```

```
安装包         提供的拓展
php70w          mod_php , php70w-zts
php70w-bcmath       
php70w-cli      php-cgi, php-pcntl, php-readline
php70w-common   php-api, php-bz2, php-calendar, php-ctype, php-curl, php-date, php-exif, php-fileinfo, php-filter, php-ftp, php-gettext, php-gmp, php-hash, php-iconv, php-json, php-libxml, php-openssl, php-pcre, php-pecl-Fileinfo, php-pecl-phar, php-pecl-zip, php-reflection, php-session, php-shmop, php-simplexml   , php-sockets, php-spl, php-tokenizer, php-zend-abi, php-zip, php-zlib 
php70w-dba      
php70w-devel        
php70w-embedded     php-embedded-devel
php70w-enchant      
php70w-fpm      
php70w-gd       
php70w-imap     
php70w-interbase        php_database, php-firebird
php70w-intl     
php70w-ldap     
php70w-mbstring     
php70w-mcrypt       
php70w-mysql        php-mysqli, php_database
php70w-mysqlnd      php-mysqli, php_database
php70w-odbc     php-pdo_odbc, php_database
php70w-opcache      php70w-pecl-zendopcache
php70w-pdo      php70w-pdo_sqlite, php70w-sqlite3
php70w-pdo_dblib        php70w-mssql
php70w-pear     
php70w-pecl-apcu    
php70w-pecl-imagick 
php70w-pecl-memcached   
php70w-pecl-mongodb 
php70w-pecl-redis   
php70w-pecl-xdebug  
php70w-pgsql        php-pdo_pgsql, php_database
php70w-phpdbg       
php70w-process      php-posix, php-sysvmsg, php-sysvsem, php-sysvshm
php70w-pspell       
php70w-recode       
php70w-snmp     
php70w-soap     
php70w-tidy     
php70w-xml      php-dom, php-domxml, php-wddx, php-xsl
php70w-xmlrpc
```

命令：

```
1、php 扩展文件
php --ini
2、PHP 查看扩展
php -m
```



