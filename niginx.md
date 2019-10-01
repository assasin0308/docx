## Nginx---高性能HTTP和反向代理服务器

### 1. 编译与安装

```
官网 : http://nginx.org
下载 : wget http://nginx.org/download/nginx-1.15.12.tar.gz
解压 : tar zxvf nginx-1.15.12.tar.gz
进入目录: cd nginx-1.15.12
./configure --prefix=/usr/local/nginx  (若出现错误安装: yum install pcre pcre-devel ) --add-module=../nginx-rtmp-module-1.2.1 (前提是先下载nginx-rtmp-module作为直播模块	https://github.com/arut/nginx-rtmp-module wget https://codeload.github.com/arut/nginx-rtmp-module/tar.gz/v1.2.1
tar -zxvf v1.2.1
//或者
wget https://github.com/arut/nginx-rtmp-module/archive/v1.2.1.tar.gz
tar -zxvf v1.2.1.tar.gz)
编译安装: make && make install
nginx目录介绍:
...conf 配置文件
...hmtl 网页目录
...logs 日志文件
...sbin 主要二进制程序
启动Nginx:  ./sbin/nginx (80端口不能被占用)
```

### 2. Nginx整合PHP

```
nginx+PHP编译
注意: ./configure --prefix=/usr/local/fastphp7.0 \
--with-config-file-path=/usr/local/fastphp7.0/etc \
--with-mysql=mysqlnd \
--enable-mysqlnd \
--with-gd \
--enable-gd-native-ttf \
--enable-gd-jis-conv \
--enable-fpm   #作为独立进程
--with-apxs2=/usr/local/httpd/bin/apxs #作为Apache的一个模块
--with-config-file-path=/usr/local/php/etc 
--with-mysqli --with-pdo-mysql 
--with-iconv-dir 
--with-freetype-dir 
--with-jpeg-dir 
--with-png-dir 
--with-zlib 
--with-libxml-dir
--enable-simplexml 
--enable-xml 
--disable-rpath 
--enable-bcmath --enable-soap --enable-zip --with-curl --enable-fpm --with-fpm-user=www --with-fpm-group=www --enable-mbstring --enable-sockets --with-gd --with-openssl --with-mhash --enable-opcache --disable-fileinfo
而nginx则是把http请求变量(如get,user_agent)转发给PHP进程,PHP独立进程,与nginx进行通讯,称为fastcgi运行方式.
location ~ \.php$ {
            root           html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
            include        fastcgi_params;
        }
```

### 3. 信号控制

```
https://www.nginx.com/resources/wiki/start/topics/tutorials/commandline/
```

```
nginx的信号控制:  kill -信号选项 主进程pid(`cat logs/nginx.pid`)
1. TERM,INT 快速杀死
2. QUIT  优雅的关闭进程,即等请求结束再关闭进程
3. HUP   改变配置文件,平滑的重读配置文件
4. USR1  重读日志,在日志按月/日分割时有用
5. USR2  平滑的升级
6. WINCH  优雅的关闭旧的进程
```

### 4. 虚拟主机配置

```
<nginx.conf>

#user  nobody;
worker_processes  1;  //有一个工作的子进程,可自行修改但太大无益,一般为CPU数*核数

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
//一般配置nginx链接的特性
    worker_connections  1024; 一个子进程最大允许连接1024个连接
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;
        
        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}

```

```
1. 基于域名的虚拟主机
server{
      listen    80;
      server_name:    z.com;
      
      location / {
          root z.com;   //根目录可相对(nginx根目录)
          index  index.html;
      }
}
```

```
2. 基于端口的虚拟主机
server {
    lsiten   2022;
    server_name    z.com;
    
    location / {
        root /var/www/html;
       index index.html;
    }
}
```

```
3. 基于IP的虚拟主机
server {
   listen   80 ;
   server_name  192.168.31.95;
   
   location / {
       root html/ip;   //ip目录
       index index.html
   }
}
```

### 5. 日志管理 

```
 http://nginx.org/en/docs/http/ngx_http_log_module.html
 nginx server段:
 #access_log  logs/access.log  main;
 说明该server访问的日志文件是access.log,使用的格式是 `main`格式,
 main格式:
 log_format  main    '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
 远程IP- 远程用户/用户时间 请求方法(如GET/POST) 请求体body长度 referer来源信息
  http-user-agent 用户代理/蜘蛛 ,被转发的请求的原始IP
  http_x_forwarded_for:在经过代理时,代理把你的本来IP加在此头信息中,传输你的原始IP


除main格式外,可自定义其他格式.nginx允许对不同的server做不同的Log:
server{
      listen    80;
      server_name:    z.com;
      
      location / {
          root z.com;   //根目录可相对(nginx根目录)
          index  index.html;
      }
      access_log  logs/z.com.access.log  main;
}
```

```
定时任务+日志切割  以z.com为例:
server{
      listen    80;
      server_name:    z.com;
      
      location / {
          root z.com;   //根目录可相对(nginx根目录)
          index  index.html;
      }
      access_log  logs/z.com.access.log  main;
}

shell脚本 log.sh
    #!/bin/bash
    LOGPATH=/usr/local/nginx/logs/z.com.access.log
    BASEPATH=/data/$(date -d yeaterday +%Y%m)
    mkdir -p $BASEPATH/
    bak = $BASEPATH/$(date -d yesterday +%d%H%M).zcom.access.log
    mv $LOGPATH  $bak
    touch $LOGPATH
    kill -USR1  `cat /usr/local/nginx/logs/nginx.pid`

```

### 6.location匹配

```
语法: location [=|~|~*|^~] patt {

       }
分为三中类型:
location = patt {} [精准匹配]
location  patt{}   [一般匹配]
location ~ patt{}  [正则匹配]
发挥作用的方式:
首先看有没有精准匹配,如果有,则停止匹配过程.
location = patt {
    config A
}
如果 $uri == patt,匹配成功，使用configA
   location = / {
              root   /var/www/html/;
             index  index.htm index.html;
        }
         
  location / {
             root   /usr/local/nginx/html;
             index  index.html index.htm;
  }
 如果访问　　http://xxx.com/
 1: 精准匹配中　”/”   ,得到index页为　　index.htm
 2: 再次访问 /index.htm , 此次内部转跳uri已经是”/index.htm” , 根目录为/usr/local/nginx/html
 3: 最终结果,访问了 /usr/local/nginx/html/index.htm 
 
 正则匹配
 location / {
            root   /usr/local/nginx/html;
            index  index.html index.htm;
        }

location ~ image {
           root /var/www/image;
           index index.html;
		}
如果访问  http://xx.com/image/logo.png
此时, “/” 与”/image/logo.png” 匹配
同时,”image”正则 与”image/logo.png”也能匹配,谁发挥作用?
正则表达式将会发挥作用  图片真正会访问 /var/www/image/logo.png

location / {
             root   /usr/local/nginx/html;
             index  index.html index.htm;
         }
 
location /foo {
            root /var/www/html;
             index index.html;
}
访问 http://xxx.com/foo
对于uri “/foo”,   两个location的patt,都能匹配他们
即 ‘/’能从左前缀匹配 ‘/foo’, ‘/foo’也能左前缀匹配’/foo’,
此时, 真正访问 /var/www/html/index.html 
原因:’/foo’匹配的更长,因此使用之
```

```
location的命中过程:
1.先精准命中,如果命中,立即返回结果并结束解析过程;
2.判断普通命中,如果有多个命中,记录寄来'最长'的命中结果(记录但不结束);
3.继续判断正则表达式的解析结果,按照配置的正则表达式顺序为准,由上到下开始匹配,一旦成功匹配1个,立即返回结果,并结束解析过程.
普通命中,顺序不影响命中结果,因为命中结果是按照命中的长短来确定的;
正则表达式命中,顺序影响命中结果,因为命中结果是从前往后命中的.
```

### 7. Rewrite 重写

```
常用命令:
if 空格 (条件) {
  重写模式
} 
设定条件,再进行重写 
条件语法:
1: “=”来判断相等, 用于字符串比较
2: “~” 用正则来匹配(此处的正则区分大小写)
   ~* 不区分大小写的正则
3: -f -d -e来判断是否为文件,为目录,是否存在.
set #设置变量
return #返回状态码 
break #跳出rewrite
rewrite #重写
```

```
 示例1:
 location / {
            if ($remote_addr = 192.168.31.95 ){
              return 403;
            }
            root   /usr/local/nginx/html;
            index  index.html index.htm;
        }
  示例2:
  location / {
            if ($http_user_agent ~ MSIE) {
                rewrite ^.*$ /ie.htm;
                break; #(不break会循环重定向)
             }
            root   /usr/local/nginx/html;
            index  index.html index.htm;
        }
  示例3:
  location / {
            if (!-e $document_root$fastcgi_script_name) {
                rewrite ^.*$ /404.html break;
             } 
            root   /usr/local/nginx/html;
            index  index.html index.htm;
        }
以 xx.com/dsafsd.html这个不存在页面为例,
日志中显示的访问路径,依然是GET /dsafsd.html HTTP/1.1
提示: 服务器内部的rewrite和302跳转不一样. 
跳转的话URL都变了,变成重新http请求404.html, 而内部rewrite, 上下文没变,
就是说 fastcgi_script_name 仍然是 dsafsd.html,因此会循环重定向.

set 是设置变量用的, 可以用来达到多条件判断时作标志用
达到apache下的 rewrite_condition的效果
location / {
            if ($http_user_agent ~* msie){
                set $isie 1;
            }
            if ($fastcgi_script_name = ie.html) {
                set $isie 0;
            }
            if ($isie 1) {
                rewrite ^.*$ ie.html;
            }
            root   /usr/local/nginx/html;
            index  index.html index.htm;
        }
```

### 8. url重写 

```
location /ecshop {
            index index.php;
            rewrite good-(\d{1,9})\.html   /ecshop/goods.php?id=$1;
            rewrite article-([\d]+)\.html$ /ecshop/article.php?id=$1;
            rewrite category-(\d+)-b(\d+)\.html /ecshop/category.php?id=$1&brand=$2;
            rewrite category-(\d+)-b(\d+)-min(\d+)-max(\d+)-attr([\d\.]+)\.html          /ecshop/category.php?id=$1&brand=$2&price_min=$3&price_max=$4&filter_attr=$5;
            rewrite category-(\d+)-b(\d+)-min(\d+)-max(\d+)-attr([\d+\.])-(\d+)-([^-]+)-([^-]+)\.html /ecshop/category.php?id=$1&brand=$2&price_min=$3&price_max=$4&filter_attr=$5&page=$6&sort=$7&order=$8;
            注意:用url重写时, 正则里如果有”{}”,正则要用双引号包起来
        }
```

### 9.Gzip压缩 

```
原理: 
浏览器---请求----> 声明可以接受 gzip压缩 或 deflate压缩 或compress 或 sdch压缩
从http协议的角度看--请求头 声明 acceopt-encoding: gzip deflate sdch  (是指压缩算法,其中sdch是google倡导的一种压缩方式,目前支持的服务器尚不多)
服务器-->回应---把内容用gzip方式压缩---->发给浏览器
浏览<-----解码gzip-----接收gzip压缩内容----
```

```
gzip配置的常用参数:
gzip on|off;  #是否开启gzip
gzip_buffers 32 4K| 16 8K #缓冲(压缩在内存中缓冲几块? 每块多大?)
gzip_comp_level [1-9] #推荐6 压缩级别(级别越高,压的越小,越浪费CPU计算资源)
gzip_disable #正则匹配UA 什么样的Uri不进行gzip
gzip_min_length 200 # 开始压缩的最小长度字节(再小就不要压缩了,意义不在)
gzip_http_version 1.0|1.1 # 开始压缩的http协议版本(可以不设置,目前几乎全是1.1协议)
gzip_proxied          # 设置请求者代理服务器,该如何缓存内容
gzip_types text/plain  application/xml # 对哪些类型的文件用压缩 如txt,xml,html ,css
gzip_vary on|off  # 是否传输gzip压缩标志
```

```
示例:
 #gzip  on;

    server {
        listen       80;
        server_name  localhost;
        
        gzip on;
        gzip_buffers 32 4K;
        gzip_comp_level 6;
        gzip_min_length 4000;
        gzip_types text/css text/xml application/javascript; #(conf/mime.types)

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index index.php  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        location ~ \.php$ {
            root           html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
            include        fastcgi_params;
        }

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny all;
        #}
    }

```

```
注意: 
图片/mp3这样的二进制文件,不必压缩
因为压缩率比较小, 比如100->80字节,而且压缩也是耗费CPU资源的
```

### 10.Expires缓存 

```
nginx中设置过期时间:在location或if段里
格式: expires 30s;
      expires 30m;
      expires 2h;
      expires 30d;
location ~* \.(jpg|jpeg|gif|png) {
      #root  html/image;
      expires 1d;
}
```

```
缓存原理:
服务器响应文件内容时,同时响应etag标签(内容的签名,内容一变,他也变), 和 last_modified_since 2个标签值
浏览器下次去请求时,头信息发送这两个标签, 服务器检测文件有没有发生变化,若无变化,直接头信息返回 etag,last_modified_since浏览器知道内容无改变,于是直接调用本地缓存.
这个过程,也请求了服务器,但是传输的内容极少.对于变化周期较短的,如静态html,js,css,比较适于用这个方式
```

### 11. 反向代理实现Nginx+Apache动静分离 

```
支持两个用法 : proxy, upstream,分别用来做反向代理和负载均衡
1.apache 8080端(假设ip是192.168.1.200:8080),处理PHP文件
2. nginx配置代理: 处理js,css文件
location ~ \.php$ {
          proxy_set_header X-Forwarded-For $remote_addr; 带着客户端的IP
          proxy_pass 127.0.0.1:8080; #单台服务器,也可指向多台服务器
          #  root           html;
          #  fastcgi_pass   http://192.168.1.200:8080;
          #  fastcgi_index  index.php;
          #  fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
          #  include        fastcgi_params;
        }
```

### 12. 负载均衡 

```
具体实现:反向代理后端如果有多台服务器,把多台服务器用 upstream指定绑定在一起并起个组名,然后proxy_pass指向该组即可.
假设有两台服务器(专门存放图片信息): 192.168.1.200
upstream imageserver {
  server  192.168.1.200:81 weight=1 max_fails=2 fail_timeout=3;
  server  192.168.1.200:82 weight=1 max_fails=2 fail_timeout=3;
}
server {
   listen 81;
   server_name localhost;
   root html;
   access_log logs/81-access.log main;
}
server {
   listen 82;
   server_name localhost;
   root html;
   access_log logs/82-access.log main;
}

location ~* \.(jpg|jpeg|gif|png)$ {
      proxy_set_header X-Forwarded-For $remote_addr; 带着客户端的IP
      proxy_pass http://imageserver;
}
默认的均衡的算法就是针对后端服务器的顺序,逐个请求.
其他负载均衡算法,如一致性哈希,需要安装第三方模块 ngx_http_upstream_consistent_hash
```

### 13. Memcached

```
nginx连接:
location / {
          set $memcached_key "$uri";
          memcached_pass 127.0.0.1:11211;
          error_page 404  /callback.php;
           # root   html;
           # index  index.html index.htm;
        }
```

```
PHP连接:
$mem = new Memcached();
$mem->addServer('127.0.0.1',11211);
```

```
<?php
//user2345.html
//获取URI作为key
$uri = $_SERVER['REQUEST_URI];
//分析URI中的uid
$uid = substr($uri,5,strpos($uri,'.')-5);

//链接数据库,查询并写入memcache
$conn = mysql_connect('localhost','root','pwd');
$sql = 'use test';
mysql_query($sql,$conn);
$sql ='set charset utf8';
mysql_query($sql,$conn);
$sql = "select * from user where uid=$uid";
$res = mysql_query($sql,$conn);
$user = mysql_fetch_assoc($res);
if(empty($user)){
  echo "no this user";
}else{
  print_r($user);
  //链接Memcache
  $mem = new memcache();
  $mem->connect('127.0.0.1',11211);
  $mem->add($uri,$user,0,300);
  $mem->close();
}
```

### 14. ngx_http_upstream_consistent_hash(哈希一致)

```
nginx编译第三方模块:  ./configure --prefix=/xxx/xxx --add_module=/path/ngx_module
编译安装: make && make install
```

```
安装后 Nginx.conf 
upsream memserver {
   consistent_hash $request_uri;
   server 127.0.0.1:11211;
   server 127.0.0.1:11212;
   server 127.0.0.1:11213;
}
location / {
          set $memcached_key "$uri";
          memcached_pass memserver;
          error_page 404  /callback.php;
           # root   html;
           # index  index.html index.htm;
        }
```

```
<?php
//user2345.html
//获取URI作为key
$uri = $_SERVER['REQUEST_URI];
//分析URI中的uid
$uid = substr($uri,5,strpos($uri,'.')-5);

//添加多台服务器(同nginx)
$mem = new memcache();
$mem->connect('127.0.0.1',11211);
$mem->connect('127.0.0.1',11212);
$mem->connect('127.0.0.1',11213);


//链接数据库,查询并写入memcache
$conn = mysql_connect('localhost','root','pwd');
$sql = 'use test';
mysql_query($sql,$conn);
$sql ='set charset utf8';
mysql_query($sql,$conn);
$sql = "select * from user where uid=$uid";
$res = mysql_query($sql,$conn);
$user = mysql_fetch_assoc($res);
if(empty($user)){
  echo "no this user";
}else{
  print_r($user);
 //哈希一致策略存储
  $mem->add($uri,$user,0,300);
  $mem->close();
}
```

```
php.ini 配置

memcache.hash_strategy = consistent
```

```
注意:
upstream 做负载均衡时 使用 127.0.0.1 而不是 localhost!
```

### 15. 单机nginx压力测试 

```
现4台服务器:
服务器A : 192.168.1.201
服务器B : 192.168.1.202 压力测试
服务器C : 192.168.1.203
服务器D : 192.168.1.204
```

```
对于请求量大的高性能网站,如何支撑?
1.尽可能要减少请求,对于开发人员,如:合并css, 背景图片, 减少mysql查询等;
2.对于运维,使用nginx的expires,利用浏览器缓存等,减少查询;
3.利用cdn来响应请求;
4.最后,不可避免的请求,则使用服务器集群+负载均衡来支撑.
```

```
nginx统计模块 : http_stub_status_module
./configure --prefix=/usr/local/nginx/ --add-module=/app/ngx_http_consistent_hash-master --with-http_stub_status_module

location /status {
     stub_status on;
     access_log off;
     allow 192.168.1.100;
     deny all;
}
```

### 高并发思路:

```
一 .Socket方面
系统层面: 
1. 洪水攻击: 不做洪水抵御
2.最大连接数:somaxconn
3.加快TCP链接的回收
4.空的TCP是否允许回收利用 reuse
nginx层面: 每个子进程允许打开的链接 worker_connections
```

```
二.文件方面
系统层面:
 ulimit -n 加大
nginx层面:
每个子进程允许打开的链接 worker_connections
```

具体配置:

```
nginx 配置 worker_connections
events {
    worker_connections 10240;
}
worker_processes 1;
worker_rlimit_nofile 10000; #1个工作进程允许打开多少文件
keepalive_timeout 0; 高并发网站中,http连接快速关闭,加快TCP回收
```

```
系统层面: 1.socket优化  /proc/sys/net/core/somaxconn
修改: echo 50000 > /proc/sys/net/core/somaxconn
          2.加快TCP回收  /proc/sys/net/ipv4/tcp_tw_recycle
修改: echo 1 > /proc/sys/net/ipv4/tcp_tw_recycle
          3.空的TCP回收利用  /proc/sys/net/ipv4/tcp_tw_reuse
修改: echo 1 > /proc/sys/net/ipv4/tcp_tw_reuse
          4.不做洪水抵御 /proc/sys/net/ipv4/tcp_syncookies
修改: echo 0 > /proc/sys/net/ipv4/tcp_syncookies
```

```
或脚本优化:
tcpupgrade.sh
#!/bin/bash
echo 50000 > /proc/sys/net/core/somaxconn
echo 1 > /proc/sys/net/ipv4/tcp_tw_recycle
echo 1 > /proc/sys/net/ipv4/tcp_tw_reuse
echo 0 > /proc/sys/net/ipv4/tcp_syncookies
```

### 16. nginx服务器集群  

```
现4台服务器:
服务器A : 192.168.1.201 MySQL
服务器B : 192.168.1.202 nginx静态缓存 memcache集群
服务器C : 192.168.1.203 MySQL+PHP
服务器D : 192.168.1.204 Memcache
```

```
服务器C  192.168.1.203  运行PHP  四个端口  9001 | 9002 | 9003 | 9004
nginx来访问fpm时, fpm的进程要是不够用, 会生成子进程.生成子进程需要内核来调度,比较耗时,如果网站并发比较大, 我们可以用静态方式一次性生成若干子进程,保持在内存中.
php-fpm   php-fpm.conf配置  php-fpm9001.conf php-fpm9002.conf php-fpm9003.conf  php-fpm9004.conf
pm = static   #让fpm进程始终保持,不要动态生成
pm.max_children = 16  #始终保持的子进程数量
```

```
启动php-fpm指定配置文件
start.sh 脚本批量运行
#!/bin/bash
/usr/local/php/sbin/php-fpm  -y   /usr/local/php/etc/php-pfm.conf
/usr/local/php/sbin/php-fpm  -y   /usr/local/php/etc/php-pfm9001.conf
/usr/local/php/sbin/php-fpm  -y   /usr/local/php/etc/php-pfm9002.conf
/usr/local/php/sbin/php-fpm  -y   /usr/local/php/etc/php-pfm9003.conf
/usr/local/php/sbin/php-fpm  -y   /usr/local/php/etc/php-pfm9004.conf```
```

```
服务器C  192.168.1.203  /var/www目录下
php.ini 配置 哈希一致  memcache.hash_strategy = consistent
```

```
服务器C  192.168.1.203  /var/www目录下
callback.php
<?php
$mem = new Memcached();
$mem->addServer('192.168.1.204',11211);
$mem->addServer('192.168.1.204',11212);
$mem->addServer('192.168.1.204',11213);
$mem->addServer('192.168.1.204',11214);
$mem->addServer('192.168.1.204',11215);
$mem->addServer('192.168.1.204',11216);
$mem->addServer('192.168.1.204',11217);
$mem->addServer('192.168.1.204',11218);
$uri = $_SERVER['REQUEST_URI'];
if($uri =='/index.html'){
	$cont = "this is index.html";
	$mem->add($uri,$cont,false,300);
	echo $cont;
} else if(substr($uri, 1,3) == 'com'){
	$comid = substr($uri, 4,-5);
	echo "you want to see ".$comid."company";
	$conn = mysql_connect('192.168.1.201','root','pwd');
	if(!$conn){
		exit('mysql connect failed');
	}
	$sql = "use big_data";
	mysql_query($sql,$conn);
	$sql = "set names utf8";
	mysql_query($sql,$conn);
	$sql = "select id,name,address from lx_com where id = ".$comid;
	$res = mysql_query($sql,$conn);
	$info = mysql_fetch_assoc($res);
	if(!empty($info)){
		echo "no this company";
		exit;
	}
	$cont = '';
	$cont = '<h1>'.$info['name'].'</h1>';
	$cont .= '<h2>'.$info['address'].'</h2>';
	$mem->add($uri,$cont,0,300);
	echo $cont;
}
echo " ~ ~ from mysql";
```



```
服务器B  192.168.1.202 nginx.conf 配置
upstream memserver {
   consistent_hash $request_uri;
   server  192.168.1.204:11211;
   server  192.168.1.204:11212;
   server  192.168.1.204:11213;
   server  192.168.1.204:11214;
   server  192.168.1.204:11215;
   server  192.168.1.204:11216;
   server  192.168.1.204:11217;
   server  192.168.1.204:11218;
}
upstream phpserver {
  server  192.168.2.203:9000;
  server  192.168.2.203:9001;
  server  192.168.2.203:9002;
  server  192.168.2.203:9003;
  server  192.168.2.203:9004;
}
location / {
     set memcached_key $request_uri;
     memecahced_pass memserver;
     error_page 404 = /callback.php  #服务器C  192.168.1.203  /var/www目录下
     #root html;
     #index index.html index.htm;
}
location ~ \.php$ {
            root           /var/www; #服务器C  192.168.1.203  /var/www目录下
            fastcgi_pass   phpserver;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }
```

### 17. 集群性能测试  

```
测试脚本: 前10万条为热门数据  
客户端: 服务器A : 192.168.1.201 MySQL 
请求: 192.168.1.202
<?php
$mem = new Memcached();
$mem->addServer('192.168.1.204',11211,true); # true:持久连接
$mem->addServer('192.168.1.204',11212,true);
$mem->addServer('192.168.1.204',11213,true);
$mem->addServer('192.168.1.204',11214,true);
$mem->addServer('192.168.1.204',11215,true);
$mem->addServer('192.168.1.204',11216,true);
$mem->addServer('192.168.1.204',11217,true);
$mem->addServer('192.168.1.204',11218,true);
$min = 15422432;
if(!mt_rand(0,100) <= 90){
	$comid = $min + mt_rand(0,100000);
}else{
	$comid = mt_rand(0,$min);
}
if( !($cont = $mem->get('com'.$comid) )){
	$conn = mysql_connect('192.168.1.201','root','pwd');
	$sql = "use big_data";
	mysql_query($sql,$conn);
	$sql = "set names utf8";
	mysql_query($sql,$conn);
	$sql = "use big_data";
		mysql_query($sql,$conn);
		$sql = "set names utf8";
		mysql_query($sql,$conn);
		$sql = "select id,name,address from lx_com where id = ".$comid;
		$res = mysql_query($sql,$conn);
		$info = mysql_fetch_assoc($res);
		if(!empty($info)){
			echo "no this company";
			exit;
		}
		$cont = '';
		$cont = '<h1>'.$info['name'].'</h1>';
		$cont .= '<h2>'.$info['address'].'</h2>';
		$mem->add('com'.$comid,$cont,0,300);
		echo $cont;
}else{
  echo $cont;
}
```



###   

###   

