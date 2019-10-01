# Django   

Django是Python的web开发框架.

```
与MVC不同,属于MVT框架
model负责与数据库交互
view是核心,负责请求数据,获取数据返回结果
template负责呈现内容到浏览器
```

### 安装Django

```
pip install django 
```

### 创建项目

```
django-admin startproject 项目名称(assasin)
```

### 数据库配置

```
在settings.py文件中,通过DATABASES项进行数据库设置
Django支持的数据库包括:sqlite,myaql等主流数据库
Django默认使用SQLite数据库
```

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

### 创建应用

```
python manage.py startapp 应用名称(booktest)
```

### 运行项目

```
python manage.py runserver 端口号 默认8000
```

### 激活模型:编辑settings.py将booktest(应用名称)添加到installed_apps中

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'booktest'
]
```

### 生成迁移:根据模型类生成SQL语句

```
python manage.py makemigration
```

### 执行迁移

```
python manage.py migrate
```

### 测试数据操作

```
python manage.py shell
```

### 创建管理员用户

```
python manage.py createsuperuser,按提示输入用户名,密码
```



