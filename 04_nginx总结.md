`Nginx使用基于事件驱动的架构能够并发处理百万级别的TCP连接；epoll是Linux上处理大并发连接的利器；Nginx以性能为王（更快，高扩展，高可靠，低内存消耗，单机支持10万以上的并发连接，热部署）。`

**正向代理：客户端知道服务器端**，通过代理端连接服务器端。代理端代理的是服务器端。

反向代理：所谓反向，是对正向而言的。服务器端知道客户端，客户端不知道服务器端，通过代理端连接服务器端。代理端代理的是客户端。（客户端C发给ngnix，ngnix代理服务器通过某种算法分发给服务器S）

## 1、应用

- **反向代理**

  ```powershell
  # 关键命令proxy_pass,   localhost 28000转发至 localhost 38000
  server{
          listen 28000;
          location ~* /{
          proxy_pass  http://127.0.0.1:38000;
          }
   }
  ```

  

- **负载均衡**

  ```powershell
  # upstream, 将localhost 18000端口的请求 均分到 localhost 38000 和 localhost 39000两个服务上
  
  # 1）RR轮询方式，每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。（轮询算法：把来自用户的请求轮流分配给内部的服务器：从服务器1开始，直到服务器N，然后重新开始循环）
  
  # 2）权重 weight：必须实现session 共享，否则导致用户session不同步，导致用户重新登陆
  upstream cssearch{
       server localhost:38000 weight=9;  #请求的 90% 进入到8080服务器
       server localhost:39000 weight=1;
  }
  server{
          listen 18000;
          server_name  csserver;
          client_max_body_size 1024M;
  
          location ~* /{
          proxy_pass  http://cssearch;
          proxy_set_header Host $host:$server_port;
        }
   }
   
  # 3）ip_hash：每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可解决session的问题
  upstream cssearch{
       ip_hash;
       server localhost:38000; 
       server localhost:39000;
  }
  # 4）fair(第三方)：按后端服务器的响应时间来分配请求，响应时间短的优先分配
  # 5）url_hash(第三方)：问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效 ???
  ```

  

- **安全加密**    [在线 htpasswd 生成器](https://tool.oschina.net/htpasswd)

  ```powershell
  server {
          listen       9201;
          location ~* / {
          auth_basic "login";#提示信息
          auth_basic_user_file /usr/local/nginx/conf/pwd.conf;
          autoindex on;
          proxy_pass  http://127.0.0.1:9200;
         client_max_body_size   200m;
     }
  }
  # /usr/local/nginx/conf/pwd.conf目录下添加密码文件：pwd.conf，重新加载配置文件即可
  ```

  

## 2、路径匹配location



```shell
1) 基本语法
location [=|~|~*|^~] /uri/ { … }

/ 通用匹配，任何请求都会匹配到
~*开头表示不区分大小写的正则匹配
~ 开头表示区分大小写的正则匹配
!~和!~*分别为区分大小写不匹配及不区分大小写不匹配的正则
^~ 开头表示uri以某个常规字符串开头,理解为匹配 url路径。nginx不对url做编码,因此请求为/static/20%/aa,可以被规则^~ /static/ /aa匹配到（注意是空格）

2) 举例说明
匹配优先级: 精确匹配 =-》其次以xx开头匹配^~-》然后是按文件中顺序的正则匹配-》最后是交给 / 通用匹配。
当有匹配成功时候，停止匹配，按当前匹配规则处理请求。
location ~* \.(gif|jpg|jpeg|png|css|js|ico)$ {}    //以xx结尾    http://localhost/a.PNG 
location ^~ /static/ {}      //以xx开头          访问 http://localhost/static/a.html 
location = / {}  //精确匹配      访问根目录/， 比如http://localhost/ 
```

##  3、nginx命令

- **命令**

```powershell
ps -ef|grep nginx # 查找nginx进程(看下主目录是哪里) 
./nginx -c /etc/nginx/nginx.conf # (指定配置文件所在的路径)，启动nginx(其实属于后台启动)     
./nginx -t -c /etc/nginx/nginx.conf # 验证配置文件是否合法            
./nginx  -h # 命令帮助 
./nginx -s reload -c /etc/nginx/nginx.conf # 重新加载配置文件(即可生效)              
./nginx -V # 查看nginx的版本 
./nginx -s quit # 关闭旧worker进程 
/usr/local/nginx/sbin/nginx -s stop		# 优雅退出

# nginx默认路径: /usr/local/nginx/conf/nginx.conf
# 需要编辑/etc/sysctl.conf文件，即可手工或自动执行由sysctl控制的功能,sysctl-p从指定的文件加载系统参数，如不指定即从/etc/sysctl.conf中加载。
```

## 4、配置文件优化 

[Nginx配置性能优化](https://blog.csdn.net/xifeijian/article/details/20956605)

```shell
1)高层配置
user和pid应该按默认设置 ; worker_processes设置可用cpu核数 ; worker_rlimit_nofile 更改worker进程的最大打开文件数限制。

#user  nobody;
#pid  logs/nginx.pid;
worker_processes  auto;
worker_rlimit_nofile 100000;
error_log  logs/error.log  error; （debug、 info、 notice、 warn、 error、 crit、 alert、
emerg， 从左至右级别依次增大。 当设定为一个级别时， 大于或等于该级别的日志都会被输）

2)Events模块(包含所有处理nginx连接的设置)
worker_connections 设置可由一个worker进程同时打开的最大连接数;
multi_accept 告诉nginx收到一个新连接通知后接受尽可能多的连接;
use 设置用于复用客户端线程的轮询方法。如果你使用Linux 2.6+，你应该使用epoll。如果你使用*BSD，你应该使用kqueue。

events {
        worker_connections 10240;
        multi_accept on;
        use epoll;
}

3)Http模块(控制着nginx http处理的所有核心特性)
http { 
server_tokens off; 
sendfile on; 
tcp_nopush on; 
tcp_nodelay on; 
reset_timedout_connection   on;
limit_conn_zone             $binary_remote_addr zone=addr:5m; 
limit_conn addr             65535;
include             mime.types;
default_type        application/octet-stream;
charset             UTF-8;
open_file_cache_errors on; 

... 
}

access_log 设置nginx是否将存储访问日志。关闭这个选项可以让读取磁盘IO操作更快(aka,YOLO)
error_log 告诉nginx只能记录严重的错误：
keepalive_timeout  给客户端分配keep-alive链接超时时间(单位秒)。服务器将在这个超时时间过后关闭链接。
client_header_timeout 和client_body_timeout 设置请求头和请求体(各自)的超时时间。我们也可以把这个设置低些。
reset_timeout_connection 告诉nginx关闭不响应的客户端连接。这将会释放那个客户端所占有的内存空间。
send_timeout 指定客户端的响应超时时间。这个设置不会用于整个转发器，而是在两次客户端读取操作之间。如果在这段时间内，客户端没有读取任何数据，nginx就会关闭连接。

include 只是一个在当前文件中包含另一个文件内容的指令。这里我们使用它来加载稍后会用到的一系列的MIME类型。
default_type 设置文件使用的默认的MIME-type。
charset 设置我们的头文件中的默认的字符集
open_file_cache_errors 指定了当搜索一个文件时是否缓存错误信息，也包括再次给配置中添加文件。我们也包括了服务器模块，这些是在不同文件中定义的。如果你的服务器模块不在这些位置，你就得修改这一行来指定正确的位置。
```

