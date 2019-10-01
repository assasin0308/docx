## Node.js学习

```
官网:nodejs.org
中文网: nodejs.cn
```

### 1. REPL执行js代码

```javascript
1. 打开cmd
2. 运行node
3. .exit退出或ctl+c
```

### 2. 写文件操作

```javascript
var fs = require('fs'); //引入文件系统
fs.writeFile(__dirname +'/shi.txt',"床前明月光,疑似地上霜",'utf8',function(err){
   if (err){
     console.log(err);
   }else{
     console.log('写入成功');
   }
})
```

### 3.读文件操作

```javascript
var fs = require('fs'); //引入文件系统
fs.readFile(__dirname + '/test.txt','utf8',function(err,data){
  if(err){
    console.log("读取失败",err);
  }else{
    console.log("读取到内容",data);
  }
})
注意:若不传字符编码,则会读取到buffer对象,以16进制显示
```

### 4. 文件目录路径

```javascript
global:
__dirname 

__filename

path.join() 拼接路径
var path = require('path');
path.join(__dirname,'shibin.log')
```

### 5. 异常处理

```js
try{
  console.log(a);
}catch(e){
  cosole.log('代码出错了',e);
}
注意:文件操作不能使用try捕获.
```

### 6. http_server

```js
var http = require('http'); //引入http模块
var server = http.createServer(); //创建http服务

//监听指定端口号
server.listen(8888,function(){
  console.log('监听端口成功,通过访问:http://127.0.0.1:8888');
});
server.on('request',function(request,response){
  //request事件的处理函数
//  response.write('hello-world');
  response.setHeader("content-type","text/html;charset=utf-8");//设置响应头信息
  response.setHeader("name","assasin");//设置响应头信息
  response.setHeader("age","16");//设置响应头信息
  // response.writeHead(),也可设置响应头,可一次设置多条信息,包括状态码
  response.writeHead(200,"ok",{
    "content-type":"text/html;charset=utf-8",
    "name":"assasin",
    "age":18
  })
  //返回数据给浏览器,传字符串或buffer对象
  response.write("hello-world");
  response.end('响应结束');  //结束响应
})

注意:设置响应头应在write()之前设置.
```

### 7. response APIs

```javascript
var http = require('http'); //引入http模块
var server = http.createServer(); //创建http服务
//监听指定端口号
server.listen(8888,function(){
  console.log('监听端口成功,通过访问:http://localhost:8888 进行测试');
});
server.on('request',function(request,response){
  //response 函数
  //response.statusCode() 状态码
  //response.statusMessage() 状态信息
  response.statusCode = '502';
  response.statusMessage = "Bad gateWay";

  response.end('响应结束');  //结束响应
})
```

### 8. content-type

```js
浏览器在接收到服务器响应的数据后,会根据响应头中的content-type来进行处理.
Content_Type:text/html
Content_Type:text/css
Content_Type:application/josn
```

### 9. 模拟服务器

```javascript
var http = require('http');
var server = http.createServer();
var path = require('path');
var fs = require('fs');
var mime = require('mime');

server.on('request',function(req,res){
  if(res.url == '/'){
    res.url = '/index.html';
  }
  res.setHeader("content-type",mime.getType(req.url));
  fs.readFile(path.join(__dirname,'view',req.url),function(err,data){
    if(err){
     res.statusCode = 404;
     res.statusMessage = "Not Found";
     res.end();
    }else{
      res.end(data);
    }
  })

})
server.listen(8888,function(){
  console.log("http://localhost:8888")
})

```

### 10. request常用属性

```javascript
   request.url:请求地址中的路径与参数
   request.headers: 请求头信息(对象)
   request.rawHeader:请求头信息(数组)
   request.httpVersion: http协议版本信息
   request.method:http请求方式
```

### 11. Get请求参数的获取

```javascript
http://localhost:8888?name=assasin&age=18
提取参数:{"name":"assasin","age":18}
```

```javascript
var http = require('http');
var server = http.createServer();
var querystring = require('querystring');
var url = require('url');
server.on('request',function(req,res){
  //1.原生操作
  // var params = {};
  // if(req.url.indexOf("?") != -1){
  //   var querystring = req.url.split('?')[1]; //name=assasin&age=18
  //   querystring.split('&').forEach(function(v,i){
  //     var temp = v.split('=');
  //     params[temp[0]] = temp[1];
  //   })
  // }
  // console.log(params);

  //2. 升级 使用querystring.parse()
  // if(req.url.indexOf("?") != -1){
  //   var str = req.url.split('?')[1];
  //   var params = querystring.parse(str);
  //   console.log(params);
  // }

  //3. 使用url.parse模块处理 第二个参数传 TRUE
 var urlObj = url.parse(req.url,true);
  // urlObj.pathname:路径部分内容
  //urlObj.query 参数部分内容 字符串 ---->参数对象
  console.log(urlObj.query);
   res.end('ok');
})
server.listen(8888,function(){
  console.log("http://localhost:8888");
})

```

### 12. Post请求参数的获取

```javascript
var http = require('http');
var server = http.createServer();
var querystring = require('querystring');
server.on('request',function(req,res){
  //获取Post请求参数 请求体中
//  首先,定义一个数组,用于存放每次接收的数据
  var bufferlist = [];
  //1. 给req对象注册一个data事件,当有POST请求的数据发送至服务器时,就触发该事件
  req.on('data',function(chunk){
    //post请求的数据会分多次发送至服务器.每次发送给服务器都会触发data事件,每次发送的数据可以通过chunk接收, chunk是一个buffer对象
  //  console.log(chunk);
    bufferlist.push(chunk);
  })
  // 2. 给req注册end事件,表示数据发送完毕
  req.on('end',function(){
    //end事件会在POST数据发送完毕触发,所以只能在end 事件中获取
    //需要将数组中所有的buffer对象合并为一个buffer对象 buffer.concat()
    var result = Buffer.concat(bufferlist);

    //将result转为字符串即可 buffer-->string
  //  console.log(result.toString());\
    //最后使用querystring将字符型转为对象
    var params = querystring.parse(result.toString());
    console.log(params);
  })
  res.end('ok');
})

server.listen(8888,function(){
  console.log("http://localhost:8888");
})

```

### 13. npm简介

```javascript
npm (node package manager) node的包管理工具.
npm 服务器 
npm 网站 www.npmjs.com
npm 命令行工具
```

```javascript
基本命令:()内参数可选
npm init (-y) :初始化package.json文件
npm install 包名:下载需要的资源包
npm install 包名@版本号:下载指定版本的资源包
npm i 包名:简写

npm uninstall 包名: 卸载资源包

npm install 包名 -g :全局安装
npm install live-server -g 
npm install less -g
```

### 14. package.json文件的作用及属性说明

```javascript
package.json:用来描述一个包的信息.
必须包含两个信息:name,version
name:包名(不能包含中文,空格,大写字母,特殊字符!);
version:包的版本信息;
```

```javascript
{
  "name": "05-19",
  "version": "1.0.0",
  "description": "",
  "main": "exception.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "mime": "^2.4.3"
  }
}
```

### 15. nrm

```javascript
设置npm让其从国内镜像下载.nrm
npm install nrm -g 
查看服务器列表: nrm ls
产看当前正在使用的服务器 nrm current
切换服务器: nrm use 服务器名称
测试服务器速度: nrm test 服务器名称
```

### 16. hacker_news 案例

```javascript
var http = require('http');
var server = http.createServer();
var fs = require('fs');
var path = require('path');
var mime = require('mime');
var url = require('url');
server.on('request',function(req,res){
    var urlObj = url.parse(req.url,true);
  //1. 设置路由规则
 //   /index 首页
 //   /details 详情页
 //   /submit 添加页
    if(req.url == '/' || req.url == "/index"){
       fs.readFile(path.join(__dirname,'views','index.html'),function(err,data){
           res.end(data);
       })

     //  res.end('首页');
    }else if(urlObj.pathname == "/details"){
       fs.readFile(path.join(__dirname,'views','details.html'),function(err,data){
           res.end(data);
       })
      //  res.end('详情页');
    }else if(req.url == '/submit') {
        fs.readFile(path.join(__dirname,'views','submit.html'),function(err,data){
            res.end(data);
        })

      //  res.end('添加页面');
    }else if(req.url.indexOf("/resource") == 0){
        res.setHeader("content-type",mime.getType(req.url));
       fs.readFile(path.join(__dirname,req.url),function(err,data){
           res.end(data);
       })
     //   res.end("静态资源");
    }else{
        res.statusCode = 404;
        res.statusMessage = "Not Found";
        res.end("Not Found");
    }
})

server.listen(8888,function(){
    console.log("http://localhost:8888");
})
```

###  17. 使用文件存储数据

```javascript
var fs = require('fs');
var path = require('path');
fs.readFile(path.join(__dirname,'data.json'),'utf8',function(err,data){
    data = JSON.parse(data || '[]'); //若为空 转为空数组
    console.log(data);
})

//添加新数据到data.json
var news = {
    "title":"123",
    "url":"",
    "text":"睡大觉"

}
fs.readFile(path.join(__dirname,'data.json'),'utf8',function(err,data){
    data = JSON.parse(data || '[]');

    //将新数据添加至原有数据中
    //需要给news添加id属性
    news.id = data.length == 0 ? 1 : data[data.length -1].id +1 ;
    data.push(news);
    fs.writeFile(path.join(__dirname,'data.json'),JSON.stringify(data),function(err){
        console.log("添加成功");

    })
})

//根据id 查找对应数据
//读取所有数据 data.json
var id = 1
fs.readFile(path.join(__dirname,'data.json'),'utf8',function(err,data){
   data = JSON.parse(data || '[]');
   // data.forEach(function(v,i){
   //     if(v.id == id){
   //         console.log(v);
   //     }
   // })
    // 找数组中满足条件的第一个元素
    var result = data.find(function(v,i){
        return v.id == id;
    });
    console.log(result);
})
```

### 18. 数组常用方法

```javascript
var arr = [1,2,3,4,5,6,7,8,9,10];
//遍历数组
arr.forEach(function(v,i){
    console.log(v + "====>" + i);
})
//映射 map
var newArr = arr.map(function(v,i){
    return v * 2;
})
console.log(newArr); // [ 2, 4, 6, 8, 10, 12, 14, 16, 18, 20 ]

// some方法 判断数组中是否有任意元素满足条件
//判断氏族中是否有偶数
var flag = arr.some(function(v,i){
    return v % 2 == 0;
})
console.log(flag); //true

// 判断数组中是否满足所有条件
var flag = arr.every(function(v,i){
    return v % 2 !== 0;
})
console.log(flag);//false

//查找数组中第一个满足条件的元素
var first = arr.find(function(v,i){
    return v % 2 == 0;
})
console.log(first); // 2

//在数组中找所有满足条件的元素
var newdata = arr.filter(function(v,i){
    return v % 2 == 0;
})
console.log(newdata); // [ 2, 4, 6, 8, 10 ]
```

### 19. art-template 使用

```javascript
1. 安装 npm install art-template 
    var template = require('art-template');
    var path = require('path');
    //以文件作为模板渲染
    var obj = {
        msg: "hello-world"
    }

    var result = template(path.join(__dirname,'tpl.html'),obj);
    console.log(result);
    -------------------------------------------------------------
    //以字符串变量作为模板渲染
    var str = "<div>{{msg}}</div>";
    //1.根据模板字符串创建渲染函数
    var render = template.compile(str);
    //2.使用渲染函数 进行渲染
    var result  = render(obj);
    console.log(result);
```

### 20. hacker_new 渲染数据

```javascript
var http = require('http');
var server = http.createServer();
var fs = require('fs');
var path = require('path');
var mime = require('mime');
var url = require('url');
var template = require('art-template');
server.on('request',function(req,res){
    var urlObj = url.parse(req.url,true);
  //1. 设置路由规则
 //   /index 首页
 //   /details 详情页
 //   /submit 添加页

    if(req.url == '/' || req.url == "/index"){
       // fs.readFile(path.join(__dirname,'views','index.html'),function(err,data){
       //     res.end(data);
       // })
        //将数据渲染到页面
        fs.readFile(path.join(__dirname,'views','index.html'),function(err,data){

            fs.readFile(path.join(__dirname,'data.json'),'utf8',function(err,newslist){
                newlist = JSON.parse(newslist || '[]');
                //data 是模板 newslist是数据
                var render = template.compile(data.toString());
                var result = render({list:newslist});
                res.end(result);
            })
        })
     //  res.end('首页');
    }else if(urlObj.pathname == "/details"){
       // fs.readFile(path.join(__dirname,'views','details.html'),function(err,data){
       //     res.end(data);
       // })
        //读取到模板文件内容
        fs.readFile(path.join(__dirname,'views','details.html'),function(err,data){
            //res.end(data);
           //获取id GET请求参数
            fs.readFile(path.join(__dirname,'views','data.json'),function(err,news){
                //res.end(data);
                //获取id GET请求参数
                news = JSON.parse(news || '[]');
                var news = news.find(function(v,i){
                    return v.id = urlObj.query.id;
                })
                var render = template.compile(data.toString());
                var result = render({item:news});
                res.end(result);
            })
        })
      //  res.end('详情页');
    }else if(req.url == '/submit') {
        // fs.readFile(path.join(__dirname,'views','submit.html'),function(err,data){
        //     res.end(data);
        // })
        fs.readFile(path.join(__dirname,'views','submit.html'),function(err,data){
           // res.end(data);

        })
      //  res.end('添加页面');
    }else if(req.url.indexOf("/resource") == 0){
        res.setHeader("content-type",mime.getType(req.url));
       fs.readFile(path.join(__dirname,req.url),function(err,data){
           res.end(data);
       })
     //   res.end("静态资源");
    }else if(urlObj.pathname == '/add'){
        var news = urlObj.query;
       //获取表单数据   urlObj.query
       fs.readFile(path.join(__dirname,'data.json'),'utf8',function(err,newslist){
           newslist = JSON.parse(newslist || '[]');
           //增加id
           news.id = newslist.length == 0 ? 1 : newslist[newslist.length -1 ].id + 1 ;
           newslist.push(news);
           fs.writeFile(path.join(__dirname,'data.json'),Json.stringify(newslist),function(){
             //  res.end('ok');
               //跳转到首页 并展示
               //设置状态码
               res.statusCode = 302;
               //设置状态信息
               res.statusMessage = "Found";
               //设置响应头中的location
               res.setHeader('location','/index');
               //结束响应
               res.end();
           })
       })
        //存储到data.json
    }else{
        res.statusCode = 404;
        res.statusMessage = "Not Found";
        res.end("Not Found");
    }
})

server.listen(8888,function(){
    console.log("http://localhost:8888");
})
```

### 21.封装读取数据的方法

```javascript
var FILEPATH = path.join(__dirname,'data.json');
function readfile(callback){
    fs.readFile(FILEPATH,'utf8',function(err,data){
        data = JSON.parse(data || "[]");
        callback(data);
    })
}
//回调即可
readfile(function(newslist){
    console.log(newslist);
})
```

### 22. 封装写数据的方法

```javascript
function writefile(data,callback){
    //读取原有数据
    //设置新数据的id
    data.id = newslist.length == 0 ? 1 : newslist[newslist.length - 1] + 1 ;
    readfile(function(newslist){
        //将新数据写入原有数据
        newslist.push(data);
        fs.writeFile(FILEPATH,JSON.stringify(newslist),function(err){
             callback();
        })
    })
}
writefile({title:"",},function(){
    console.log("ok");
})
```

### 23.封装render函数进行模板渲染

```javascript
function render_tpl(filename,data,res){
    fs.readFile(path.join(__dirname,'views',filename + '.html'),function(err,tpl){
        var render = template.compile(tpl.toString());
        var result = render(data);
        res.end(result);
    })
}
```

### 24. 封装读取id对应的数据 

```javascript
function getdatabyid(id,callback){
    readfile(function(newslist){
        var news = newslist.find(function(v,i){
            return v.id = id;
        })
        callback(news);
    })
}
```

### 25. 模块化

```javascript
1.定义模块
一个js文件就是一个模块,每个模考有自己单独的作用域,防止相互影响.
2.引用模块
require函数引用模块
3.模块化的导出
   module.exports 默认是一个空对象 给该对象添加属性,将内容导出;
   module.exports.名称 =  要导出的内容
   exports.名称 = 要导出的内容
 
 var 导出项 = require('模块路径')
```

### 26. 模块化标准

```javascript
1.AMD:异步模块定义 require.js 依赖前置
2.CMD:通用模块定义 sea.js  依赖延迟(后置)
3.CommonJs: Node.Js模块化标准 
```

### 27. 封装页面跳转函数

```javascript
function(url){
        this.statasCode = 302;
        this.statusMessage = "Found";
        this.setHeader('location',url);
        this.end();
    }
```

### 28. Express介绍及路由注册方式

```javascript
Express:基于node.js的web开发框架.
npm install express //下载资源
var express = require('express');//引入资源包
var app = express(); //创建实例
```

```javascript
1. app.METHOD方式,请求方式必须与方法名一致;请求路径必须与注册路径一致;
app.get(路由路径,function(req,res){
  
}),
app.post(路由路径,function(req,res){
  
})
2. app.all 方式:任意请求方式都可以,请求路径必须与注册路由一致;
app.all(路由路径,function(req,res){
  
})
3. app.use方式:任意请求方式都可以,请求路径只要以注册路由路径开头即可!省略路由路径,默认为"/"
app.use(路由路径,function(req,res){
  
})
```

### 29.Express中req,res新增方法与属性

```javascript
1. response
    res.send(); //向浏览器相应数据 ,可接受的参数:对象,数组,字符串,数字(作为状态码使用),buffer对象
    res.download(path.join(__dirname,'1.jpg'),'');//向浏览器响应文件 并下载
    res.status(404).end();
    res.json({name:"assasin",age:25});
    res.jsonp({name:"assasin",age:25});//返回jsonp合适的数据,请求时需要传递callback函数
    res.redirect("http://baidu.com");//重定向
    res.sendFile(path.join(__dirname,'1.jpg')); //返回文件至浏览器
2. request
    req.body; //获取POST请求参数,但不能直接使用
    req.query; //获取GET请求参数,返回对象 可直接使用
    req.originalUrl; //获取原始url地址 类似于req.url
    req.params; //获取路由参数 返回参数对象
```

### 30. Express托管静态资源

```javascript
app.use(express.static('public'));
```

### 31.Express中间件

```javascript
中间件起始是一个函数,参数是req,res,next;next响应下一个请求

app.get('/index',function(req,res,next){
    //要么结束响应,要么调起下一个
    next();
    //express默认有一个中间件,用来兜底的
})
```

### 32.获取POST请求参数

```javascript
通过req.body 可以获取POST请求参数,但必须使用body-parser中间件;
npm install body-parser

var bodyParser = require('body-parser');
app.use(bodyParser.urlencoded());
app.post('/api',function(req,res){
    console.log(req.body);
})
```

### 33.自己实现body-parser中间件

```javascript
var querystring = require('querystring');
app.use(function(req,res,next){
    //获取Post请求参数,添加给req作为body属性
    var bufferlist = [];
    req.on('data',function(chunk){
        bufferlist.push(chunk);
    })
    req.on('end',function(){
        var result = Buffer.concat(bufferlist);
        var result = result.toString()
        //判断浏览器请求时发送的数据格式
       // console.log(get('content-type'));
        if(req.get('content-type').indexOf('json') != -1){
            req.body = JSON.parse(result);
        }else if(req.get('content-type').indexOf('urlencoded') != -1){
            req.body = querystring.parse(result);
        }else{
            req.body =  {};
        }
    })
    next();
})

app.post('./add',function(req,res){
    console.log(req.body);
})
```

### 34. Express中模板引擎使用

```javascript
// 模板引擎的使用  app.engine(后缀名,require('express-art-template')) 用于为指定的后缀的模板指定对应的模板引擎
// npm install art-template
// npm install express-art-template
// 1.定义 HTML 对应的模板引擎
app.engine('html',require('express-art-template'));
// 2. 设置模板引擎所在目录,若不设置,默认去views中去找
//app.set('views',要指定的模板文件所在目录路径)
app.set('views',path.join(__dirname,'views'));

//3. 设置模板文件的默认后缀,调用render方法时文件后缀
app.set('view engine','html');
```

### 35.  Express-hacker-news

```javascript
1.资源包
npm install express
npm install art-template
npm install express-art-template
npm install body-parser
```

```javascript
2. app.js
var express = require('express');
var app = express();
var path = require('path');
var storage = require('./storage');

app.use(require('body-parser').urlencoded());
app.use(require('body-parser').json());
app.use('/resources',express.static('resources'));//静态资源
//配置模板引擎
app.engine('html',require('express-art-template'));
app.set('view engine','html');

app.get('/index',function(req,res,next){
    //读取数据
    storage.getallnews(function(newslist){
        res.render('index',{list:newslist});
    })
    //res.sendFile(path.join(__dirname,'views','index.html'));
})

app.get('/details',function(req,res,next){
    storage.getnewsbyid(req.query.id,function(news){
        res.render('details',{item:newslist});
    })
    //res.sendFile(path.join(__dirname,'views','details.html'));
})

app.get('/submit',function(req,res,next){

    res.sendFile(path.join(__dirname,'views','submit.html'));
})

app.get('/add',function(req,res,next){
    storage.addnews(req.query,function(){
        res.redirect('index');
    })
})
app.post('/add',function(req,res,next){
    storage.addnews(req.body,function(){
        res.redirect('index');
    })
})
app.listen(8888,function(){
    console.log("http://localhost:8888");
})
```

```javascript
3. storage.js
var fs = require('fs');
var path = require('path');
var NEWSPATH = path.join(__dirname,'data.js');
module.exports = {
    //读取所有数据
    getallnews:function(callback){
        fs.readfile(NEWSPATH,'utf8',function(err,data){
            data = JSON.parse(data || '[]');
            callback(data);
        })
    },
    //读取id
    getnewsbyid:function(id,callback){
        this.getallnews(function(newslist) {
            var news = newslist.find(function(v,i){
                return v.id == id;
            })
            callback(news);
        })
    },
    addnews:function(){
       this.getallnews(function(newslist){
           news.id = newslist.length==0?1:newslist[newslist.length -1 ].id +1 ;
           newslist.push(news);
           fs.writefile(NEWSPATH,JSON.stringify(newslist),function(err){
               callback();
           })
       })
    }

}
```

### 36. Mongodb

```javascript
1.启动mongodb服务: mongod --dbpath 数据库文件所在目录
                  mongo --host 127.0.0.1 --port 27017
2.连接操作mongodb: mongo
3.查看现有数据库: show databases;
3.切换数据库: use 数据库名;
4.查看数据库中所有集合: show collections;
5.增: db.集合名称.insert(数据对象)
      db.集合名称.insertMany([数据对象1,数据对象2])
6.删: db.集合名称.deleteOne(条件对象)
      db.集合名称.deleteMany(条件对象)
7.改: db.集合名称.updateOne(条件对象,操作对象)
      db.集合名称.updateMany(条件对象,操作对象)
      db.db.users.updateMany({name:'shibin'},{$set:{age:18}}) //修改name=shibin的年龄为18
8.查:  db.集合名称.find() //查询所有数据
       db.集合名称.find(条件对象)  //条件查询
       条件对象 {age:{$gte:18}} //年龄大于等于18
        {age:{$lt:18}} //小于
        {age:{$gt:18}} //大于
        {age:{$lte:18}} //小于等于
        {age:{$gte:18}} //大于等于
        {age:{$ne:18}} //不等于
        {age:{$in:[18,28]}} //在一个范围内
        {age:{$nin:[18,28]}} //不在这个范围内
```

### 37. Nodejs操作Mongodb

```javascript
npm install mongodb  
//1. 引入mongo客户端
var  mongoClient = require('mongodb').MongoClient;
//2. 创建连接字符串
var connStr = "mongodb://localhost:27017";
//3.调用mongoClient 方法 connect() 进行连接
mongoClient.connect(connStr,function(err,client){
    //console.log('ok');
    //client数据库客户端对象
    //进行数据库增删改查操作
    var db = client.db('test');
    //通过dbd对象操作数据库集合
    var users = db.collection('users');
    //新增数据
    //db.collection('users').insert();
    users.insert({name:"史俊祥",age:25},function(err,dbresult){
        console.log(dbresult.result);
    })
    users.insertMany([{name:"www",age:28},{name:"qqq",age:21}],function(err,dbresult){
        console.log(dbresult.result);
    })
    //查询数据
    users.find({age:18}).toArray(function(err,arr){
        console.log(arr);
    })
    users.find({age:{$gt:18}}).toArray(function(err,arr){
        console.log(arr);
    })
    //删除数据
    users.deleteOne({age:18},function(err,dbreslt){
        console.log(dbreslt.result);
    })
    //修改数据
    users.updateOne({age:28},{$set:{sex:'female'}},function(err,dbresult){
        console.log(dbresult.result);
    })
    
    //最后一步,关闭数据库链接
    client.close();
})
```

### 38. 创建storage.js操作Mongodb

```javascript
var mongoClient = require('mongodb').MongoClient;
var ObjectId = require('mongodb').ObjectId;
var connStr = "mongodb://localhost:27017";
var DBNAME = 'test';
var COLLECTION_NAME = 'news';
module.exports = {
    getallnews:function(callback){
        mongoClient.connect(connStr,function(err,client){
            var db = client.db(DBNAME);
            var news = db.collection(COLLECTION_NAME);

            news.find().toArray(function(err,arr){
                console.log(arr);
            })
            //关闭数据库链接
            client.close();
        })
    },
    getnewsbuid:function(id,callback){
        //将mongodb中的_id 转化 Objectid
        // 将 ObjectId('njbfjdjfskdj45s4df54dsf') 传进去即可;
        mongoClient.connect(connStr,function(err,client){
            var db = client.db(DBNAME);
            var news = db.collection(COLLECTION_NAME);
            db.find({_id:id}).toArray(function(err,arr){
                callback(arr[0]);//取数组第一项即可
            })
            //关闭数据库链接
            client.close();
        })
    },
    addnews:function(info,callback){
        mongoClient.connect(connStr,function(err,client){
            var db = client.db(DBNAME);
            var news = db.collection(COLLECTION_NAME);
            //添加数据
            news.insertOne(info,function(err,dbresult){
                if(dbresult.result.ok == 1){
                    callback();
                }
            })
            //关闭数据库链接
            client.close();
        })
    },
}
```

### 39. hacker-news-api

```javascript
前后端分离实现数据接口
api.js

var express = require('express');
var storage = require('storage');
var app = express();

//设置cors跨域
app.use(function(req,res,next){
    res.header("Access-Control-Allow-Origin","*");
    res.header("Access-Control-Allow-Method","GET,PUT,POST,DELETE");
    res.header("Access-Control-Allow-Headers","Content-Type");
    next();
})
app.get('/newslist',function(req,res,next){
    storage.getallnews(function(newslist){
        res.send({
            errCode:200,
            msg:'success',
            data:newslist
        });
    })
})

api.get('/details',function(req,res,next){
    storage.getnewsbyid(req.query.id,function(news){
        res.send({
            errCode:200,
            msg:'success',
            data:news
        });
    })
})
app.get('/addnews',function(req,res,next){
    storage.addnews(req.query,function(){
        res.send({
            errCode:200,
            msg:"success",
            data:''
        });
    })
})

app.listen(8888,function(){
    console.log("http://localhost:8888");
})

```

### 40. npm上传资源包

```javascript
npm 官方网站注册账号
1. npm init 初始化资源包
2. 创建资源包
3. nrm use npm 切换至npm
4. npm login  登录npm
5. npm publish 发布资源包(不能与npm已有资源命名冲突)
6. npm version major/minnor/patch  修改版本号
```

### 41. npm编写命令行工具

```javascript
1. 创建资源包
2. 实现命令行工具的功能
3. 在package.json中增加
   "bin":{
    "mkassasindir":"./index.js"
    //命令别名:执行文件路径
  }
4. 在资源最上方添加 #! node
5. 上传资源包至npm  npm publish
6. 全局安装  npm install 资源包名 -g
7. 执行自己的命令行
```

### 42. ES6语法

``` javascript
ES6新增功能:
1. 变量声明的方式 let
let 变量名  (不能重复声明;有块级作用域)
2. const声明常量 不可重复声明;也有块级作用域;不能被修改值;
3. 对象的简写形式
   属性简写:属性名与属性值的变量名同名;
   方法简写:
   let obj{
     sing:function(){
       console.log('sing')
     }
   }
   可写成
   let obj{
     sing () {
       console.log('sing')
     }
   }
4.  对象解构赋值
let {对象的属性名:要声明的变量名} = 对象
let {name:name} = obj; //将对象的name属性值赋值给name变量
5. import {要导入的内容1,要导入的内容2} from "模块"
6. 数组解构赋值
    let arr = [1,2,3,4];
    let [num1,num2,num3,num4] = arr;
    
    let arr = [1,2,3,4,5]; //剩余元素只能有一个;只能为最后一个元素
    let [num1,...num2] = arr; // [ 2, 3, 4, 5 ]
7. 箭头函数
   let func2 = (参数列表) =>{
    console.log(2222);//函数体
  }
  func2();
  ###简写形式###
  如果参数列表中只有一个参数,那么参数()可以省略;
   let f1 = (a) => {
      console.log(a *2);
  }
  let f2 = a => {
      console.log(a *2);
  }
  f2(10);
  如果箭头函数的函数体只有一句代码,函数体的{}可以省略;
  let f1 = (a) => {
    console.log(a *2);
    }
  let f3 = a => console.log(a / 10);
  如果箭头函数的函数体只有一句代码,并且这句代码是返回语句,那么return与{}都可省略;
  let sum = (a,b) =>{
    return  a +b ;
  }

	let sum = (a,b) => a+ b;
8. this的用法
   this的四种调用模式:
   函数调用: this--->window  函数名()
   方法调用: this--->调用 方法的对象  对象.方法名()
   构造函数调用:this--->创建出来的实例 new 函数名()
   上下文调用模式:this--->call和apply的第一个参数 函数名.call()
   
   箭头函数中没有this,若果使用this,this会向上一级作用域中查找.
   使用var that = this的场景全都可以使用箭头函数解决
   // let obj = {
//     name :"纯生",
//     say (){
//         var that = this ;
//         setTimeout(function(){
//             console.log("我叫"+that.name);
//         },1000)
//     }
// }
let obj = {
    name :"纯生",
    say (){
        setTimeout(() =>{
            console.log("我叫"+this.name);
        },1000)
    }
}
obj.say();
9. 箭头函数中没有arguments的说明:
   arguments在函数被调用时,会将所有的实参存贮到arguments对象中
   使用场景:传递参数个数不确定时,使用arguments获取所有实参
   function sum(){
    var result = 0;
    for (let i = 0; i< arguments.length;i++){
        result += arguments[i];
    }
    return result;
  }
  res = sum(1,2,3,5);
  console.log(res);
10. 剩余参数 rest params 只能有一个,并且只能是参数列表的最后一个
    function func(b,...a){
    console.log(a);
  }
  func(1,2,3,4);
  箭头函数获取不定个数的参数,就可使用剩余参数.
11. 函数参数默认值的使用
12. 使用变量值作为属性名:
    var key = "name";
    var obj = {
        [key]:"assasin",
    }
    console.log(obj);
13. 对象扩展运算符
     var obj = {
    name :"assasin",
    age:25
   }

  var ass = {
      ...obj
  }
  console.log(ass);
14. 运算符在数组传(参)递时的使用
    function sum(a,b,c){
    return  a + b + c;
}
var arr = [2,3,5];
console.log(sum(...arr));
15. Class使用
   // function Person(name,age){
//     this.name = name;
//     this.age = age;
// }
//


class Person {

    //静态方法 es6不支持静态属性
    static sayhi(){
        console.log("sayhi方法");
    }
    //构造函数
    constructor(name,age){
        this.name = name;
        this.age = age;
    }
    //实例方法
    say(){
        console.log("say方法");
    }
}
//继承
class Student extends Person{

}
var stu = new Student();
console.log(stu);
console.log(stu.say());
Person.sayhi();
var p = new Person('assasin',28);
console.log(p);

16.super调用的原因:
function Person(){
    this.name = "assasin";
    this.age = 25;
}
function Student(){
    Person.call(this);
}
var stu = new Student();
console.log(stu);


class Person{
    constructor(){
        this.name = "assasin";
        this.age = 25;
    }
}

class Student extends Person{
    constructor(){
        super();
    }
}
   
```

### 43.Promise

```javascript
promise用于解决回调地域问题.支持promise就可使用.then()方法来使用回调函数,
.then(成功的回调函数,失败的回调函数)
promise对象有三种状态:
① pending:挂起,正在执行
② fullfilled:成功
③ rejected:失败
.then()连写时,需要在回调函数中返回一个新的Promise对象

//promise APi
function timeout(time){
   return new Promise(function(resolve, reject){
       //要执行的内容
       setTimeout(function(){
         // console.log("1s");
           //异步操作完成后,需要改变当前promise状态即可
           //resolve调用改为成功
           //reject调用艾薇失败
           resolve(123);
          // reject();
       },time)
   });
}
//调用
timeout(1000).then(function(data){
    //要做的事情
    console.log("1s",data);
},function(err){
    console.log(err);
})
```

```javascript
封装ajax:
function ajax(){
    return new Promise((resolve, reject)=>{
        //发送ajax请求
        let xhr = new XMLHttpRequest();
        xhr.open(option.type,option.url);
        xhr.send(null);
        xhr.onreadystatechange = function(){
            if(xhr.readyState == 4 ){
                if(xhr.status == 200){
                    resolve(xhr.responseText);
                }else{
                    reject();
                }
            }
        }
    });

}
//调用
ajax({
    url:"",
    data:{},
    type:"get"
}).then(function(data){
   console.log(data);
})

```

```javascript
promise静态方法all,race说明
all:在所有promise异步操作完成后,执行某个任务就可使用
race:在promise第一个异步完成后,就执行某个任务
```

### 











 



