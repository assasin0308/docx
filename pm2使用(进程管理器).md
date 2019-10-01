### pm2使用(进程管理器)

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



### pm2开多进程时与log4j冲突, 导致log4不输出日志. (log4js pm2 cluster配置)

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

