# centos7.4 安装LAMP环境(流水账)

[TOC]

摘要:

LAMP指的Linux操作系统 + Apache服务器 + MariaDB/MySQL数据库软件 + PHP开发语言的第一个字母。

### 1. 安装Apache

1. 安装httpd, 其中httpd-devel可选

   ```
   yum install httpd httpd-devel
   ```

2. 启动Apache

   ```
   systemctl start httpd.service
   ```

3. 设置httpd开机自启

   ```
   systemctl enable httpd
   ```

4. 查看服务器状态

   ```
   systemctl status httpd
   ```

5. 查看是否安装httpd

   ```
   rmp -qa | grep httpd
   ```

6. 防火墙开启时, 需要打开80端口

   ```
   firewall-cmd --permanent --zone=public --add-service=http
   firewall-cmd --permanent --zone=public --add-service=https
   firewall-cmd --reload
   //确认80端口监听中
   netstat -tulp
   ```

apache配置文件在/etc/httpd/conf.d/目录

apache默认的主配置文件etc/httpd/conf/httpd.conf



### 2. 安装Mariadb

1. 安装mariadb

   ```
   yum install mariadb-sever mariadb
   ```

2. 启动mariadb

   ```
   systemctl start mariadb
   ```

3. 设置mariadb开机自启

   ```
   systemctl enable mariadb
   ```

4. 对数据库进行配置

   ```
   mysql_secure_installation
   ```

   ```scss
   Enter current password for root (enter for none):  输入当前的root密码(默认空)，直接回车
   Set root password? [Y/n] 设置新密码，y
   Remove anonymous users? [Y/n] 移除匿名用户，y
   Disallow root login remotely? [Y/n] 禁止root用户远程登录，y
   Remove test database and access to it? [Y/n] 移除测试数据库，y
   Reload privilege tables now? [Y/n] y
   ```

5. mariadb配置文件路径

   ```
   /etc/my.cnf
   /etc/my.cnf.d
   ```

6. 创建子用户并赋权

   ```
   建用户命令
   mysql>create user username@localhost identified by 'password';

   直接创建用户并授权的命令
   mysql>grant all on *.* to username@localhost identified by 'password';

   授予外网登陆权限 
   mysql>grant all privileges on *.* to username@'%' identified by 'password';

   授予权限并且可以授权
   mysql>grant all privileges on *.* to username@'hostname' identified by 'password' with grant option;
   ```

   调整权限后, 通常需要执行以下语句刷新权限

   ```
   flush privileges
   ```

   删除刚才创建的用户

   ```
   drop user username@localhost;
   ```



### 3. 安装php7.2

1. centos中默认的php版本是php5.4, 因此需要手动更新rpm

```
# yum install epel-release
# rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
```

2. 安装php72及其扩展

   ```
   yum --enablerepo=remi --enablerepo=remi-php72 install php php-bcmath php-cli php-common php-fpm php-gd php-mbstring php-mcrypt php-mysql php-pdo php-process php-tidy php-xml php-pecl-igbinary php-pecl-mongodb php-pecl-redis
   ```

```
安装PHP需要的相关依赖包
yum apt-get install gcc gcc-c++ libmcrypt-devel mcrypt mhash gd-devel ncurses-devel libxml2-devel bzip2-devel libcurl-devel curl-devel libjpeg-devel libpng-devel freetype-devel net-snmp-devel openssl-deve python-devel zlib-devel freetype libxslt* bison autoconf re2c
以下是编译执行:
 ./configure \

--prefix=/usr/local/php \

--exec-prefix=/usr/local/php \

--bindir=/usr/local/php/bin \

--sbindir=/usr/local/php/sbin \

--includedir=/usr/local/php/include \

--libdir=/usr/local/php/lib/php \

--mandir=/usr/local/php/php/man \

--with-config-file-path=/usr/local/php/etc \

--with-mysql-sock=/var/run/mysql/mysql.sock \

--with-mhash \

--with-openssl \

--with-mysqli=shared,mysqlnd \

--with-pdo-mysql=shared,mysqlnd \

--with-gd \

--with-iconv \

--with-zlib \

--enable-zip \

--enable-inline-optimization \

--disable-debug \

--disable-rpath \

--enable-shared \

--enable-xml \

--enable-bcmath \

--enable-shmop \

--enable-sysvsem \

--enable-mbregex \

--enable-mbstring \

--enable-ftp \

--enable-pcntl \

--enable-calendar \

--enable-exif \

--enable-sockets \

--with-xmlrpc \

--with-libxml-dir \

--enable-soap \

--without-pear \

--with-gettext \

--enable-session \

--with-curl \

--with-jpeg-dir \

--with-png-dir \

--with-freetype-dir \

--with-bz2 \

--enable-opcache \

--enable-fpm \

--with-fpm-user=nginx \

--with-fpm-group=nginx \

--without-gdbm \

--enable-fast-install \

--disable-fileinfo

make && make install 
```

