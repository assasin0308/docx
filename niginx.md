## Nginx---高性能HTTP和反向代理服务器

### 1. 编译与安装

```
官网 : http://nginx.org
下载 : wget http://nginx.org/download/nginx-1.15.12.tar.gz
解压 : tar zxvf nginx-1.15.12.tar.gz
进入目录: cd nginx-1.15.12

./configure --prefix=/usr/local/nginx --with-select_module  --without-select_module --with-poll_module  --without-poll_module --with-threads --with-file-aio  --with-http_ssl_module  --with-http_v2_module    --with-http_realip_module   --with-http_addition_module --with-http_xslt_module  --with-http_geoip_module      --with-http_geoip_module=dynamic --with-http_sub_module   --with-http_dav_module        --with-http_flv_module        --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_auth_request_module  --with-http_random_index_module --with-http_secure_link_module  --with-http_degradation_module --with-http_slice_module --with-mail  --with-mail=dynamic  --with-mail_ssl_module  --with-stream  --with-stream=dynamic  --with-stream_ssl_module  --with-stream_realip_module  --with-stream_geoip_module  --with-stream_geoip_module=dynamic --with-stream_ssl_preread_module  --with-cpp_test_module  --with-libatomic   


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

```php
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

###   nginx 复习

### 18  安装

```
下载必要组件

nginx下载地址

http://nginx.org/en/download.html
pcre库下载地址，nginx需要

http://sourceforge.net/projects/pcre/files/pcre/
zlib下载地址，nginx需要

http://www.zlib.net/
openssl下载地址，nginx需要

https://github.com/openssl/openssl
在同级目录下,解压安装zlib、openssl、pcre

进入nginx目录，进行配置安装

./configure \
 --prefix=/usr/local/nginx \
 --with-http_ssl_module \
 --with-http_flv_module \
 --with-http_stub_status_module \
 --with-http_gzip_static_module \
 --with-pcre=../pcre-8.39 \
 --with-zlib=../zlib-1.2.8 \ 
 --with-openssl=../openssl-master
下面可直接复制粘贴

./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-http_flv_module --with-http_stub_status_module --with-http_gzip_static_module --with-pcre=../pcre-8.39 --with-zlib=../zlib-1.2.8 --with-openssl=../openssl-master
编译安装

$ make && sudo make install
Nginx会被安装在/usr/local/nginx目录下（也可以使用参数--prefix=指定自己需要的位置）， 安装成功后 /usr/local/nginx 目录下有四个子目录分别是：conf、html、logs、sbin 。 其中 Nginx 的配置文件存放于 conf/nginx.conf， bin文件是位于 sbin 目录下的 nginx 文件。 确保系统的 80 端口没被其他程序占用，运行 sbin/nginx 命令来启动 Nginx，

启动nginx

$sudo /usr/local/nginx/sbin/nginx
    #netstat -ano|grep 80
        tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      关闭 (0.00/0/0)
        unix  17     [ ]         数据报                10801    /dev/log
打开浏览器访问此机器的 IP，如果浏览器出现 Welcome to nginx! 则表示 Nginx 已经安装并运行成功

    # 检查配置文件是否正确
    # /usr/local/sbin/nginx -t 
    # 可以看到编译选项
    # /usr/local/sbin/nginx -V
    #重启Nginx
    #sudo /usr/local/sbin/nginx -s reload
    #关闭Nginx
    #sudo /usr/local/sbin/nginx -s stop
    #优雅停止服务
    #sudo /usr/local/sbin/nginx -s quit
    #kill -s SIGQUIT pid_master
    #kill -s SIGWINCH pid_master
```

### 19. 配置

 ```json
nginx.conf配置文件,基本就分为以下几块：

main
events   {
  ....
}
http        {
  ....
  upstream myproject {
    .....
  }
  server  {
    ....
    location {
        ....
    }
  }
  server  {
    ....
    location {
        ....
    }
  }
  ....
}

nginx配置文件主要分为六个区域：

1. main(全局设置)
2. events(nginx工作模式)
3. http(http设置)
4. sever(主机设置)
5. location(URL匹配)
6. upstream(负载均衡服务器设置)



1. main模块
下面时一个main区域，它是一个全局的设置：

user nobody nobody;
worker_processes 2;
error_log  /usr/local/var/log/nginx/error.log  notice;
pid        /usr/local/var/run/nginx/nginx.pid;
worker_rlimit_nofile 1024;
user 来指定Nginx Worker进程运行用户以及用户组，默认由nobody账号运行。

worker_processes来指定了Nginx要开启的子进程数。每个Nginx进程平均耗费10M~12M内存。根据经验，一般指定1个进程就足够了，如果是多核CPU，建议指定和CPU的数量一样的进程数即可。我这里写2，那么就会开启2个子进程，总共3个进程。

error_log用来定义全局错误日志文件。日志输出级别有debug、info、notice、warn、error、crit可供选择，其中，debug输出日志最为最详细，而crit输出日志最少。

pid用来指定进程id的存储文件位置。

worker_rlimit_nofile用于指定一个nginx进程可以打开的最多文件描述符数目，这里是65535，需要使用命令“ulimit -n 65535”来设置。


2.events模块
events模块来用指定nginx的工作模式和工作模式及连接数上限，一般是这样：

events {
    use epoll; #Linux平台
    worker_connections  1024;
}
use用来指定Nginx的工作模式。Nginx支持的工作模式有select、poll、kqueue、epoll、rtsig和/dev/poll。其中select和poll都是标准的工作模式，kqueue和epoll是高效的工作模式，不同的是epoll用在Linux平台上，而kqueue用在BSD系统中,对于Linux系统，epoll工作模式是首选。

worker_connections用于定义Nginx每个进程的最大连接数，即接收前端的最大请求数，默认是1024。最大客户端连接数由worker_processes和worker_connections决定，即Max_clients=worker_processes*worker_connections，在作为反向代理时，Max_clients变为：Max_clients = worker_processes * worker_connections/4。 进程的最大连接数受Linux系统进程的最大打开文件数限制，在执行操作系统命令“ulimit -n 65536”后worker_connections的设置才能生效。


3. http模块
http模块可以说是最核心的模块了，它负责HTTP服务器相关属性的配置，它里面的server和upstream子模块，至关重要，等到反向代理和负载均衡以及虚拟目录等会仔细说。

http{
    include       mime.types;
    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /usr/local/var/log/nginx/access.log  main;
    sendfile        on;
    tcp_nopush      on;
    tcp_nodelay     on;
    keepalive_timeout  10;
    #gzip  on;
    upstream myproject {
        .....
    }
    server {
        ....
    }
}
下面详细介绍下这段代码中每个配置选项的含义。

include 来用设定文件的mime类型,类型在配置文件目录下的mime.type文件定义，来告诉nginx来识别文件类型。

default_type设定了默认的类型为二进制流，也就是当文件类型未定义时使用这种方式，例如在没有配置asp 的locate 环境时，Nginx是不予解析的，此时，用浏览器访问asp文件就会出现下载了。

log_format用于设置日志的格式，和记录哪些参数，这里设置为main，刚好用于access_log来纪录这种类型。

main的类型日志如下：也可以增删部分参数。

127.0.0.1 - - [21/Apr/2015:18:09:54 +0800] "GET /index.php HTTP/1.1" 200 87151 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2272.76 Safari/537.36"
access_log

用来纪录每次的访问日志的文件地址，后面的main是日志的格式样式，对应于log_format的main。

sendfile参数用于开启高效文件传输模式。将tcp_nopush和tcp_nodelay两个指令设置为on用于防止网络阻塞。

keepalive_timeout设置客户端连接保持活动的超时时间。在超过这个时间之后，服务器会关闭该连接。

4. server模块
sever 模块是http的子模块，它用来定一个虚拟主机，我们先讲最基本的配置，这些在后面再讲。

我们看一下一个简单的server 是如何做的？

server {
        listen       8080;
        server_name  localhost 192.168.12.10 www.yangyi.com;
        # 全局定义，如果都是这一个目录，这样定义最简单。
        root   /Users/yangyi/www;
        index  index.php index.html index.htm; 
        charset utf-8;
        access_log  usr/local/var/log/host.access.log  main;
        aerror_log  usr/local/var/log/host.error.log  error;
        ....
}
server标志定义虚拟主机开始。

listen用于指定虚拟主机的服务端口。

server_name用来指定IP地址或者域名，多个域名之间用空格分开。

root 表示在这整个server虚拟主机内，全部的root web根目录。注意要和locate {}下面定义的区分开来。

index 全局定义访问的默认首页地址。注意要和locate {}下面定义的区分开来。

charset用于设置网页的默认编码格式。

access_log用来指定此虚拟主机的访问日志存放路径，最后的main用于指定访问日志的输出格式。

5. location模块
location模块是nginx中用的最多的，也是最重要的模块了，什么负载均衡啊、反向代理啊、虚拟域名啊都与它相关。慢慢来讲：

location 根据它字面意思就知道是来定位的，定位URL，解析URL，所以，它也提供了强大的正则匹配功能，也支持条件判断匹配，用户可以通过location指令实现Nginx对动、静态网页进行过滤处理。像我们的php环境搭建就是用到了它。

我们先来看这个，设定默认首页和虚拟机目录。

location / {
            root   /Users/yangyi/www;
            index  index.php index.html index.htm;
        }
location /表示匹配访问根目录。

root指令用于指定访问根目录时，虚拟主机的web目录，这个目录可以是相对路径（相对路径是相对于nginx的安装目录）。也可以是绝对路径。

  #反向代理配置
  location /itcast/ {
             proxy_pass http://127.0.0.1:12345;
             proxy_set_header X-real-ip $remote_addr;
             proxy_set_header Host $http_host;
         }


  #采用uwsgi方式
  location /python/ {
             include uwsgi_params;
             uwsgi_pass 127.0.0.1:33333;
         }



    #访问nginx本机目录的文件
    location / {
            root   /home/itcast/xwp/itcast/;
            index  index.html index.htm;
        }

    location  /static/ {
             alias /var/static/;
        }

6. upstream模块
upstream 模块负债负载均衡模块，通过一个简单的调度算法来实现客户端IP到后端服务器的负载均衡。我先学习怎么用，具体的使用实例以后再说。

upstream test.com{
    ip_hash;
    server 192.168.123.1:80;
    server 192.168.123.2:80 down;
    server 192.168.123.3:8080  max_fails=3  fail_timeout=20s;
    server 192.168.123.4:8080;
}
在上面的例子中，通过upstream指令指定了一个负载均衡器的名称test.com。这个名称可以任意指定，在后面需要的地方直接调用即可。

里面是ip_hash这是其中的一种负载均衡调度算法。

Nginx的负载均衡模块目前支持4种调度算法:

① weight 轮询（默认）。每个请求按时间顺序逐一分配到不同的后端服务器，如果后端某台服务器宕机，故障系统被自动剔除，使用户访问不受影响。weight。指定轮询权值，weight值越大，分配到的访问机率越高，主要用于后端每个服务器性能不均的情况下。
② ip_hash。每个请求按访问IP的hash结果分配，这样来自同一个IP的访客固定访问一个后端服务器，有效解决了动态网页存在的session共享问题。
③ fair。比上面两个更加智能的负载均衡算法。此种算法可以依据页面大小和加载时间长短智能地进行负载均衡，也就是根据后端服务器的响应时间来分配请求，响应时间短的优先分配。Nginx本身是不支持fair的，如果需要使用这种调度算法，必须下载Nginx的upstream_fair模块。
④ url_hash。按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，可以进一步提高后端缓存服务器的效率。Nginx本身是不支持url_hash的，如果需要使用这种调度算法，必须安装Nginx 的hash软件包。
在HTTP Upstream模块中，可以通过server指令指定后端服务器的IP地址和端口，同时还可以设定每个后端服务器在负载均衡调度中的状态。常用的状态有：

down，表示当前的server暂时不参与负载均衡。

backup，预留的备份机器。当其他所有的非backup机器出现故障或者忙的时候，才会请求backup机器，因此这台机器的压力最轻。

max_fails，允许请求失败的次数，默认为1。当超过最大次数时，返回proxy_next_upstream 模块定义的错误。

fail_timeout，在经历了max_fails次失败后，暂停服务的时间。max_fails可以和fail_timeout一起使用。

注意 当负载调度算法为ip_hash时，后端服务器在负载均衡调度中的状态不能是weight和backup。

备注： nginx的worker_rlimit_nofile达到上限时，再有客户端链接报502错误. 用了log_format指令设置了日志格式之后，需要用access_log指令指定日志文件的存放路径.
 ```

### 20. 反向代理

```json
正向代理，也就是传说中的代理,他的工作原理就像一个跳板，简单的说，我是一个用户，我访问不了某网站，但是我能访问一个代理服务器，这个代理服务器呢，他能访问那个我不能访问的网站，于是我先连上代理服务器，告诉他我需要那个无法访问网站的内容，代理服务器去取回来，然后返回给我。 从网站的角度，只在代理服务器来取内容的时候有一次记录，有时候并不知道是用户的请求，也隐藏了用户的资料，这取决于代理告不告诉网站。结论就是，正向代理 是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。客户端必须要进行一些特别的设置才能使用正向代理。

反向代理（Reverse Proxy）方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

从用途上来讲：

   正向代理的典型用途是为在防火墙内的局域网客户端提供访问Internet的途径。
   正向代理还可以使用缓冲特性减少网络使用率。反向代理的典型用途是将防火墙后面的服务器提供给Internet用户访问。
   反向代理还可以为后端的多台服务器提供负载平衡，或为后端较慢的服务器提供缓冲服务。
   另外，反向代理还可以启用高级URL策略和管理技术，从而使处于不同web服务器系统的web页面同时存在于同一个URL空间下。

反向代理服务器的基本配置:

1. proxy_pass
	proxy_pass URL;
    配置块 location if
    此配置将当前请求代理到URL参数指定的服务器上,URL可以是主机名或者IP地址加PORT的形式
    proxy_pass http://localhost:8000;
    也可以结合负载均衡实用<负载均衡会说明这种情况>
    也可以吧HTTP转换成HTTPS
    proxy_pass http://192.168.0.1;
    默认情况反向代理不转发请求中的Host头部,如果需呀设置抓发头部
        则 proxy_set_header Host $host;

2. proxy_method
	proxy_method method_name;
    配置块 http server location
    此配置项表示转发时的协议方法名:
        proxy_method POST;
    那么客户端发来的GET请求在转发时方法改为POST;

3. proxy_hide_header
	proxy_hide_header header1;
    配置块 http server location;
    Nginx会将上游服务器的响应转发给客户端,但默认不转发HTTP头部字段(Date Server X-Pad X-Accel-* )
    使用proxy_hide_header可以指定任意头部不能被转发
    proxy_hide_header Cache-Control;
    proxy_hide_header MicrosoftOfficeWebServer;

4. proxy_pass_header
	proxy_pass_header header1;
    配置块 http server location
    功能与 proxy_hide_header相反,是设置哪些头部允许转发.
    proxy_pass_header X-Accel-Redirect;

5. proxy_pass_request_body
	proxy_pass_request_body off|on;
    默认 on
    配置块 http server location;
    确定上游服务器是否向上游服务器转发HTTP包体

6. proxy_pass_request_header
	proxy_pass_request_header on | off;
    默认on
    配置块 http server location
    确定是否转发HTTP头部

7. proxy_redirect
	proxy_redirect [default | off |redirect |replacement]
    默认default
    配置块 http server location
    当上游服务响应时重定向或刷新(HTTP 301 302),proxy_redirect可以重设HTTP头部的location或refresh字段

    proxy_redirect http://locahost:8000/two/ http://frontend/one/;
    上游响应302,location是URI是http://locahost:8000/two/some/uri/
    那是实际转发给客户端的是 http://frontend/one/some/uri/;
    可以使用前面提到的ngx_http_core_module模块提供的变量 
    proxy_redirect http://locahost:8000/two/ http://$host:server_port/;
    可以省略replacement参数的主机名部分,这时候用虚拟主机名填充
    proxy_redirect http://locahost:8000/two/ /one/;

    使用off参数的时候,将使location和refresh的字段维持不变
    proxy_redirect off;

    如果使用的 proxy_redirect default;
    下面两种配置是等效的
        location /{
            proxy_pass http://upstream:port/two/;
            proxy_redirect default;
        }
        location /{
            proxy_pass http://upstream:port/two/;
            proxy_redirect http://upstream:port/two/ /one/;
        }

8. proxy_next_upstream
	proxy_next_upstream [error |timeout |invalid_header |http_500 |http_502~504 |http_404 | off]
    默认 proxy_next_upstream error timeout;
    配置块 http server location

    此配置表示上游一台服务器转发请求出现错误时,继续换一套服务器处理这个请求
    其参数用来说明在那些情况下继续选择下一台上游服务器转发请求.
    error 向上游发起连接 发送请求 读取响应时出错
    timeout 发送请求或读取响应时出错
    invalid_header 上游服务器发送的响应时不合法
    http_500 上游响应500
    http_502 上游响应502
    http_503 上游响应503
    http_504 上游响应504
    http_404 上游响应404
    off      关闭proxy_next_upstream功能 只要一出错就选择另外一台上游再次出发
Nginx反向代理模块中还提供很多配置,如设置连接的超时时间,临时文件如何存储,如何缓存上游服务器响应等功能.


可以通过阅读 ngx_http_proxy_module了解更多详细情况
		#sudo vim /usr/local/nginx/conf/nginx.conf 

            server {
                listen       80;
                server_name  localhost;
                location / {
                #保证代理机器能访问到 下面的机器并装有nginx  在主机号为100的机器上有响应网页
                proxy_pass http://192.168.1.100;
                root   html;
                index  index.html index.htm;
                }
            }
            sudo /usr/local/nginx/sbin/nginx -s reload
加一些判断条件 获取到 对方请求的主机 防止别人代理到自己的主机上
```

### 21. 负载均衡

```json
负载均衡是由多台服务器以对称的方式组成一个服务器集合，每台服务器都具有等价的地位，都可以单独对外提供服务而无须其他服务器的辅助。通过某种负载分担技术，将外部发送来的请求按照事先设定分配算法分配到对称结构中的某一台服务器上，而接收到请求的服务器独立地回应客户的请求。

均衡负载能够平均分配客户请求到服务器列阵，籍此提供快速获取重要数据，解决大量并发访问服务问题。

1. upstream块
upstream name {...}
配置块 http
upstream块定义一个上游服务器的集群,便于反向代理中的proxy_pass使用

upstream mynet{
    server www.wopai1.com;
    server www.wopai2.com;
    server www.wopai3.com;
}
server {
    location /{
        proxy_pass http://mynet;
    }
}

2. server 
server name [paramenters]
配置块upstream
server配置项指定了一台上游服务器的名字,可以是域名 IP地址端口 UNIX句柄
weight= number;设置向这台服务器转发的权重,默认为1
max_fails=number;该选项域fail_timeout配合使用
        指在fail_timeout时间段内如果转发上游失败超过number次就认为当前的fail_timeout时间内
        这台服务器不可用,max_fails默认为1 如果设置为0 表示不检查失败次数
fail_timeout=time; fail_timeout表示该时间内转发多少次失败后就认为上游不可用.默认10s
down    表示上游服务器永久下线,只能在ip_hash配置时才有效
backup  在ip_hash配置时无效.只有所有非备份机都失败,才向上游备份服务器转发请求.
upstream mynet{
    server www.wopai1.com weight=5;
    server www.wopai2.com:8081 max_fails=3 fail_timeout=300s;
    server www.wopai2.com down;
}

3. ip_hash
配置块 upstream
希望来自某一个用户的请求始终落在固定的一台服务器上进行处理.
根据客户端的IP散列计算出一个key,将key按照upstream集群中的上游服务器进行取模,求得的值对应的主机接收转发请求.
ip_hash不可以与weight同时使用
如果upstream配置中有一台服务器暂时不可用,不能直接删除该配置,而应该使用down标识.
upstream mynet{
    ip_hash;
    server www.wowpai1.top;
    server www.wowpai2.top;  
    server www.wowpai3.top down;
}

例子,服务器负载均衡基本配置,nginx中可以进行负载均衡的相关设置:
upstream my.net{    #my.net是自定义的命名 在server结构中引用即可

        #代理服务器为 两台机器192.168.22.136 192.168.22.147做负载均衡操作 
        #两台机器上 可以跑apache负载功能更为强大的网页相关任务

        #max_fails 表示尝试出错最大次数 即可认为该服务器 在fail_timeout时间内不可用
        # server servername:port   servername可以写主机名 或者点分式IP
        server 192.168.22.136:80 max_fails=1 fail_timeout=300s;
        server 192.168.22.147:80 max_fails=1 fail_timeout=300s;  
        }


        server {
        listen       80;
        server_name  localhost; 
        location / {
            #upstream 块名
            proxy_pass http://my.net;
            root   html;
            index  index.html index.htm;
        }

高质量讲nginx的电子书: http://tengine.taobao.org/book/
```



