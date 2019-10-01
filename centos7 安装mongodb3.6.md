# centos7 安装mongodb3.6

[TOC]

问题起源, centos7自带的mongodb2.6, php中需要安装mongodb3.6支持新特性.

# 1. 卸载已安装的mongodb

安装mongodb前在服务器上看到已安装了mongodb, 但是调用php的php_info(),未看到扩展. 而且mongodb指定的数据文件路径也不对, 决定卸载后重新安装

删除步骤

```
1. 停止服务：
sudo service mongod stop
2. 删除软件包：
sudo yum erase $(rpm -qa | grep mongodb-org) 
或 (此方法会删除php的mongodb扩展)
sudo yum erase $(rpm -qa | grep mongodb) 
3. 删除数据和日志目录：
sudo rm -rf /var/log/mongodb（日志）
sudo rm -rf /var/lib/mongo（数据）
```

### 2. 安装mongodb3.6 

1. 配置mongodb的yum源

   ```
   vim /etc/yum.repos.d/mongodb-org-3.6.repo
   ```

   添加以下内容

   ```
   [mongodb-org-3.6]
   name=MongoDB Repository
   baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
   gpgcheck=1
   enabled=1
   gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
   ```

2. 安装mongodb

   ```
   yum install -y mongodb-org
   ```

3. 启动mongodb

   ```
   systemctl start mongod
   ```

4. yum下载安装好mongodb3.6之后，切记把mongodb-org-3.6.repo文件删掉或重命名，否则yum之后下载其他东西都会失败，

   ```
   rm -f /etc/yum.repos.d/mongodb-org-3.6.repo
   ```


