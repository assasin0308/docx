# centos7 安装nginx

`注:` Nginx是C语言开发, 建议在Linux上运行

### 一. 源码编译安装nginx

1. 安装Nignx所需环境

   ```scss
   yum install gcc-c++
   yum install -y pcre pcre-devel
   yum install -y zlib zlib-devel
   yum install -y openssl openssl-devel
   ```

2. 官网下载.tar.gz安装包, 地址:<https://nginx.org/en/download.html>

   ```scss
   wget -c https://nginx.org/download/nginx-1.10.1.tar.gz
   ```

3. 解压

   ```scss
   tar -zxvf nginx-1.10.1.tar.gz
   ```

4. 配置 — 使用的默认配置

   ```scss
   cd nginx-1.10.1
   ./configure
   make
   make install
   ```

5. 启动, 停止nginx

   查找安装路径

   ```scss
   [root/]# whereis nginx
   nginx: /usr/local/nginx
   ```

   启动, 停止Nginx

   ```scss
   cd /usr/local/nginx/sbin/
   ./nginx 
   ./nginx -s stop
   ./nginx -s quit
   ./nginx -s reload

   //注释
   ./nginx -s quit:此方式停止步骤是待nginx进程处理任务完毕进行停止。
   ./nginx -s stop:此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程。
       
   //重新加载配置文件
   ./nginx -s reload
   ```

6. 开机自动启动

   在rc.local增加启动代码

   ```scss
   vim /etc/rc.local

   //增加一行 /usr/local/nginx/sbin/nginx
   //设置执行权限
   chmod 755 rc.local
   ```

   ​

