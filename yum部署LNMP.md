YUM部署PHP-fpm

## nginx

```
Centos7.4 安装nginx和php-fpm:

1. 安装nginx:

rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
yum install -y nginx

systemctl restart nginx

重新加载nginx配置：

nginx -s reload


./configure --prefix=/usr/local/php7.3 --with-config-file-path=/usr/local/php7.3/etc --enable-fpm   --with-curl --with-gd  --enable-intl --enable-mbstring --enable-json --enable-opcache --disable-fileinfo --with-libxml-dir --enable-xml --with-openssl --with-freetype-dir --with-jpeg-dir --with-png-dir 


```

## #php-fpm

```
要安装高版本的php-fpm,需要按如下步骤：

1）.安装epel-release
通过命令：
yum -y install epel-release

2）.安装PHP7
终端再次运行如下命令：
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

3）. yum查看
yum search php70w-fpm      #PHP7.0
yum search php71w-fpm      #PHP7.1
yum search php72w-fpm      #PHP7.2

 

4）. 安装PHP 7.0、7.1的yum源，然后再执行：

安装php 7.0以及扩展:
yum install php70w php70w-fpm php70w-cli php70w-common php70w-devel php70w-gd php70w-pdo php70w-mysql php70w-mbstring php70w-bcmath php70w-xml php70w-pecl-redis php70w-process php70w-intl php70w-xmlrpc php70w-soap php70w-ldap php70w-opcache

安装php 7.1以及扩展:
yum install php71w php71w-fpm php71w-cli php71w-common php71w-devel php71w-gd php71w-pdo php71w-mysql php71w-mbstring php71w-bcmath php71w-xml php71w-pecl-redis php71w-process php71w-intl php71w-xmlrpc php71w-soap php71w-ldap php71w-opcache

安装php 7.2以及扩展:

yum install -y php72w php72w-fpm php72w-cli php72w-common php72w-devel php72w-gd php72w-pdo php72w-mysql php72w-mbstring php72w-bcmath php72w-xml php72w-pecl-redis php72w-process php72w-intl php72w-xmlrpc php72w-soap php72w-ldap php72w-opcache

5）. 开启服务
systemctl start php-fpm

6）. 开机开启服务

systemctl enable php-fpm
```





