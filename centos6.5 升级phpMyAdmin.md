# centos6.5 升级phpMyAdmin

[TOC]

`前言:` 系统配置 centos6.5 mysql 5..73 , apache2.4 升级至phpMyAdmin4.8.2-1. 



##一 自定义安装升级

下载最新版4.8.2并上传至服务器, 解压到对应文件夹, 分配好apache权限即可

如果服务器mysql版本低于mysql5.5

则需修改以下内容

1. in common.inc.php delete these lines 

common.inc.php 自定义安装路径:phpMyAdmin/libraries/common.inc.php

```
if (PMA_MYSQL_INT_VERSION < $cfg['MysqlMinVersion']['internal']) {
PMA_fatalError(
__('You should upgrade to %s %s or later.'),
array('MySQL', $cfg['MysqlMinVersion']['human'])
);
}
```

2. in DatabaseInterface.php delete these lines

DatabaseInterface.php 自定义安装路径:phpMyAdmin/libraries/classes/DatabaseInterface.php

```
 if (! empty($locale)) {
$this->query(
"SET lc_messages = '" . $locale . "';",
$link,
self::QUERY_STORE
);
}
```

 



##二. yum方式升级

### 1. 移除旧版本

```scss
yum remove phpMyAdmin
```

### 2. 安装新版本

```scss
//由于之前升级过yum源, 执行安装命令时需配置repo
//更新yum源
//centos6.x
# rpm -Uvh http://ftp.iij.ad.jp/pub/linux/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
# rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
更新完后查看phpMyAdmin版本
yum list phpMyAdmin*
//如下
yum --enablerepo=remi --enablerepo=remi-php56 install  phpMyAdmin.noarch
//安装完成后需重启apache
```

### 3.排查错误

3.1 安装完成后第一次登录报访问权限错误, 如下

```scss
Forbidden
You don't have permission to access /phpMyAdmin on this server.
```

  修改/etc/httpd/conf.d/phpMyAdmin.conf文件中访问权限, 注释掉Deny from all即可. 如下

```scss
<Directory /usr/share/phpMyAdmin/>
   AddDefaultCharset UTF-8

   <IfModule mod_authz_core.c>
     # Apache 2.4
     Require local
   </IfModule>
   <IfModule !mod_authz_core.c>
     # Apache 2.2
     Order Deny,Allow
     #Deny from All
     Allow from 127.0.0.1
     Allow from ::1
   </IfModule>
</Directory>
```

3.2 登录完成后报错如下:

```scss
#1193 - Unknown system variable 'lc_messages' when trying to login to phpmyadmin
错误原因是mysql版本和phpmyadmin版本不匹配
I faced the same problem. Check your mySQL version:
For mySQL 5.5, use phpMyAdmin 4.4.x and above
For mySQL 5.1, use phpMyAdmin 4.0.x
Hope this helps someone.
```

错误解决方案:

1. 升级mysql至5.5+

2. 修改phpmyadmin源码

   第二种方案实现

   - in common.inc.php delete these lines 

     common.inc.php yum安装路径:/usr/share/phpMyAdmin/libraries/common.inc.php

     ​				自定义安装路径:phpMyAdmin/libraries/common.inc.php

   ```
   if (PMA_MYSQL_INT_VERSION < $cfg['MysqlMinVersion']['internal']) {
   PMA_fatalError(
   __('You should upgrade to %s %s or later.'),
   array('MySQL', $cfg['MysqlMinVersion']['human'])
   );
   }
   ```

   - in DatabaseInterface.php delete these lines

     DatabaseInterface.php yum安装路径:/usr/share/phpMyAdmin/libraries/classes/DatabaseInterface.php

     ​				自定义安装路径:phpMyAdmin/libraries/classes/DatabaseInterface.php

   ```
    if (! empty($locale)) {
   $this->query(
   "SET lc_messages = '" . $locale . "';",
   $link,
   self::QUERY_STORE
   );
   }
   ```

    

参考链接:

[1193 - Unknown system variable 'lc_messages' when trying to login to phpmyadmin](https://www.e-learn.cn/content/wangluowenzhang/75333)

