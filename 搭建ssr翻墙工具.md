# 搭建ssr翻墙工具



## 1. 创建服务器

一、 Vultr官方地址

Vultr官方网站：[点击这里](https://www.vultr.com/?ref=7225801)

Vultr中文网地址：[点击这里](https://www.vultrblog.com/)

二、 注册地址： 

最新优惠注册地址，冲10美金送25美金唯一活动地址：[**点击进入**](https://www.vultr.com/promo25b?ref=7225801)

Vultr更多优惠活动请关注[**Vultr中文网**](https://www.vultrblog.com/)。

进入后按图输入邮箱和要设置的密码（最少10位，要同时有数字和大小写字母），最后点击create account创建

充值 信用卡或者paypal. 支付宝不支持优惠

三. 创建服务器

位置: 选择日本

操作系统: centos7

服务器规格:$3.5/月 具有ipv4地址

等待服务器创建

服务器启动好后, 本地通过终端, ping测试, ssh连接. 如果ssh超时, 则可能端口被墙. 检测方案为 [点击进入](https://blog.csdn.net/qq_38401919/article/details/80824432)

如果22端口被封, 则在vultr后台删除服务器后重新添加.

四. 部署ssr代码

参考链接 

[vultr/vps搭建SS/SSR教程最新完整版(2018.4.13更新)](https://leziqu.com/3001.html)

**五. centos7开启对应端口**  设置后重启

centos7中的防火墙改成了firewall，使用iptables无作用，开放端口的方法如下：

firewall-cmd --zone=public --add-port=80/tcp --permanent

返回success为成功

命令含义： 

--zone #作用域 

--add-port=80/tcp #添加端口，格式为：端口/通讯协议 

--permanent #永久生效

重启防火墙：

systemctl restart firewalld.service 

关闭防火墙：

systemctl stop firewalld.service 

查看监听(Listen)的端口

netstat -lntp

检查端口被哪个进程占用

netstat -lnp|grep 8080

