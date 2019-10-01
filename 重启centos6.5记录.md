# 重启centos6.5记录



`需求说明`: 服务器升级配置后, cpu和内存等配置需要重启服务器才能生效.

`注意事项`: 默认没有开机自动挂载, 没有自启http, mysql, mongdb, redis等服务.

1. 控制台重启服务器

   shutdown命令

   shutdown [选项] 时间

   -c：取消前一个关机命令

   -h：关机

   -r：重启

   ```scss
   shutdown -r now 是立即重启
   //注 没有阿里云账户时, 服务器不推荐使用关机shutdown -h, 关机后, 无法连接ssh等
   ```

2. 查看服务器磁盘情况

   ```scss
   df -h
   fdisk -l
   ```

   ​

3. 重启后重新挂载磁盘到指定目录

   ```scss
   mount /dev/xvdb1 /big/
   ```

4. 检查挂载磁盘中的软连接是否生效

5. 启动使用需要的服务

   ```scss
   service httpd start
   service mysqld start
   service mongod status //查看mongodb是否启动
   service redis start
   ```

6. 正式服务器注意事项

   - 查看mongodb是否正常启动

   - 查看指定软连接是否正常连接

     ```scss
     /var  //此目录下的log日志
     /var/lib/mysql/ //此目录下ibdata1
     /var/www/html_000826/ //目录下media目录
     /var/www/databack // 此目录下big目录
     ```

     ​

