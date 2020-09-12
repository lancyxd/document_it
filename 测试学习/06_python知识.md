## 1、python理论

[python3文档内容](https://docs.python.org/zh-cn/3/contents.html)

[十五分钟让你了解Python套路](https://www.jianshu.com/p/36ae91c38279)

python特性是动态语言

### 1.1  基本语法

- **name=='main'说明**
  如果py文件作为模块被导入(import)，那么name就是该py文件的文件名(也称 模块名)；
  

if __name__ == '__main__'的意思是：当.py文件被直接运行时，if __name__ == '__main__'之下的代码块将被运行；当.py文件以模块形式被导入时，if __name__ == '__main__'之下的代码块不被运行。

- **init.py** 文件的作用是将文件夹变为一个Python模块,Python 中的每个模块的包中，都有__init__.py 文件。
  
  包：将文件夹当做模块，目录中必须包含 __init__.py 文件
  
- **python注释**

  注释（推荐四个空格缩进）：多行 '''注释内容'''或者"""注释内容"""  单行 #注释内容

  ```python
  '''
  @Author:xxx
  @File:my_name.py
  @Time:2019/10/14 22:02
  '''
  ```

- **列表、元祖、字典**

  ```python
    # 列表用[ ]标识。是python最通用的复合数据类型，是有序的对象结合；列表alist=[4,5,7]
    for i in range(len(alist)):
    for i in alist:
    for index,element in enumerate(alist):
    for i in enumerate(alist):
    for i in iter(alist):
    a=['one','two','three']
    逆序输出列表       for i in a[::-1]:
    正序输出列表       for i in a:
    按逗号分隔列表    ','.join(str(n) for n in a)
  
    # 元组用"()"标识。内部元素用逗号隔开。但元组不能二次赋值，相当于只读列表。不可变性atuple=(1,2,3)。
    for i in range(len(atuple))
   
    # 字典用大括号{}包裹，由索引(key)和它对应的值value组成，是无序的对象集合。adict={'x':1, 'y':2, 'z':3}
    for key in  adict:
    for key,value in  adict.items():
    
    # Numpy数组   array([1, 2, 3])
    # 集合 {1, 2, 3}（a = set()创建空集合只能用set创建，在Python中{}创建的是一个空的字典）
    #不可变集合（frozen set）使用 frozenset 来进行创建：s=frozenset([1,2,3,'a',1])
    
    字符串    s = "hello world"（不可变）
    不想导入的属性名称加上一个下划线( _ )
  
    dir(list)查看函数的功能 // dir(dict)
    type(a) 查看a的类型
    isinstance(i, int) 判断i是否为int类型
    返回当前工作目录os.getcwd()  当前操作系统的换行符os.linesep
    print squre.__doc__  //打印函数的注释
    id(x)  返回变量x的内存地址
    help(os)查看帮助，按q键退出
    
    import this  # python彩蛋
    默认第三方库安装路径/usr/lib64/python2.7/site-packages/PIL
    字体元素路径：/usr/lib64/python2.7/site-packages/matplotlib/mpl-data/fonts/ttf
    文件操作：打开；写入；读取；关闭；定位；改名；删除；写入时会把原来的覆盖掉。close() 方法允许调用多次
    print(1,2+3)
    list(range(0,4))
    range(0,4)适用于for循环的变量控制
    字符串以 16-bit Unicode 字符串存储
    try except语句变化:  try:    ....    except Exception as e:    ....
    s = "张三abc12"，b = s.encode( 编码方式)
    b 就是 bytes 类型的数据
    常用的编码方式为 ： "uft-16"    , "utf-8", "gbk", "gb2312", "ascii" , "latin1" 等，当字符串不能编码为指定的“编码方式”时，会引发异常
    
    print(r'C:\some\name')
    import xxx  # 模块导入，注意：自己创建模块时要注意命名，不能和python自带的模块名重名
    import xxx as xx
    from xxx import xx 
    单引号和双引号区别：单引号（''）或双引号（""）都可表示字符串，多行字符串可以使用三重引号'''或 """ 
  ```

### 1.2 python注意点

  ```python
交互式编程：不需要创建脚本文件，在命令行中输入python。（ll /usr/bin/python*）
执行脚本文件：python test.py     ./test.py
魔术字符串：#!/usr/local/bin/python（帮你在系统搜索中找到指定python解释器路径）
UNIX下，python解释器的路径一般为/usr/local/bin/python或/usr/bin/python
查看版本：python3 -V		 sys.version
  
# -*- coding=utf-8 -*-，允许使用中文或者其它非ASCII码
ASCII编码和unicode编码区别：ASCII编码是1个字节，而Unicode编码通常是2个字节；字母A用ASCII编码是十进制的65，二进制的01000001；A的Unicode编码是00000000 01000001

文件(*.py源码文件，由python程序解释；*.pyc源码经编译后生成的二进制字节码文件；*.pyo优化编译后的程序)
  
Python对对空格和缩进很敏感；一句话有冒号的下一行往往要缩进，该缩进就缩进；变量赋值不需要类型声明；括号在pyhton中不是必须的；
pass是空语句，是为了保持程序结构的完整性。一般用作占位语句。
break（跳出整个循环）和continue（跳出本次环，继续执行下一轮循环）
退出系统：exit()   ctrl+D退出 ctrl+z     sys.exit()
在同一行中使用多条语句，语句之间使用分号;分割
  
模块的搜索路径:先在当前目录中搜索，如果找不到，则在sys.path变量中给出的目录列表中查找。sys.path（输入脚本的目录（当前目录）;环境变量 PYTHONPATH 表示的目录列表中搜索;Python 默认安装路径中搜索。
（临时生效）：动态增加路径sys.path.append(‘/home/wang/wokespace’)     
（永久生效）vim ~/.bashrc，将内容export PYTHONPATH=$PYTHONPATH:/home/wang/workspace 附加到文件末尾，修改PYTHONPATH变量。
  
bytes和str两种类型转换的函数encode(),decode(),str通过encode()可以编码为指定的bytes。若从网络或磁盘上读取了字节流，则读到的数据就是bytes。bytes变为str，用decode()方法
'/'和‘//’的区别：只有一个反斜杠，会输出完整运算结果;两个反斜杠时，只输出整数。5/2  2.5  5//2  2
5**2=25
  
b'hello'    python对bytes类型的数据用带b前缀的单引号或双引号表示
x = b'xxx'转换为真正的（Unicode）字符串   str(x, 'utf-8')
加r和不加区别：'r'是防止字符转义，若路径中出现'\t'，不加r，'\t'会被转义。加'r'之后'\t'就能保留原有的样子。
  ```

  ![08_03_python模块导入](../image/08_03_python模块导入.png)

  

![08_03python包导入用法](../image/08_03python包导入用法.png)

![08_03_python语法](../image/08_03_python语法.png)

![08_03_python函数入参.png](../image/08_03_python函数入参.png)

## 2 python网络框架

`4种Python网络框架：Django、Tornado、Flask(支持快速建站的框架)、Twisted(底层自定义协议网络框架)。`

`要性能Tornado 首选; Tornado 适合高度定制，适合访问量大，异步情况多的网站。也可以用于定制api服务。`

`要开发速度，Django; 适合初学者或小团队的快速开发，适合做管理类、博客类网站、或者功能十分复杂需求十分多的网站。`

### 2.1  **Django框架（企业级开发框架）MVC框架**

[Django文档](https://docs.djangoproject.com/zh-hans/2.0/)

`url请求-->访问路由系统(负责分发请求到相应视图函数)-->视图函数(处理请求)-->DataBase(数据库操作数据生成对应页面返回给用户)`

```shell
# 安装 django相关命令
pip3 install Django==2.1.1
python3 
sudo python3 -m pip install --upgrade pip //pip升级
sudo pip3 install -U Django==2.0.0 //升级Django版本
python3 -m django --version
print(django.get_version())
django-admin version
django-admin help（备注：django-admin应该使用python3.6版本）

# 创建项目(实战eg) https://docs.djangoproject.com/zh-hans/2.0/intro/tutorial01/
django-admin startproject mysite //在当前目录下创建一个 mysite 目录
python3 manage.py runserver
python3 manage.py runserver xx.xx.xx.xx:8800
访问:http://xx.xx.xx.xx:8800/
备注：settings.py中如下进行设置ALLOWED_HOSTS = ['xx.xx.xx.xx']

创建投票应用（重点关注 views.py和urls.py、models.py ）
python3 manage.py startapp polls //创建应用，在 manage.py所在的文件夹下
python3 manage.py runserver 127.0.0.1:8800
http://127.0.0.1:8800/polls/
        
python3 manage.py makemigrations polls //'polls.apps.PollsConfig'写入INSTALLED_APPS python3 manage.py sqlmigrate polls 0001
python3 manage.py migrate //移植到数据库，在数据库创建对应的数据表
python3 manage.py check //用于检查项目中的问题
# python api交互
python3 manage.py shell
创建一个管理员账号： python3 manage.py createsuperuser 用户名·admin 密码123abc23邮箱admin@example.com 
templates 目录：将页面的设计从代码中分离出来，Django会在templates目录里查找模板文件。
python3 manage.py test polls //运行测试用例
```

### 2.2  **Tornado框架（高并发处理框架）**

[tornado用户指南](https://tornado-zh.readthedocs.io/zh/latest/guide.html)

`Tornado 是一个Python web框架和异步网络库，通过使用非阻塞网络I/O, Tornado 可以支持上万级的连接，处理 长连接, WebSockets, 和其他需要与每个用户保持长久连接的应用`

```shell
# 安装：pip3 install tornado
tornado利用epoll解决了c10k问题,用一个进程/线程来同时处理若干个连接的想法，减少了硬件资源的浪费。
c10常见场景 两种高并发场景：用户量大，高并发。如秒杀抢购，双十一，618和春节抢票。大量的HTTP持久连接。
核心模块 ioloop模块: tornado.ioloop.IOLoop.current().start() IOLoop类，current()类的一个方法 结果是返回一个当前线程的IOLoop的实例，start()也是IOLoop的方法，调用后开启循环。epoll负责监控。
Tornado异步处理(请求特别耗时，把它丢在哪里，继续处理下一个请求，确保请求不会卡死)
```

![08_03_tornado](../image/08_03_tornado.png)

