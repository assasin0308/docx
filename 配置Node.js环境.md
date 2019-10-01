# 配置Node.js环境

[TOC]

新项目需要使用node.js。 记录一下node.js和npm安装过程.



### 1. Mac本地安装Node.js

    下载之前的node.js项目, 用phpstorm打开后报语法错误. 检查phpstorm的nodejs插件已启用. 判断是node.js环境未配置.

    Mac安装配置node.js和npm.

1. 访问node.js官网下载列表(https://nodejs.org/en/download/)

   下载推荐的node-v8.11.3.pkg包, 点击并根据提示进行安装.  安装完成后会显示软件的安装版本和安装位置, 如下所示.

   ```scss
   	•	Node.js v8.11.3 to /usr/local/bin/node
   	•	npm v5.6.0 to /usr/local/bin/npm
   Make sure that /usr/local/bin is in your $PATH.

   ```

   可在控制台输入以下命令查看安装版本.

   ```scss
   $ node -v
   v8.11.3
   $ npm -v
   5.6.0
   ```

2. 更换为淘宝的NPM镜像(http://npm.taobao.org)

   ```scss
   //查看当前镜像
   $ npm get registry
   https://registry.npmjs.org/
   //更换为淘宝的镜像 -- 此方案安装cnpm, 不影响之前的npm
   sudo npm install -g cnpm --registry=https://registry.npm.taobao.org

   //添加成功后查看cnpm版本信息
   $ cnpm -v
   cnpm@6.0.0 (/usr/local/lib/node_modules/cnpm/lib/parse_argv.js)
   npm@6.2.0 (/usr/local/lib/node_modules/cnpm/node_modules/npm/lib/npm.js)
   node@8.11.3 (/usr/local/bin/node)
   npminstall@3.10.0 (/usr/local/lib/node_modules/cnpm/node_modules/npminstall/lib/index.js)
   prefix=/usr/local 
   darwin x64 17.4.0 
   registry=https://registry.npm.taobao.org

   ```

3. 导入express框架及其相关模块

   ```scss
   //安装express并将其保存到依赖列表中
   cnpm install express --save
   ```

4. 查看已安装过的包

   ```
   //全局查看
   npm list -g --depth 0
   //查看本地
   npm list --depth 0
   ```

5. 卸载本地package

   ```
   npm uninstall <package-name>
   ```

6. 创建package.json

   ```
   使用 npm init 即可在当前目录创建一个 package.json 文件：
   ```

   输入 `npm init` 后会弹出一堆问题，我们可以输入对应内容，也可以使用默认值。在回答一堆问题后输入 `yes` 就会生成图中所示内容的  `package.json` 文件。

   如果嫌回答这一大堆问题麻烦，可以直接输入 `npm init --yes` 跳过回答问题步骤，直接生成默认值的 `package.json` 文件：



### 2. centos6.5 nvm 安装升级nodejs

1. 查看服务器nodejs版本

   ```
   # node -v
   v6.10.3
   # npm -v
   3.10.10
   ```

2. 服务器以前版本是通过nvm安装管理的

   通过nvm安装升级指定nodejs

   ```scss
   //查看可用node版本
   # nvm ls-remote
     	 v8.11.1
        v8.11.2
   ->   v8.11.3
        v9.0.0
        
   //安装v8.11.3版本
   nvm install v8.11.3
   //安装完成后使用新版
   nvm use v8.11.3
   //卸载旧版本(只有在该版本未使用状态可卸载)
   nvm uninstall v6.10.3
   //将此版本设为默认
   nvm alias default v6.10.2
   ```


### 3. centos7 使用nvm方式安装nodejs

1. 安装nvm 最新版本是0.33.11 [git地址](https://github.com/creationix/nvm)

2. ```
   # curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
   or Wget:
   # wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
   ```

3. 使配置生效

   ```
   source ~/.bash_profile  
   ```

4. 查看nvm版本

   ```
   nvm --version  
   ```

5. 查看可安装node版本

   查看可以安装的node版本，注意`(Latest LTS: Carbon)`标识, 此标识是长期支持的最新版本

   ```
   nvm list-remote 
   ```

6. 安装指定版本的node

   ```
   nvm install v8.12.0
   ```

7. 确认安装结果

   ```
   node -v  
   npm -v 
   ```

8. 切换版本

   ```
   nvm use v10.10.0
   ```

9. 设置默认版本

   ```
   nvm alias default v10.10.0
   ```

10. 查看所有已安装的版本

   ```
   nvm list
   ```

### 4. pm2使用(进程管理器)

pm2 是一个带有负载均衡功能的Node应用的进程管理器.

[pm2官网](https://pm2.io/doc/en/runtime/quick-start/)

以npm方式安装**pm2** (全局安装)

```
npm install pm2 -g
```

以下是pm2常用的命令行

```
$ pm2 start app.js              # 启动app.js应用程序

$ pm2 start app.js -i 4         # cluster mode 模式启动4个app.js的应用实例     # 4个应用程序会自动进行负载均衡

$ pm2 start app.js --name="api" # 启动应用程序并命名为 "api"

$ pm2 start app.js --watch      # 当文件变化时自动重启应用

$ pm2 start script.sh           # 启动 bash 脚本


$ pm2 list                      # 列表 PM2 启动的所有的应用程序

$ pm2 monit                     # 显示每个应用程序的CPU和内存占用情况

$ pm2 show [app-name]           # 显示应用程序的所有信息


$ pm2 logs                      # 显示所有应用程序的日志

$ pm2 logs [app-name]           # 显示指定应用程序的日志

$ pm2 flush


$ pm2 stop all                  # 停止所有的应用程序

$ pm2 stop 0                    # 停止 id为 0的指定应用程序

$ pm2 restart all               # 重启所有应用

$ pm2 reload all                # 重启 cluster mode下的所有应用

$ pm2 gracefulReload all        # Graceful reload all apps in cluster mode

$ pm2 delete all                # 关闭并删除所有应用

$ pm2 delete 0                  # 删除指定应用 id 0

$ pm2 scale api 10              # 把名字叫api的应用扩展到10个实例

$ pm2 reset [app-name]          # 重置重启数量


$ pm2 startup                   # 创建开机自启动命令

$ pm2 save                      # 保存当前应用列表

$ pm2 resurrect                 # 重新加载保存的应用列表

$ pm2 update                    # Save processes, kill PM2 and restore processes

$ pm2 generate                  # Generate a sample json configuration file


$ pm2 deploy app.json prod setup    # Setup "prod" remote server

$ pm2 deploy app.json prod          # Update "prod" remote server

$ pm2 deploy app.json prod revert 2 # Revert "prod" remote server by 2

```



### 5. pm2开多进程时与log4j冲突, 导致log4不输出日志. (log4js pm2 cluster配置)

修复方案:

安装pm2-intercom

```
pm2 install pm2-intercom
```

配置log4js.json的配置文件, 增加: `pm2: true`

```
 "categories":{
    "default":{"appenders":["console","log_info"],"level":"info"},
    "logInfo":{"appenders":["console","log_info"],"level":"info"},
    "logErr":{"appenders":["console","log_error"],"level":"error"}
  },
  "pm2": true
```



参考链接:

https://github.com/xiaozhongliu/node-api-seed/blob/master/util/logger.js

[pm2 logging problem with multiple applications](https://github.com/log4js-node/log4js-node/issues/547)

[集群模式PM2+Log4js log写入失败问题](https://juejin.im/entry/5a0cf3276fb9a0450167814f)

