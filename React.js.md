## React.js--15.3.2

```
Github: https://facebook.github.io/react/
```

```
官方网站: https://reactjs.org/
```

```
查看历史版本: https://facebook.github.io/react/blog/all.html
             https://cdnjs.com/libraries/react
```

```
npm配置环境:
1. cnpm init 初始化cnpm
2. cnpm install --save react react-dom babelify babel-preset-react
   cnpm install --save react@15.3.2 react-dom babelify babel-preset-react
3. cnpm install babel-preset-es2015 --save
```

```
webpack打包:
1. cnpm install -g webpack
   cnpm install  webpack --save
2. cnpm install -g  webpack-dev-server
   cnpm install  webpack-dev-server --save
3. webpack
4. webpack --watch
5. webpack -dev-server 
6. webpack -dev-server --contentbase src --inline --hot
```

### 1. CDN引入资源包

```
<script src="https://cdn.bootcss.com/react/16.4.0/umd/react.development.js"></script>
<script src="https://cdn.bootcss.com/react-dom/16.4.0/umd/react-dom.development.js"></script>
<script src="https://cdn.bootcss.com/babel-standalone/6.26.0/babel.min.js"></script>
```

### 2.react Api

```
1. React.createElement(创建什么类型的对象(标签名),创建的元素的属性(对象),创建的对象的内容)
   React.createElement('div',{id:'box',title:"haha",className:'heihei'},'我是div的内容')
2. ReactDom.render(创建出的选项,渲染的位置);
   ReactDom.render(element,document.getElementById('app'));
3. 
```





