# Vue.js

### 1. 项目搭建

```
1. npm install -g vue-cli
2. vue init webpack my-project
3. npm run dev /npm start 运行
4. npm run build 打包
```

### 2. element-ui 使用

```
1. npm install element-ui -S 
2. main.js中写入以下内容
   import ElementUI from 'element-ui'
   import 'element-ui/lib/theme-chalk/index.css'
   Vue.use(ElementUI)
```

### 3. 安装axios

```
npm install axios 或 npm i -S axios

import axios from 'axios'
```

### 4. 页面跳转

```
1.  this.$router.push('/home')
```

### 5. 导航守卫

```
router.beforeEach((to,from,next) =>{
  if (to.path === '/login'){
      next();
  }else{
    /*判断是否有token*/
    const token = localStorage.getItem('token')
    if(token){
      next()
    }else{
      next('/login')
    }
  }
})
```

### 6.

### 7.

### 8.

### 9.

### 10.

### 11.

### 12.



