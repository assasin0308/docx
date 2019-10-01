# centos 激活操作系统级别监控指标

[TOC]

1. 在云服务监控->云服务器ECS中, 点击安装插件

2. 安装完成后在监控详情中的操作系统级别监控指标中, 根据安装指南安装

   安装云监控组件，需要使用`administrator 或者 root权限执行安装操作：`

   ```scss
   1. Linux类系统：
       1) 确认操作系统中已有unzip、iostat命令。如果没有，请通过yum install unzip sysstat或apt-get install unzip sysstat进行安装。
       2) wget http://update.aegis.aliyun.com/download/cms_install.sh 
       3) chmod +x cms_install.sh 
       4) ./cms_install.sh 

   安装完成后，请等待5分钟后检查服务器列表。
   ```

执行./cms_install.sh  出现 aegis version is smaller than 60, pls upgrade it first错误.