[Elasticsearch 版本选择](https://www.cnblogs.com/jtlgb/p/12221031.html)

[死磕 Elasticsearch 方法论](https://blog.csdn.net/wojiushiwo987/article/details/79293493 )

[elasticsearch社区](http://elasticsearch.cn/)

2.x版本数据可以直接迁移到 5.x； 5.X版本的数据可以直接迁移到6.x； 但是2.x版本数据无法直接迁移到6.x

5.6.x 版本字符串将默认被同时映射成text和keyword类型

注：非root方式启动   国外包慢的解决方案  https://motrix.app/zh-CN/  

### 1   [elasticseach安装](https://www.elastic.co/cn/downloads/elasticsearch)     

[Elasticsearch 7.x 最详细安装及配置](https://www.cnblogs.com/Alandre/p/11386178.html)

[ElasticSearch 7.8.1集群搭建](https://www.cnblogs.com/chenyanbin/p/13493920.html)

备注：目前安装的版本为 7.8.0版本

```shell
groupadd elsearch    创建组
useradd elsearch –g elsearch –p elasticsearch  （-g用户名或id，-p密码）
chown -R elsearch:elsearch elasticsearch-7.8.0（将该文件夹下的文件拥有者变更）

安装es参考链接：https://zhuanlan.zhihu.com/p/79064468
1 解压 tar zxvf elasticsearch-7.8.0-linux-x86_64.tar.gz
2 修改配置文件
 cluster.name: byjes-7.8
 node.name: 127.0.0.1
 path.data: /data/es_7.8/elasticsearch_data/esdata
 path.logs: /data/es_7.8/elasticsearch_data/logs
 network.host: 127.0.0.1
 transport.host: 127.0.0.1
 cluster.initial_master_nodes: ["127.0.0.1"]
3 启动 bin/elasticsearch (以非root方式启动， -d后台)
4 检测是否安装成功 curl http://127.0.0.1:9200  或  http://127.0.0.1:9200/

# 启动报错解决:(非root方式启动; 自带jdk启动)
vi bin/elasticsearch 修改elastic的启动脚本文件，将其改为自带的jdk版本
 # elastic中jdk路径
 export JAVA_HOME=/data/es_7.8/elasticsearch-7.8.0/jdk/
 export PATH=$JAVA_HOME/bin:$PATH
 # 添加jdk判断
 if [ -x "$JAVA_HOME/bin/java" ]; then
    JAVA="/data/es_7.8/elasticsearch-7.8.0/jdk/bin/java"
else
    JAVA=`which java`
fi


备注： 
groupadd elsearch    创建组
useradd elsearch –g elsearch –p elasticsearch  （-g用户名或id，-p密码）  useradd -m -g elsearch elsearch
chown -R elsearch:elsearch elasticsearch-7.8.0（将该文件夹下的文件拥有者变更）

root用户下: 
vi /etc/sysctl.conf 
vm.max_map_count=655360
sysctl -p

从节点无法加入集群？
出错的原因为，复制时也把data目录下的数据复制了一份。删除复制过来的data目录下的数据，再次启动时启动成功。

# es7.x之后新增的配置，写入候选主节点的设备地址，在开启服务后可以被选为主节点
discovery.seed_hosts: ["127.0.0.1:9300", "127.0.0.1:9400", "127.0.0.1:9500"]

# es7.x之后新增的配置，初始化一个新的集群时需要此配置来选举master
cluster.initial_master_nodes: ["node-1", "node-2", "node-3"]

# 安装成功测试指令
curl -u 'elastic:es123456'  127.0.0.1:9200  # elastic 加密后验证
curl http://127.0.0.1:9200  
```

### 2  [kibana 插件安装详解](https://www.elastic.co/cn/downloads/kibana)

```shell
1 解压 tar zxvf kibana-7.8.0-linux-x86_64.tar.gz
2 修改配置文件  elasticsearch.hosts  server.host: "127.0.0.1"
3 启动 bin/kibana
4 访问 http://localhost:5601
# 错误处理(然后将30000毫秒（也就是30s）更改成 90000(90秒)，这个根据实际情况进行修改)
```

### 3  [ik分词器插件](https://github.com/medcl/elasticsearch-analysis-ik/releases)

```shell
1 下载 elasticsearch-analysis-ik-7.8.0.zip
2 目录创建 cd your-es-root/plugins/ && mkdir ik
3 解压 unzip plugin to folder your-es-root/plugins/ik
4 重启es
5 验证分词器的有效性 
PUT index
PUT index/_mapping 
{
        "properties": {
            "content": {
                "type": "text",
                "analyzer": "ik_max_word",
                "search_analyzer": "ik_smart"
            }
        }

}
PUT /index/_create/1 
{"content":"美国留给伊拉克的是个烂摊子吗"}
PUT /index/_create/2
{"content":"公安部：各地校车将享最高路权"}
PUT /index/_create/3
{"content":"中韩渔警冲突调查：韩警平均每天扣1艘中国渔船"}

POST /index/_search  
{
    "query" : { "match" : { "content" : "中国" }},
    "highlight" : {
        "pre_tags" : ["<tag1>", "<tag2>"],
        "post_tags" : ["</tag1>", "</tag2>"],
        "fields" : {
            "content" : {}
        }
    }
}

POST /_analyze?pretty=true
{
	"analyzer":"ik_smart",
	"text": "美国留给伊拉克的是个烂摊子吗"
}

POST /_analyze?pretty=true
{
	"analyzer":"standard",
	"text": "美国留给伊拉克的是个烂摊子吗"
}
备注：插件目录  /data/es_7.8/elasticsearch-7.8.0/plugins
```



### 4 [拼音分词器插件](https://github.com/medcl/elasticsearch-analysis-pinyin/releases/tag/v7.8.0)

```shell
# 同ik分词器安装方式方式
1 创建目录mkdir pinyin  ，解压即可，重启es
2 验证pinyin分词器
PUT /medcl/ 
{
    "settings" : {
        "analysis" : {
            "analyzer" : {
                "pinyin_analyzer" : {
                    "tokenizer" : "my_pinyin"
                    }
            },
            "tokenizer" : {
                "my_pinyin" : {
                    "type" : "pinyin",
                    "keep_separate_first_letter" : false,
                    "keep_full_pinyin" : true,
                    "keep_original" : true,
                    "limit_first_letter_length" : 16,
                    "lowercase" : true,
                    "remove_duplicated_term" : true
                }
            }
        }
    }
}

GET /medcl/_analyze
{
  "text": ["刘德华"],
  "analyzer": "pinyin_analyzer"
}

# https://github.com/gitchennan/elasticsearch-analysis-lc-pinyin  (暂时不安装，因为其仅支持5.3版本)
```

### 5   [ 安装analysis-icu分词器插件](https://blog.csdn.net/weixin_42257250/article/details/97757295)

```shell
icu_分词器 和 标准分词器 使用同样的 Unicode 文本分段算法， 只是为了更好的支持亚洲语，添加了泰语、老挝语、中文、日文、和韩文基于词典的词汇识别方法，并且可以使用自定义规则将缅甸语和柬埔寨语文本拆分成音节

bin/elasticsearch-plugin install analysis-icu   # 安装analysis-icu 
sudo bin/elasticsearch-plugin remove analysis-icu # 卸载analysis-icu 

./bin/elasticsearch-plugin list # 查看已安装的插件
```

### 6  elasticsearch-head

```shell
1 解压  unzip elasticsearch-head-master.zip 
2 移动至插件目录  mv elasticsearch-head elasticsearch-7.8.0/plugins/
3 安装 npm install
4 启动 npm run start
5 打开 open http://localhost:9100/

# head插件需借助于npm，linux下npm安装如下
curl --silent --location https://rpm.nodesource.com/setup_10.x | bash -
yum install -y nodejs
npm install -g cnpm --registry=https://registry.npm.taobao.org
npm install
npm run build
npm -v
```

### 7 新版本特性（对比es2.4版本，es7.8版本新特性）

[免费安全功能全景认知]([https://blog.csdn.net/wojiushiwo987/article/details/90554761?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522159548849219724811807244%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=159548849219724811807244&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v3~rank_business_v1-2-90554761.ecpm_v3_rank_business_v1&utm_term=elastic%E5%AE%89%E5%85%A8%E7%AF%87&spm=1018.2118.3001.4187](https://blog.csdn.net/wojiushiwo987/article/details/90554761?ops_request_misc=%7B%22request%5Fid%22%3A%22159548849219724811807244%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=159548849219724811807244&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v3~rank_business_v1-2-90554761.ecpm_v3_rank_business_v1&utm_term=elastic安全篇&spm=1018.2118.3001.4187))

```shell
# kibana 探索
https://www.elastic.co/guide/en/kibana/7.8/introduction.html    kibana面板学习 (暂时pass)

# Kibana多用户创建及角色权限控制 
Stack Management权限管理和设置  (针对不同用户只能查看各自系统的索引文件：http://www.mamicode.com/info-detail-2764620.html )
创建系统app1index-log和app2index-log角色，选择对应的索引文件，分配对应的权限read (Security - Roles 创建角色)
创建两个用户 app1index/app1index（用户名/密码），app2index/app2index, 然后分配对应系统角色和kibana_user角色 (Security - Users)

# 监控功能
????

# es gosdk 版本管理工具的不同  https://github.com/olivere/elastic

# xpack安全探索 （7 .1版本：基础安全免费。Elastic Stack安全功能免费提供）
https://www.elastic.co/guide/en/elasticsearch/reference/7.8/security-api.html
https://www.elastic.co/guide/en/elasticsearch/reference/7.8/index.html#
1) 安全相关
GET /_security/_authenticate  # 查看权限
POST /_security/realm/default_file/_clear_cache?usernames=rdeniro,alpacino  # 指定用户名退出选定用户
GET /_security/privilege/_builtin # 检测内置特权
2) 权限应用
PUT /_security/privilege
{
  "myapp": {
    "read": {
      "actions": [ 
        "data:read/*" , 
        "action:login" ],
        "metadata": { 
          "description": "Read access to myapp"
        }
      }
    }
} 


# string格式(text   keyword不分词) ， 移除名为 ik 的analyzer和tokenizer,请分别使用 ik_smart 和 ik_max_word
```







