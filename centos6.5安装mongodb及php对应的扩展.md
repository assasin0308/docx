#centos6.5安装mongodb及php对应的扩展

[TOC]

###一. mongodb的卸载

安装mongodb前在服务器上看到已安装了mongodb, 但是调用php的php_info(),未看到扩展. 而且mongodb指定的数据文件路径也不对, 决定卸载后重新安装

* 删除步骤

  ```
  1. 停止服务：
  sudo service mongod stop
  2. 删除软件包：
  sudo yum erase $(rpm -qa | grep mongodb-org)
  3. 删除数据和日志目录：
  sudo rm -rf /var/log/mongodb（日志）
  sudo rm -rf /var/lib/mongo（数据）
  ```



###二. yum安装mongodb

1. 安装mongodb, php扩展

   ```php
   yum install php-pecl-mongo mongodb mongodb-devel mongodb-server
   ```

2. 配置etc/mongodb.conf

   ```php
   # 执行
   cat /etc/mongodb.conf |awk '{if($0 !~ /^$/ && $0 !~ /^#/) {print $0}}'
   #展示如下
   bind_ip = 127.0.0.1							//
   port = 27017								//端口
   fork = true									//后台运行
   pidfilepath = /var/run/mongodb/mongodb.pid  //pid路径
   logpath = /var/log/mongodb/mongodb.log		//日志路径
   dbpath =/var/lib/mongodb					//数据库路径
   journal = true
   nohttpinterface = true
   ```

   根据需求配置对应的, 

3. 启动mongodb

   ```php
   # 配置开机启动
   sudo chkconfig mongod on
   # 启动mongodb
   sudo service mongod start
   # 停止
   sudo service mongod stop
   # 重启
   sudo service mongod restart
   ```

4. 重启Apache/Nginx后php扩展生效

5. 备注

   对应路径的文件夹不存在则创建并赋权限

   ```php
   mkdir -p /var/lib/mongodb 
   chown mongodb:mongodb -R /var/lib/mongodb/ 
   ```

   ​

### 三 mongodb设置用户名密码 (mongodb3.0+无效)

1. 设置super admin

   ```Php
   # mongodb在没有假--auth的情况下
   > use admin
   switched to db admin
   > db.addUser('root','root')
   {
   	"user" : "root",
   	"readOnly" : false,
   	"pwd" : "2a8025f0885adad5a8ce0044070032b3",
   	"_id" : ObjectId("5ad04fa7706ec252bc7b1e14")
   }
   
   #设置成功后show collecitons 会显示已有的表
   > show collections
   system.indexes
   system.users
   > 
   ```

2. 设置mongodb的auth为true

   ```Scss
   #编辑mongodb的配置文件
   vim /etc/mongodb.conf
   #找到auth = true, 若没有则手动添加
   auth = true
   #设置完成后重启mongo服务
   service mongod restart
   ```

3. 单独访问数据库的表

   ```php
   //每次登录都要在admin否则无权限查看编辑
   > use admin
   switched to db admin
   > db.auth('root','root')
   ```




### 新版mongodb(当前版本3.6)创建用户 代码如下

```
db.createUser({user:"user123","pwd":"root",roles:[{role:"readWrite",db:"qddx"}]})
```





参考链接**

[MongoDB 设置用户名密码登录](https://blog.csdn.net/qq_32502511/article/details/80619277)



