## 1、kafka安装

```shell
yum list java*
yum –y install java-1.7.0-openjdk*  //备注：安装kafka需要依赖java jdk
2.	集群安装 (先启动zookeeper;再启动kafka)
注意:kafka有自带的zookeeper;不用kafka自带zookeeper集群，而是自己安装的zookeeper集群(zookeeper集群+kafka)
Zookeeper集群的工作是超过半数才能对外提供服务，3台中超过两台，允许1台挂掉，是否能用偶数，其实没必要。
安装步骤
1) tar –xzvf kafka_2.11-0.8.2.1.tgz #解压
2)创建datalogs文件夹，方便kafka和zk的日志管理，删除logs文件夹中的日志
3) 修改配置文件(编辑修改相应参数)
server.properties
broker.id=2(不可重复，最好用机器ip标识，在集群中必须唯一)
listeners=PLAINTEXT://10.0.0..112:9092 #服务器IP地址，修改为自己的服务器IP
log.dirs=/apps/kafka_2.11-0.9.0.1/datalogs/kafka   #数据存放位置（服务日志默认输出到$KAFKA_HOME/logs目录下）
zookeeper.connect=10.0.0..112:2181,10.0.0..113:2181,10.0.0..114:2181  #zookeeper地址和端口
zookeeper.properties
dataDir=/apps/kafka_2.11-0.9.0.1/datalogs/zk   #zookeeper数据目录
dataLogDir=/usr/local/kafka/log/zookeeper #zookeeper日志目录
clientPortAddress=10.0.0..112
server.2=10.0.0..112:2888:3888(#server.n=ip:portA:portB;n为服务器标识号;ip是服务器ip地址;portA是与leader进行信息交换的端口;portB是在leader宕机后，进行leader选举所用的端口) (.2,此时myid配置为2)
创建/apps/kafka_2.11-0.9.0.1/datalogs/zk目录，在该目录下创建myid文件，在myid中添加服务器标识号。
4)启动(先启动zookeeper;再启动kafka)
./zookeeper-server-start.sh –daemon ../config/zookeeper.properties(-daemon后台启动)
./kafka-server-start.sh –daemon ../config/server.properties
备注：若启动失败，可以若干台机器的zookeeper都启动后，再启动kafka试下。
Myid文件和server.myid  在快照目录下存放的标识本台服务器的文件，他是整个zk集群用来发现彼此的一个重要标识。
5)测试
生产者
./kafka-console-producer.sh –broker-list xx.xxx.xx.xx:9092 –topic test
消费者
./kafka-console-consumer.sh –zookeeper xx.xxx.xx.xx:2181 –topic test
./kafka-console-consumer.sh –zookeeper xx.xxx.xx.xx:2181 –group groupid1–topic topictest

2  kafka单机安装
3.	修改配置文件
server.properties
broker.id=1
listeners=PLAINTEXT://xx.xxx.xxx.xxx:9092
log.dirs=/apps/kafka/datalogs
zookeeper.properties
server.1= xx.xxx.xxx.xxx:2888:3888    (/apps/kafka/zookeeper中myid配置为1)
1)	其它步骤同集群安装
```

## 2、mysql安装

```shell
本地编译机mysql启动：service mysqld start
service mysqld restart
chkconfig –list | grep mysqld  //来查看mysql服务是不是开机自动启动
chkconfig mysqld on //设置成开机启动

连接mysql：mysql –hxx.xxx.xxx.xxx –uroot –pxxxxx

MariaDB（Mysql）和简单配置
安装方法：
1)yum install mariadb  mariadb-server
2)/etc/my.cnf内容:
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# Settings user and group are ignored when  ystem is used.
# If you need to run mysqld under a different user or group,
# customize your  ystem unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd
 
[mysqld_safe]
log-error=/var/log/mariadb/mariadb.log
pid-file=/var/run/mariadb/mariadb.pid
# include all files from the config directory
!includedir /etc/my.cnf.d
4)systemctl start mariadb  启动mariadb
5)systemctl enable mariadb  开机自启动

备注：mariadb如何设置密码
报错解决方法：
mysql报错:Host xx.xxx.xxx.250’ is not allowed to connect to this MariaDB server (在xx.xxx.xx.250运行服务,无法访问xxx.xx.xx.90MariaDB server)
所连接的用户帐号(即xxx.xx.xx.90，root)没有远程连接的权限，只能在本机(localhost)登录。
需更改 mysql 数据库里的 user表里的 host项把localhost改称%( xxx.xx.xx..90机器) use mysql;
1)update user set host = ‘%’  where user =’root’ and host=’localhost’;
2)update user set host = ‘%’ where host = ‘localhost’;
3)flush privileges;
4)select host, user from user;           选择host、user从用户信息表
5)quit;        退出mysql的登录

设置mysql最大连接数
show variables like ‘%max_connections%’; 查看最大连接数
set global max_connections=1000; 修改最大连接数(备注:设置的最大连接数只在mysql当前服务进程有效，一旦mysql重启，又会恢复到初始状态。因为mysql启动后的初始化工作是从其配置文件中读取数据的，而这种方式没有对其配置文件做更改)
通过修改配置文件来修改mysql最大连接数(max_connections)，永久修改。
MySQL配置文件 my.cnf的参数max_connections，改为max_connections=1000，然后重启MySQL即可
```

## 3、nginx安装

```shell
ngnix安装(需要先安装openssl,zlib和pcre,yum命令安装即可;yum install gcc-c++)
准备工作：
gcc            yum install –y gcc-c++
pcre         yum install –y pcre pcre-devel
zib              yum install –y zlib zlib-devel
open-SLL   yum install –y openssl openssl-devel
 
ps  -ef | grep nginx  //master process 后面的就是 nginx的目录
nginx –V//显示 nginx 的版本，编译器版本和配置参数
2.	安装
tar zxvf nginx-1.9.0.tar.gz
./configure && make && make install(&&表示三条一块执行):
执行完后会创建目录/usr/local/nginx,里面包含(conf  html  logs  sbin)
修改配置文件后即可启动nginx
默认会读取/usr/local/nginx.conf文件
3.	启动
/usr/local/nginx/sbin/nginx(./nginx)   可执行文件所在的目录
4.	停止
/usr/local/nginx/sbin/nginx –s stop(quit、reload)  //停止/重新加载
/usr/local/nginx/sbin/nginx –t //验证配置文件是否合法
/usr/local/nginx/sbin/nginx –h //命令帮助
/usr/local/nginx/sbin/nginx –s reload
usrlocal/nginx/sbin/nginx –s quit   //正常地处理完当前所有请求再停止服务。“优雅”地停止服务时， 先关闭监听端口，停止接收新的连接， 然后把当前正在处理的连接全部处理完， 最后再退出进程。
Useradd –M –s /sbin/nologin nginx //nginx报错问题解决

备注：配置文件注意事项
^匹配字符开始
$匹配字符结束
.*   .匹配任意字符，*匹配数量0到正无穷
\. 斜杠用来转义，\.匹配 . 
（jpg|gif|png|bmp）匹配jpg或gif或png或bmp
~     区分大小写（大小写敏感）匹配成功 
~*   不区分大小写匹配成功

```

## 4、openrestry安装和keepalive

```shell
1）openrestry版本  1.11.2.5 （备注:nginx直接内嵌至openrestry中）
安装
tar –xzvf openresty-VERSION.tar.gz
./configure  （默认, --prefix=/usr/local/openresty 程序会被安装到/usr/local/openresty目录）
make
make install

nginx相关配置文件配置(负载均衡)
重点关注以下目录的文件(性能测试199机器为参考)
/usr/local/openresty/nginx/conf.d   *.conf
/usr/local/openresty/nginx/conf     nginx.conf

启动  /usr/local/openresty/nginx/sbin/nginx –c /usr/local/openresty/nginx/conf/nginx.conf
停止  /usr/local/openresty/nginx/sbin/nginx –s stop
重启  /usr/local/openresty/nginx/sbin/nginx –s reload –c /usr/local/openresty/nginx/conf/nginx.conf
检查nginx配置是否正确  /usr/local/openresty/nginx/sbin/nginx –t

2）keepalive + nginx : https://blog.csdn.net/yztezhl/article/details/99765786
keepalive通过虚拟路由冗余协议(vrrp)实现高可用。
虚拟ip  10.100.10.1  真实路由ip  10.100.10.2, 10.100.10.3
安装  yum install -y  keepalive
启动  systemctl  start keepalive

>>>>>>>>>>>>>>>>>>>>>>>>生产环境采用keepalive高可用
10.18.17.194  10.18.17.195(主)，195为主，此时仅195起作用。当195故障时，194起作用，用其配置的nginx实现漂移。（虚拟ip 10.18.17.29/24）
openresty目录   /usr/local/openresty
nginx目录       /etc/nginx
keepalived目录  /etc/keepalived


（域名绑定在虚拟ip上）（虚拟ip绑定至194,195两台机器）194 195路由至192,193服务，交替转发。
仅匹配_bulk,/10058_xxxx请求
server {
        listen 9203;
        auth_basic "login";
        auth_basic_user_file /etc/nginx/iflow.pwd;

        location ~ _bulk$ {
            proxy_pass  http://127.0.0.1:9200;
        }

        location ~ ^/10058_.*/_mapping$ {
            proxy_pass  http://127.0.0.1:9200;
        }
        
        location / {
            return 404;
        }
    }

```

## 5、haproxy安装

```shell
haproxy（负载均衡部署）
安装:yum install haproxy-1.5.18-6.el7.x86_64.rpm
修改配置文件：路径 /etc/haproxy/haproxy.cfg;
  listen push_v2
  bind 0.0.0.0:16000
  option httplog
  option httpclose
  balance roundrobin
  server push_v2 0.0.0.197:14000 check inter 10s fastinter 2s downinter 3s rise 3 fall 2
  server push_v2 0.0.0.199:14000 check inter 10s fastinter 2s downinter 3s rise 3 fall 2
( bind 0.0.0.0:16000，直接访问即可~)
启动：systemctl restart haproxy(修改haproxy.cfg配置文件，执行systemctl restart haproxy命令，即可生效)

```

## 6、redis安装

```shell
1 单机安装
tar zxvf  redis-3.0.5.tar.gz
cd redis-3.0.5
make(若make时报错，原因是jemalloc重载了Linux下的ANSI C的malloc和free函数。解决办法：make时添加参数。Make MALLOC=libc)
进入src文件夹，执行make install进行Redis安装
cp  redis.conf  /usr/redis(redis-benchmark  redis-cli  redis-server)
首先编辑conf文件，将daemonize属性改为yes（表明需要在后台运行）
redis-server /usr/redis/redis.conf(指定配置文件启动)
redis-server /var/data/redis/redis.conf &  //后台启动

//限制redis的绑定ip为指定机器
requirepass passwd  加密码
daemonize  yes 默认后台启动


2 集群安装
配置文件修改：（主要针对从redis的配置文件）
daemonize  yes 默认后台启动
logfile “ /usr/local/redis/log/redis.log”   日志文件路径修改
dir  /usr/local/redis/data/    redis dump.rdb appendonly.aof 文件路径修改
pidfile /usr/local/redis/redis_6379.pid 
bind xx.xxx.xxx.30
slaveof xx.xxx.xxx.31 6379   //主从机器设置
备注:31为主，此时从机器配置文件多出一行为slaveof xx.xxx.xxx.31 6379  

3 redis密码设置:    redis-cli –h xx.xx.xxx.xxx –p 6379 –a passwd
方法一:修改配置文件，重启redis
requirepass passwd
方法二:不重启Redis设置密码：(对应的配置文件也要修改好密码)
config set requirepass passwd  
config get passwd
auth passwd

```

## 7、golang环境安装

```shell
golang环境的安装部署：
uname –a 查看版本号，x86_64(64位)，i686(32位)
下载源码包:https://golang.org/dl/(被墙了，上不了，需要登录蓝灯)         https://studygolang.com/dl(中文社区)
1)tar zxvf  go1.10.1.linux-amd64.tar.gz –C /usr/local
2)修改profile
export GOROOT=/usr/local/go
export GOBIN=$GOROOT/bin
export GOPKG=$GOROOT/pkg/tool/linux_amd64
export GOARCH=amd64
export GOOS=linux
export GOPATH=/app/Golang
export PATH=$PATH:$GOBIN:$GOPKG:$GOPATH/bin
2.	使之生效，执行source /etc/profile

windows下liteIDE的安装：
安装go1.6，配置环境变量，Path(go1.6的安装目录) ；
GOPATH（自己写的代码的路径，需要自己添加；第三方库的可执行文件，包和源代码的根目录）E:\work\go\src
GOROOT（go编译器的安装路径；go标准库的根目录，包含标准库的CLI可执行文件，包，源代码，文档等）C:\Go
Liteide解压缩即可        备注：win64-user liteIDE就可以跳转啦
 cmd窗口执行go version失败时，配置windows中path环境变量即可
```

## 8、elasticsearch安装

**备注：**

es(主节点，客户端节点，数据节点)discovery.zen.ping.unicast.hosts配置说明：

主节点配置： 45,46,47(主节点两两之间相互配置)

客户端节点配置L将主节点45,46,47全部配置上)

数据节点配置：(将主节点45,46,47全部配置上)

\>>>>>>>>>>>>>>>>数据平滑迁移方案（a，b，c主节点）

a :配置b，c和45

b：配置a，c

c：配置a，b

数据节点:配置a，b，c

客户端节点:配置a，b，c

ELK包括ElasticSearch（数据存储、快速查询）、logstash（日志搜集）、kibana（展示ElasticSearch数据的图形界面）。

```shell
ES的安装(修改配置文件和添加用户;文件不能为root用户，均修改为普通用户)
首先安装Java //下载jre-8u111-linux-i586.rpm    www.java.com （下载64位）
rpm －ivh 软件包名  安装软件包
java –version   检查java版本
rpm –qa | grep java 进一步查看JDK信息

安装es
2.	创建普通用户elsearch(elasticsearch所有文件夹不能为root用户;elasticsearch_data也必须是普通用户)。
Tar zxvf elasticsearch-5.0.2.tar.gz
mkdir elasticsearch_data(目录下包含目录data和logs目录)
groupadd elsearch    创建组
useradd elsearch –g elsearch –p elasticsearch  （-g用户名或id，-p密码）
chown –R elsearch:elsearch elasticsearch-2.4.1（将该文件夹下的文件拥有者变更）
备注（日志文件存放也需要修改为普通用户）：chown –R elsearch:elsearch elasticsearch_data
3.	修改config中的.yml文件(elasticsearch.yml)
cluster.name: platform-pro#名字相同可加入同一集群
node.name: xx.xx7.xx2.112
path.data: /apps/elasticsearch_data/data
path.logs: /apps/elasticsearch_data/logs
network.host: xx.xx7.xx2.112
discovery.zen.ping.unicast.hosts: [“xx.xx7.xx2.112”,”xx.xx7.xx2.113”,”xx.xx7.xx2.114”,”xx.xx7.xx2.115”]
注意事项：	path.logs: /app/elasticsearch_data/logs#es日志的存放路径
			cluster.name: xxp-pro2#名字相同可加入同一集群
			/app/elasticsearch_data/logs  查看es日志(xxp-pro2.log)
network.host: 127.0.0.1
transport.host: xx.16.75.30

4.	启动es(必须普通用户启动才可以;报错有可能已经启动过了，kill掉该进程再重新启动)
su elsearch     cd elasticsearch/bin
./elasticsearch（启动elastic的方法）   ./elasticsearch –d(后台启动)
5.	界面上访问es  http://xx.xx7.xx2.112:9200/_plugin/head/
插件安装
6.	安装kibana（配置文件默认为localhost，30机器上默认有/app/pkg）
tar xf kibana-4.6.1-linux-x86_64.tar.gz
/app/elasticsearch-2.4.1/plugins
chown elasticsearch.elasticsearch kibana-4.6.0-linux-x86_64/bin/  #改变下属主属组。防止权限不够无法运行kibana。
Cd kibana-4.6.1-linux-x86_64/bin/
./kibana & #运行kibana程序即可。
访问kibana： http://xx.xx.xx.30:5601
nohup ./kibana >/dev/null

7.	安装sense
cd kibana-4.6.0-linux-x86_64/bin/
./kibana plugin –install elastic/sense           #安装sense
8.	安装marvel监控：
安装步骤
bin/plugin install license
bin/plugin install marvel-agent
 
安装marvel插件
./kibana plugin –install elasticsearch/marvel/latest
备注：安装完sense记得重启kibana（启动  ./kibana &）

4）analysis-lc-pinyin分词器的安装：
https://www.cnblogs.com/churao/p/5884442.html
解压elasticsearch-analysis-lc-pinyin-2.4.1.zip至plugins插件目录，重启es即可
5）   mysql与es数据实时同步，jdbc插件安装
https://blog.csdn.net/qq_28169023/article/details/74908464
6）Ik分词器安装
copy and unzip target/releases/elasticsearch-analysis-ik-{version}.zip to your-es-root/plugins/ik，重启es即可。
Es集群unsigned解决办法
curl ‘xx.xx.xxx.30:9200/_nodes/process?pretty’   //查看集群节点
curl –XGET http://xx.xx.xxx.30:9200/_cat/shards|grep UNASSIGNED   //查看UNASSIGNED分片数目

curl –XPOST ‘localhost:9200/_cluster/reroute’ –d ‘{
“commands” : [ {
        “allocate” : {
            “index” : “graylog_83”,
            “shard” : 1,
            “node” : “ii7B1dHJR5iFRRdgKhdlhg “,
            “allow_primary” : true
        }
}]
}’


index就是索引的名称：也就是graylog_88,graylog_86,graylog_87…..
node:就是在哪个节点上执行；
shared：分片的编号！


Curl –XPOST ‘xx.xx.xxx.61:9200/_cluster/reroute’ –d ‘{
“commands” : [ {
        “allocate” : {
            “index” : “ index_test “,
            “shard” : 0,
            “node” : “ii7B1dHJR5iFRRdgKhdlhg”,
            “allow_primary” : true
        }
}]
}’

es安全篇
http://tool.oschina.net/htpasswd
修改ngnix配置文件：
   server {
        listen       9201;
        location ~* / {
        auth_basic “login”;#提示信息
        auth_basic_user_file /usr/local/nginx/conf/pwd.conf;
        autoindex on;
        proxy_pass  http://127.0.0.1:9200;
     client_max_body_size   200m;
}
}
/usr/local/nginx/conf/pwd.conf目录下添加密码文件：
pwd.conf               admin:xxxlllll
用户名:admin
密码:xxxxmd
启动nginx 
/usr/local/nginx/sbin/nginx –s reload –c /usr/local/nginx/conf/nginx.conf


# 配置扩展自定义词库和停用词词库（备注：不是plugins下面的ik，是config目录下的analysis-ik）
配置该目录下 /data/elasticsearch-5.6.8/config/analysis-ik/IKAnalyzer.cfg.xml

```

## 9、tomacat安装

```shell
1)tar -zxvf apache-tomcat-7.0.73.tar.gz
2)mv apache-tomcat-7.0.73 tomcat-7.0.73
3)cd /data/tomcat-7.0.73/bin 切换到bin目录
4)vi catalina.sh

JAVA_OPTS="-Xms512m -Xmx1024m -Xss1024K -XX:PermSize=512m -XX:MaxPermSize=1024m"
export TOMCAT_HOME=/data/tomcat-7.0.73
export CATALINA_HOME=/data/tomcat-7.0.73
export JRE_HOME=/usr/java/jdk1.8.0_65/jre
export JAVA_HOME=/usr/java/jdk1.8.0_65

5)cd /data/tomcat-7.0.73/conf
vi server.xml 
<Connector port="8081" protocol="HTTP/1.1"
 72                connectionTimeout="20000"
 73                redirectPort="8443" />


 
Tomcat默认端口备注： 
　　8005：表示用于停止Tomcat的默认端口 
　　8080：表示HTTP连接的默认端口 
　　8009：表示Apache的侦听默认端口 
　　8443：表示SSL的连接默认端口

6)启动tomcat
bin/startup.sh

访问tomcat  http://10.0.0.1:8081/

/data/tomcat-7.0.73/webapps/ROOT

http://10.17.155.196:8081/hotupdate.dic  //词库地址

7)创建词库
cd /data/tomcat-7.0.73/webapps/ROOT

# maven安装方法
tar vxf apache-maven-3.3.9-bin.tar.gz
vi /etc/profile 
export MAVEN_HOME=/data/apache-maven-3.5.0
export PATH=$MAVEN_HOME/bin
mvn -version

tomcat管理员后台设置密码
```

## 10、公文附件解析安装（python）

```shell
1)xlrd-1.0.0.tar.gz(处理xls，xlsx)       beautifulsoup4-4.0.1      tornado-1.2.1.tar.gz
python setup.py build
sudo python setup.py install
tar zxvf test.tgz -C 指定目录
2)antiword-0.37.tar.gz(处理doc)
tar -xvf antiword-0.37.tar.gz
make -j 8
3)python-docx-0.8.6(处理docx)
tar xvzf python-docx-0.8.6.tar.gz
cd python-docx-{version}
python setup.py install
4)python常用模块的安装
yum install gcc
pip install python-docx
easy_install python-docx
 
4)pdf安装
  xpdfbin-linux-3.04
  xpdf-chinese-simplified
 
tar zxvf xpdfbin-linux-3.04.tar.gz
cp bin64/* /usr/local/bin/ 
mkdir  /usr/local/man
mkdir /usr/local/man/man1
mkdir /usr/local/man/man5
cd doc/
cp *1 /usr/local/man/man1/
cp *5 /usr/local/man/man5/
cp sample-xpdfrc /usr/local/etc/xpdfrc
 
//java安装
java -version查看java版本
java -help查看帮助
whereis java 路径
which java执行路径
yum list java*
yum -y install java-1.7.0-openjdk*

```

## 11、python3相关模块安装

```shell
一）linux下python3安装:   https://www.python.org/downloads/release/python-337/
yum -y install zlib zlib-devel    （N未安装成功）
yum -y install bzip2 bzip2-devel
yum -y install ncurses ncurses-devel
yum -y install readline readline-devel
yum -y install openssl openssl-devel   （N未安装成功）
yum -y install openssl-static
yum -y install xz lzma xz-devel
yum -y install sqlite sqlite-devel
yum -y install gdbm gdbm-devel
yum -y install tk tk-devel


tar -xvzfPython-3.3.7.tgz
cd Python-3.3.7/
./configure --prefix=/usr/python --enable-shared CFLAGS=-fPIC
make
make install

cp python3.3 /usr/bin   (/usr/python/bin目录中含python3.3等二进制程序)
ln -sf python3.3 python3

cp libpython3.3m.so.1.0 /usr/local/lib64/
cp libpython3.3m.so.1.0 /usr/lib/
cp libpython3.3m.so.1.0 /usr/lib64/ 
最终安装成功，python3路径如下。启动python3对应python3版本

二）pip和setuptools工具安装  
丰富的第三方库是python的优势所在，为了更加方便的安装第三方库，使用pip命令，我们需要进行相应的安装。
1）前置安装setuptools https://pypi.org/project/setuptools/19.6/
unzip setuptools-19.6.zip
mv setuptools-19.6 ./Python-3.3.7
python3 setup.py build
python3 setup.py install


2)linux下安装pip    https://pypi.org/project/pip/8.0.2/#files
tar zxvf  pip-8.0.2.tar.gz
mv pip-8.0.2 ./Python-3.3.7
python3 setup.py build
python3 setup.py install
 cp pip3 /usr/bin

pip3 -V 对应的版本

pip install -U tornado=1.0.9 //升级到指定版本
pip help //查看帮助
pip search tornado//查看所有与“tornado”关键字相关的组件
 pip3 install Twisted
 pip3 uninstall 后跟一个或者多个包名将会从虚拟环境中移除这些包 //卸载包
 pip3 show redis
 pip3 list
 pip3 freeze > requirements.txt
 pip3 install -r requirements.txt
 pip install --upgrade Twisted

备注：若升级python版本时，对应的setuptools和pip工具需要重新执行python3 setup.py build，python3 setup.py install进行安装。
linux下pip安装源的配置
pip3 install pymysql3 -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com
~/.pip/pip.conf配置源的默认目录
# 默认源http://mirrors.aliyun.com/pypi/simple/
[global]
index-url = http://mirrors.aliyun.com/pypi/simple/
[install]
trusted-host = mirrors.aliyun.com

补充：windows下pip安装与源配置
window下pip源配置： C:\Users\liuxd9\AppData\Roaming\pip\pip.ini

windows下安装pip
解压；python setup.py install（在E:\work\python\src\python36\pip-8.0.2目录下）；配置环境变量E:\work\python\src\python36\Scripts;
三）mysql及相关模块安装
pip3 install PyMySQL  (PyMySQL模块用于连接mysql数据库)
yum install gcc libffi-devel python-devel openssl-devel -y

pip3 install redis

sudo pip install --upgrade pip
sudo pip install peewee  
```

## 12、etcd安装

```shell
一  etcd单节点安装步骤
1)tar zxvf etcd-v3.2.0-rc.1-linux-amd64.tar.gz
cp etcd /usr/bin/
cp etcdctl /usr/bin/
2)创建vi /usr/lib/systemd/system/etcd.service
[Unit]
Description=Etcd Server
After=network.target
 
[Service]
Type=simple
WorkingDirectory=/var/lib/etcd/
EnvironmentFile=-/etc/etcd/etcd.conf
ExecStart=/usr/bin/etcd
 
[Install]
WantedBy=multi-user.target
 
3)配置vi /etc/etcd/etcd.conf
# [member]
ETCD_NAME=default
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_CLIENT_URLS="http://localhost:2379"
ETCD_ADVERTISE_CLIENT_URLS="http://localhost:2379"
 
4)创建工作目录
mkdir /var/lib/etcd
 
5)启动etcd并测试
systemctl daemon-reload
systemctl start etcd(后台启动etcd,关闭目录不会退出)
systemctl status etcd
etcdctl cluster-health
 
./etcd启动etcd(切换到etcd所在目录，启动etcd)                     数据库操作:CRUD
export ETCDCTL_API=3 //use API version 3(默认为 v2 API)

二     etcd集群(.tar.gz从github下载，各个机器上均按照该方式配置)
etcd0   127.0.0.112
1)tar zxvf etcd-v3.2.0-rc.1-linux-amd64.tar.gz
2)cd /etc/systemd/system/,创建etcd.service
3)etcd.service内容(/apps/etcd-v3.2.0/etcd该路径为etcd可执行文件的路径)
[Unit]
Description=etcd.service
[Service]
Type=notify
TimeoutStartSec=0
Restart=always
ExecStartPre=-/bin/mkdir -p /data/etcd
ExecStart=/apps/etcd-v3.2.0/etcd \
  --data-dir /data/etcd \
  --name etcd0 \
  --advertise-client-urls http://127.0.0.112:2379,http://127.0.0.112:4001 \
  --listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
  --initial-advertise-peer-urls http://127.0.0.112:2380 \
  --listen-peer-urls http://0.0.0.0:2380 \
  --initial-cluster-token etcd-cluster-1 \
  --initial-cluster etcd0=http://127.0.0.112:2380,etcd1=http://127.0.0..113:2380,etcd2=http://127.0.0..114:2380
[Install]
WantedBy=multi-user.target
 
4)启动etcd
systemctl enable /etc/systemd/system/etcd.service                                    # 设置服务自启动(设置开机自启动的)
systemctl restart etcd.service                                                                        #启动etcd
 
systemctl restart etcd   //编译机重新启动etcd
5)测试etcd(将etcdctl复制一份至PATH中的随便一个路径，/usr/local/bin，即可etcdctl member list;否则必须指定路径执行./etcdctl member list)
etcdctl member list
echo $PATH /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin(系统默认查找的路径)
```

## 13、 influxdb安装

```shell
wget https://dl.influxdata.com/influxdb/releases/influxdb-1.2.4.x86_64.rpm
sudo yum localinstall influxdb-1.2.4.x86_64.rpm
服务启动:sudo service influxdb start
 
InfluxDB 是一个开源分布式时序、事件和指标数据库(类似的数据库有Elasticsearch、Graphite等)
8083: Web admin管理服务的端口, http://127.0.0.250:8083/(页面无法访问时:修改/etc/influxdb中的配置文件，将enabled改为true即可，[admin]这个的#号也要去掉)
8086: HTTP API的端口
8088: 集群端口(目前还不是很清楚, 配置在全局的bind-address，默认不配置就是开启的)
针对时序数据，influxdb优于mysql;一般用来储存实时数据，配合一套UI界面来展示信息。
```

## 14、couchbase安装（203机器上安装的）

```shell

一 安装
1)下载 couchbase-server-enterprise-6.0.0-centos7.x86_64.rpm，并rz上传至安装机器
2)安装依赖包
yum provides */libcrypto.so.6
yum install openssl098e-0.9.8e-20.el6.centos.1.x86_64
3)安装couchbase
yum install couchbase-server-enterprise-6.0.0-centos7.x86_64.rpm //即可安装成功
rpm --install couchbase-server-enterprise-6.0.0-centos7.x86_64.rpm

访问couchbase：http://xx.xxx.xxx.xxx:8091/

--关闭命令 sudo /opt/couchbase/etc/couchbase_init.d stop
--启动命令 sudo /opt/couchbase/etc/couchbase_init.d start
 systemctl start couchbase-server
 systemctl stop couchbase-server

Databases Path：/opt/couchbase/var/lib/couchbase/data
Indices Path: /opt/couchbase/var/lib/couchbase/data
```

## 15、docker或k8s安装

```shell
镜像，容器和仓库 （docker与k8s解释 https://zhuanlan.zhihu.com/p/53260098）
一 .安装步骤
安装
1 卸载旧版本sudo yum remove docker docker-common docker-selinux docker-engine
2 安装依赖包sudo yum install -y yum-utils device-mapper-persistent-data lvm2
3 添加yum软件源sudo yum-config-manager --add-repo https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
4 安装dockerCE社区版 sudo yum makecache fast ；sudo yum install docker-ce（备注：如果报冲突，可采用sudo yum erase docker-common-2:1.12.6-32.git88a4867.el7.centos.x86_64卸载旧版本的包）
备注：yum客户端配置文件：/etc/yum.conf：为所有仓库提供公共配置；/etc/yum.repos.d/*.repo：为仓库的指向提供配置
启动docker
sudo systemctl enable docker
sudo systemctl start docker
建立docker组
有权限访问 UnixSocket 的用户只有 root 和 docker 用户组
sudo groupadd docker //建立docker组
sudo usermod -aG docker $USER //将当前用户加入到docker组 sudo usermod -aG docker lxd
systemctl restart docker //重启docker服务才生效
su root 切换到root用户；su ${USER} 再切换到原来的应用用户以上配置才生效 //切换或者退出当前账户再从新登入
备注： grep 'docker' /etc/group 查看docker组的用户；/etc/group ，group_name:passwd:GID:user_list；
usermod -aG myGroup myUser ##多个组之间用空格隔开，把myUser用户加入myGroup组
sysctl命令的作用在运行时配置内核参数，-p 载入sysctl配置文件
测试安装的正确性
docker run hello-world

二 镜像相关指令（镜像的唯一标识为ID和摘要）
docker pull ubuntu:16.04 //不指定默认从Docker Hub 获取镜像
docker image ls；docker images；docker system df；docker image ls -a列出所有；docker image ls ubuntu列出部分；docker image ls --digests
docker run -it --rm ubuntu:16.04 bash //运行，-i交互式操作，-t终端，bash执行命令返回结果，--rm容器退出后将其删除
docker run --name web2 -d -p 81:80 nginx:v2
docker image rm 657删除镜像（镜像id前3位以上或者镜像名称或者镜像摘要）
docker ps -a 查看所有容器记录（包括未运行的容器）
docker history nginx:v2 查看镜像内的历史记录
docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]] //慎用docker commit防止镜像臃肿
docker rm a618c597ee73 //删除容器
docker image rm web:v1，docker rmi nginx:latest //删除本地镜像

三 利用dockerfile定制镜像
FROM指定基础镜像，以一个镜像为基础在其上进行定制；scratch表示空白镜像
RUN 指令是用来执行命令行命令的。格式： RUN <命令> 
docker build -t nginx:v3 . //点.代表当前路径,在Dockerfile 文件所在目录执行。采用dockerfile构建
docker build https://github.com/twang2218/gitlab-ce-zh.git //git repo构建
docker build http://server/context.tar.gz //tar压缩包构建
docker build - < Dockerfile //从标准输入中读取 Dockerfile 进行构建

四 容器
docker stop 71dfb102a92b 停止该容器
docker container start 启动已终止的容器
docker run -d ubuntu:14.04 /bin/sh -c "while true; do echo hello world; sleep 1; done" 后台运行
docker container logs [container ID or NAMES]
docker container ls
docker export 5c23f215712d >ubuntu.tar 导出容器
cat ubuntu.tar |docker import - test/ubuntu:v1.0 从容器快照文件中再导入为镜像
docker container rm 9df1447f2602 删除容器

五 仓库
Docker Hub公共仓库
docker login登入
docker out登出
docker pull centos拉取至本地
docker tag ubuntu:17.10 username/ubuntu:17.10 ；docker push 666888999l/ubuntu:14.04 登录成功推送至docker hub仓库

```

