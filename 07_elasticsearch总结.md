## 1、基本概念

ElasticSearch(以下简称ES)是一个基于Lucene构建的开源(open-source)，分布式(distributed)，RESTful，实时(real-time)的搜索与分析(analytics)引擎。用途：它用于全文索引、结构化数据索引、数据分析以及三者的结合。扩展至数百台的服务器节点上来处理PB级的数据1PB=1024TB。ES建立在Lucene的基础之上，但是Lucene仅仅是一个库，如果要发挥它的优势，你必须使用它然后再结合自己的开发来构造一个具体的应用。ES意在取Lucene的优点，隐蔽其复杂性来构造一个简洁易用的RESTful风格的全文搜索引擎。

```shell
analysis分析：将文本text转化为查询词term的过程（用于进行全文文本(Full Text)的分词，以建立供搜索用的反向索引）。
cluster集群：一个或多个拥有同一集群名称的节点组成了一个集群。
document文档：保存在ES中的json文本，原始的 JSON 文本在索引后将被保存在 _source 字段里。
mapping映射：每个索引都存在映射，它定义了改索引的每一种类型及索引的相关配置；（可显示定义或文档被索引自动创建）
node节点：集群中es运行实例。测试时，多个节点可以同时启在同一个服务器上。生产环境一般是一个服务器上一个节点。
primary shard 主分片：默认5个，需要注意的是，一旦索引创建，就不能修改主分片个数。
replica shard 副本分片：每个主分片拥有0个或多个副本；为主分片的拷贝（故障转移，提高性能，副本对搜索的性能非常重要，同时用户也可在任何时候添加和删除副本，额外的副本能给你带来更大的容量, 更高的呑吐能力及更强的故障恢复能力)
routing路由：默认路由值来自于文档id，该值可以在索引时指定，也可以通过映射路由字段来指定。
source field源字段:默认获取或搜索请求值中的_source字段保存源JSON文本。
term查询词：一个查询词是一个被 es 索引的确切值。查询词 foo，Foo，FOO 是不同的，查询词可以用查询接口进行获取。
Token: 它是单个Term在所属Field中文本的呈现形式，包含了Term内容、Term类型、Term在文本中的起始及偏移位置。
text文本：文本（或称之为全文）是普通的、非结构化的文本，例如本段话。
hits数组包含匹配到的前10条数据。（每个结果都包含_index、_type和_id字段） total字段来表示匹配到的文档总数；
_score字段相关性得分(relevance score)：衡量了文档与查询的匹配程度，返回的结果中关联性最大的文档排在首位。
max_score：所有文档匹配查询中_score的最大值。
took告诉我们整个搜索请求花费的毫秒数。
shards：_shards节点告诉我们参与查询的分片数（total字段），有多少是成功的（successful字段），有多少的是失败的（failed字段）。
timeout：告诉我们查询超时与否。你可以定义timeout参数为10或者10ms（10毫秒），或者1s（1秒）
field字段：它是Document的组成部分，由两部分组成，名称(name)和值(value)。
es删除支持通配符进行删除，action.destructive_requires_name:true来禁用该特性。curl -XDELETE ‘http://localhost:9200/*/’

# elasticsearch 与关系数据库mysql对比
index索引：相当于一个数据库databases
type类型：一种类型类似于关系型数据库中的一张表table。每种类型都有若干字段，可以用于指定给该类型文档。映射定义了该文档中的每个字段如何进行分析。
document：rows相当于一个document。
field：相当于列columns。
id: 用于标识文档的，一个文档的索引/类型/id 必须是唯一的。（id: 用于标识文档的，一个文档的索引/类型/id 必须是唯一的）

# 集群健康状态
green主分片和复制分片都可用。
yellow所有主分片可用，但不是所有复制分片都可用。
red不是所有的主分片均可用。

# TF/IDF(term frequency/inverse document frequecy)算法：TF/IDF检索词频率/反向文档频率
玩转es集群：https://zhuanlan.zhihu.com/p/24302699

# es原理：倒排索引。
一个倒序索引由两部分组成：在文档中出现的所有不同的词；对每个词，它所出现的文档的列表。


```

## 2、常用resful指令（针对es7.8）

```shell
1. 查看索引、节点、健康、映射
curl '127.0.0.1:9200/_stats'         # 获取ES集群的统计信息
#批量导入json
GET /_cat/nodes?v&pretty
GET /_cat/health?v&pretty
GET /_cat/indices?v&pretty
GET /_aliases?pretty
GET /_stats
GET /_nodes/process?pretty# 查看集群的详细信息


2. 分词器 （standard，english等）
curl -XPOST 'http://127.0.0.1:9200/_analyze?pretty' -d'{"text": "中华人民共和国","analyzer": "ik_max_word"}'
curl -XGET 'http://127.0.0.1:9200/_analyze?analyzer=ik&pretty' -d "QM-GC01.0003-2017" 
POST /_analyze?pretty
{
  "text": ["中华人民共和国"],
  "analyzer": "ik_max_word"
}


3. 别名、增删改查(CURD)
curl -XPOST 'http://127.0.0.1:9200/bank/account/_bulk?pretty&refresh' --data-binary "@accounts.json" 
导入样本数据accounts.json（注：在accounts.json所在的文件目录里面执行curl命令）
curl -XPOST 'http://127.0.0.1:9200/bank/account/_bulk?pretty' --data-binary @accounts.json

curl -i -XGET 'http://xx.xxx.xx.91:9200/website/blog/123'?pretty  # 带响应头
GET tvs/_alias
PUT /tvs/_alias/10001_tvs_alias?pretty
DELETE /10006z_*?pretty  # 通配符删除
GET tvs/_search
DELETE tvs/_doc/11111 # es7.8版本默认其类型均为_doc
POST /indexname?pretty
{"settings":{"index":{"number_of_shards":"5","number_of_replicas":"1"}}}  # 设置分片

GET /my-index-000001,my-index-000002/_search  # 多索引查询
GET /my-index*/_search  # 通配符查询
GET /_all/_search
GET /*/_search
GET _cat/indices # 查看所有索引
DELETE my-index-000001 # 删索引
PUT 10002_idxcs # 创索引
PUT /my-index-000001  
{"mappings":{"properties":{"post_date":{"type":"date"},"user":{"type":"text","analyzer":"ik_max_word","search_analyzer":"ik_smart"},"name":{"type":"keyword"},"age":{"type":"integer"}}}}   # 创建索引并设置mapping (interger,double,date,geo_point,)

# <REST Verb> /<Index>/<Type>/<ID>	(备注：增加也可以实现更新功能,pretty为json格式返回;POST更新)
PUT /twitter
POST /twitter/_doc/1?pretty -d'{"name":"John Doe"}'
GET /twitter/_doc/1
DELETE /twitter/_doc/1?pretty


GET /gb,us/_search?pretty
GET /g*,t*/_search?pretty
GET /bank/_search?size=30&pretty
GET /bank/_search?size=5&from=10&pretty
GET /bank/_search?pretty
{ "from": 30, "size": 10 }

GET /bank/account/_mget?pretty
{ "docs" : [ { "_id" : 25 }, { "_type" : "account", "_id" :   1 } ] }

GET /bank/account/_mget?pretty
{ "ids" : [ "2", "1" ] }  

# 批处理
POST /twitter/external/_bulk?pretty
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }

# 更新第一个文档,删除第二个文档
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"delete":{"_id":"2"}}

4. 映射
# 创建映射
PUT /mjb2?pretty
{
  "mappings": {
    "mjb": {
      "properties": {
        "time": {
          "format": "yyyy-MM-dd HH:mm:ss||strict_date_optional_time||epoch_millis",
          "type": "date"
        }
      }
    }
  }
}

# 在现有索引基础上新增映射
PUT /mjb2/_mapping
{
  "properties": {
    "employee-id": {
      "type": "keyword",
      "index": false
    }
  }
}

# 创建索引mapping和setting
PUT /cs_idx_v7.8
{
  "settings": {
    "analysis": {
      "analyzer": {
        "pinyin_analyzer": {
          "tokenizer": "my_pinyin"
        }
      },
      "tokenizer": {
        "my_pinyin": {
          "type": "pinyin",
          "keep_separate_first_letter": false,
          "keep_full_pinyin": true,
          "keep_original": true,
          "limit_first_letter_length": 16,
          "lowercase": true,
          "remove_duplicated_term": true
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "name_iksmart": {
        "type": "text",
        "analyzer": "ik_smart"
      },
      "name_ikmax": {
        "type": "text",
        "analyzer": "ik_max_word"
      },
      "name_standard": {
        "type": "text",
        "analyzer": "standard"
      },
      "name_pinyin": {
        "type": "text",
        "analyzer": "pinyin_analyzer"
      },
      "name_keyword_no": {
        "type": "keyword"
      },
      "name_date": {
        "type": "date"
      },
      "name_geo_point": {
        "type": "geo_point"
      },
      "name_qtlx": {
        "type": "nested",
        "properties": {
          "INFO": {
            "analyzer": "ik_smart",
            "type": "text"
          }
        }
      }
    }
  }
}
```

## 3、结构化查询DSl

```shell
bool过滤可以合并多个过滤条件查询结果的布尔逻辑，它包含一下操作符：must（多个查询完全匹配，相当于and）、should（至少有一个条件匹配相当于or）、must_not（多个条件的相反匹配，相当于not）

# exists  (https://blog.csdn.net/qq_15058425/article/details/105909847):允许你过滤文档，只查找那些在特定字段有值的文档，无论其值是多少  ("sex":""  "sex":"男" 均可以过滤出来)
{ "query": { "bool": { "must": [ { "exists": { "field": "sex" } } ] } } }

# missing  (https://www.elastic.co/guide/en/elasticsearch/reference/2.3/query-dsl-missing-query.html)
{ "constant_score" : { "filter" : { "missing" : { "field" : "user" } } } } 
符合missing过滤：
{ "user": null }
{ "user": [] } 
{ "user": [null] } 
{ "foo":  "bar" } 
不符合missing过滤
{ "user": "jane" }
{ "user": "" } 
{ "user": "-" } 
{ "user": ["jane"] }
{ "user": ["jane", null ] } 


# tvs数据格式： { "price" : 1000, "color" : "红色", "brand" : "长虹", "sold_date" : "2016-10-28" }
GET tvs/_search?q=*&sort=sold_date:asc
GET /tvs/_search
{"query":{"match_all":{}},"sort":[{"sold_date":"asc"}]}

GET /tvs/_search
{"query":{"bool":{"minimum_should_match": "1","must":[{"match":{"color":"红色"}}],"should":[{"term":{"price":{"value":"1000"}}},{"match_phrase":{"brand":"长虹"}},{"exists":{"field":"color"}}],"filter":[{"range":{"price":{"gte":1000,"lte":15000}}}]}},"highlight":{"fields":{"color":{},"brand":{}}},"from":0,"size":10,"_source":["color","price","sold_date"],"sort":[{"sold_date":"asc"}]}

"minimum_should_match": "1" # 至少匹配其中的一个should条件

# 多字段匹配
{
  "query": {
    "multi_match": {
      "query":       "Brown fox",
      "type":        "most_fields",
      "fields":      [ "title", "body"]
    }
  }
}

{
  "query": {
    "multi_match": {
        "query":                "Brown fox",
        "type":                 "best_fields", 
        "fields":               ["title", "body" ],
        "tie_breaker":          0.3,
        "minimum_should_match": "30%" 
    }
    }
}

# match基于文本搜索，term文本和值均可以。term(cn字段设置为非解析not_analyed,用term关键词精确匹配;若换成match则匹配不到)
# match,match_phrase等语法相同
{
    "query": {
        "term": {
            "cn": "171 Putnam Avenue"
        }
    }
}


# nested查询(嵌套对象作为独立文档单独索引，因此不能直接查询，必须用nested或者nested filter来接触他们)
1)防止结构性的JSON文档会平整成索引内的一个简单键值格式
{  
  "title":"Nest eggs",  
  "body":  "Making your money work...",  
  "tags":  [ "cash", "shares" ],  
  "comments":[  
     {  
      "name":    "John Smith",  
      "comment": "Great article",  
      "age":     28
     },  
     {  
      "name":    "Alice White",  
      "comment": "More like this please",  
      "age":     31
     }  
  ]  
}
# 将索引设置为nested类型，每一个nested object 将会作为一个隐藏的单独文本建立索引
{  
  "comments.name":    [ john, smith ],  
  "comments.comment": [ article, great ],  
  "comments.age":     [ 28 ]
}  
{   
  "comments.name":    [ alice, white ],  
  "comments.comment": [ like, more, please, this ],  
  "comments.age":     [ 31 ]
}  
{   
  "title":            [ eggs, nest ],  
  "body":             [ making, money, work, your ],  
  "tags":             [ cash, shares ]  
}

# 查询语句
curl -XGET 'localhost:9200/my_index' -d '  
{  
  "query":{  
     "bool":{  
        "must":[  
           {"match":{"title":"eggs"}},  
           {  
             "nested":{  
                "path":"comments",  
                "query":{  
                   "bool":{  
                      "must":[  
                         {"match":{"comments.name":"john"}},  
                         {"match":{"comments.age":28}}  
                      ]  
                   }  
                }  
             }  
           }  
        ]  
     }  
  }  
}

# 公文附件的嵌套
{
  "query": {
    "nested": {
      "path": "ATT_INFO",
      "query": {
        "match": {
          "ATT_INFO.FD_FILE_NAME": {
            "query": "pdf"
          }
        }
      }
    }
  }
}

{"_source":false,"query":{"match":{"user.id":"kimchy"}}}  # 返回数据不包括_source
{"_source":{"includes":["http.*","@timestamp"],"excludes":["http.request"]},"query":{"term":{"user.id":"kimchy"}}} 
{"from":0,"size":20,"query":{"match":{"user.id":"kimchy"}},"highlight":{"fields":{"user.id":{}}}} #高亮，from从0开始返回20条
{"query":{"term":{"product":"chocolate"}},"sort":[{"price":{"order":"asc","mode":"avg"}}]} # 多值排序，基于价格平均值排序

# es过滤分析
post_filter 的特性是在查询 之后 执行，任何过滤对性能带来的好处（比如缓存）都会完全失去。
Doc values 的存在是因为倒排索引只对某些操作是高效的。
按照terms过滤，不参与评分。文本匹配：设置成not_analyzed。
# 过滤多用filter
{
   "query" : {
      "filtered" : { 
         "filter" : {
            "bool" : {
              "should" : [
                 { "term" : {"price" : 20}}, 
                 { "term" : {"productID" : "XHDK-A-1293-#fJ3"}} 
              ],
              "must_not" : {
                 "term" : {"price" : 30} 
              }
           }
         }
      }
   }
}

# minimum_should_match用法； title 字段包含 "brown" AND "fox" 、 "brown" AND "dog" 或 "fox" AND "dog" 。如果有文档包含所有三个条件，它会比只包含两个的文档更相关。
{
  "query": {
    "bool": {
      "should": [
        { "match": { "title": "brown" }},
        { "match": { "title": "fox"   }},
        { "match": { "title": "dog"   }}
      ],
      "minimum_should_match": 2 
    }
  }
}
# dis_max查询，不支持bool。
tie_breaker 可以是 0 到 1 之间的浮点数，其中 0 代表使用 dis_max 最佳匹配语句的普通逻辑， 1 表示所有匹配语句同等重要。
{
    "query": {
        "dis_max": {
            "queries": [
                { "match": { "title": "Brown fox" }},
                { "match": { "body":  "Brown fox" }}
            ]
        }
    }
}

# multi_match(最佳匹配字段:best_field),所有匹配评分综合考虑most_fields 
{
  "query": {
    "multi_match": {
      "query": "Quick brown fox",
      "type": "best_fields", 
      "fields": [
        "title^2"
      ]
    }
  }
}

# 模糊查询，查询字段需设置成not_analyzed(前缀查询，当字段集合较小时，伸缩性不好，且会给集群带来较大的压力。另一个索引时的解决方案，这个方案能使前缀匹配更高效，不过在此之前，需要先看看两个相关的查询： wildcard 和 regexp （模糊和正则）


# 常用查询语句
{
  "query": {
    "range": {
      "DOC_CREATE_TIME": {
        "gte": "2017-03-30 11:39:27"
      }
    }
  },
  "sort": {
    "DOC_CREATE_TIME": {
      "order": "desc"
    }
  }
}

# 模糊匹配
{
  "query": {
    "wildcard": {
      "filename": "*jpg"
    }
  }
}

# 多字段匹配
{
  "query": {
    "multi_match": {
      "query": "2761664028527",
      "type": "most_fields",
      "fields": "_all"
    }
  }
}

# match查询
{
  "query": {
    "match": {
      "uid": "Testuser008"
    }
  }
}
# 依据id精确查询
{
  "query": {
    "term": {
      "FD_ID": "2593056731"
    }
  }
}

# nested查询
{
  "query": {
    "nested": {
      "path": "ATT_INFO",
      "query": {
        "match": {
          "ATT_INFO.FD_FILE_NAME": {
            "query": "pdf"
          }
        }
      }
    }
  }
}
```

## 4、聚合查询aggs和sql相关

```shell

# 指标聚合: min、max、sum、avg、stats、value_count, boxplot箱线, cardinality基数, stats 统计数据, extended_stats扩展统计数据,geo_bounds地理范围, geo_centroid地理重心, top_hits,top_metrics。stats ( 返回 max  min  avg  sum )，extended_stats ( 返回 max  min  avg  sum 方差)，value_count (按字段统计文档数量)。
{
  "size": 0, 
  "aggs": {
    "stats_pirce": {
      "stats":  {
        "field": "price"
      }
    }
  }
}


# 桶聚合: 分桶相当与 SQL 中 SQL 中的 group by。terms  各个价格区间的数量，range  某个范围的数目。
{
  "size": 0, 
  "aggs": {
    "terms_count": {
      "terms":  {
        "field": "price"
      }
    }
  }
}

{"size":0,"aggs":{"price_range":{"range":{"field":"price","ranges":[{"to":10000},{"from":12000,"to":20000},{"from":30000}]}}}}

{
  "aggs": {
    "avg_boxplot": { "boxplot": { "field": "page_count" } }
  }
}

{"query":{"constant_score":{"filter":{"match":{"type":"hat"}}}},"aggs":{"hat_prices":{"sum":{"field":"price"}}}} # 先查询再聚合
# 管道聚合 ：Pipeline Aggregations 处理的对象是其他聚合的输出

# 矩阵聚合：matrix_stats
{"aggs":{"statistics":{"matrix_stats":{"fields":["price"]}}}}


# es 引入sql   (rest参数参考:https://www.elastic.co/guide/en/elasticsearch/reference/7.8/sql-rest-fields.html)
./bin/elasticsearch-sql-cli https://sql_user:strongpassword@some.server:9200  # 连接某个elastic集群
column field
row document
table index
database cluster

# 默认json返回，支持格式: csv,json,tsv,txt,yaml,cbor,smile
GET /_sql
{"query":"SELECT * FROM tvs limit 2"}
GET /_sql?format=json
{"query":"SELECT * FROM tvs","fetch_size":5}

GET /_sql
{"cursor":"xxxxxx"}  # continue to the next page by sending back the cursor field
POST /_sql?format=json
{"query":"SELECT * FROM tvs ORDER BY sold_date DESC","filter":{"range":{"price":{"gte":1000,"lte":2000}}},"columnar": true}  # query与filter结合
POST /_sql?format=json
{"query":"select *from tvs where price=?","params":[1000]}

# sql翻译成dsl
GET /_sql/translate?format=json
{"query":"SELECT * FROM tvs","fetch_size":5}
```

## 5 、ES优化

```shell
go-mysql-elasticsearch开源工具同步数据到ES;
1 加大内存（重启es即可）
设置ES_HEAP_SIZE环境变量的大小，jvm你有足够的内存，也尽量不要超过32G，因为它浪费了内存，降低了CPU的性能，还要让GC应对大内存。 /home/elsearch/.bashrc 修改/home（针对具体用户设置的环境变量，仅对elsearch用户有效）
export ES_HEAP_SIZE=8g

2  禁用swapping：vi /etc/fstab
禁止内存交换 bootstrap.mlockall: true

3 es常见错误及性能优化建议
https://www.cnblogs.com/jiu0821/p/6075833.html
https://www.cnblogs.com/cutd/p/5800795.html
1）es_rejected_execution_exception <429>此异常时因为请求数过多，es的线程池不够用。
thread_pool:
    index:
        type: fixed
        size: 30
        queue_size: 2000
    search:
        type: fixed
        size: 30
        queue_size: 6000
        min_queue_size: 10
        max_queue_size: 8000
        auto_queue_frame_size: 10000
        target_response_time: 1.5s
    bulk:
        type: fixed
        size: 60
        queue_size: 2000
    warmer:
        core: 1
        max: 8
        keep_alive: 2m

2）Result window is too large, from + size must be less than or equal to: [10000] but was [10000000].
curl -XPUT http://127.0.0.1:9200/indexname/_settings -d '{ "index" : { "max_result_window" : 100000000}}'
3）分词器设置及验证
curl -XGET 'http://localhost:9200/_analyze?analyzer=ik&pretty' -d "耶稣登山宝训"
4）批量插入 超时问题
60s
5）es查询中参数上线的限制
multi_match ：There is a limit of no more than 1024 fields being queried at once.
boost：Sets the boost value of the query. Defaults to 1.0.
minimum_should_match：一个值控制在结果布尔查询中应该匹配多少“应该”子句。它可以是一个绝对值，一个百分比(30%)或者两者的组合。
terms查询：直接或通过查找的术语查询中使用的最大词汇量被限制为65536。这个默认的最大值可以用索引设置index.max_terms_count来更改一个特定的索引。
6）映射
设置允许您限制手动或动态创建的字段映射的数量，以防止糟糕的文档导致映射爆炸:
index.mapping.total_fields.limit   索引中字段的最大数量。默认值是1000。
index.mapping.nested_fields.limit 索引中嵌套字段的最大数量，默认为50。索引一个包含100个嵌套字段的文档实际上索引了101个文档。
index type id：映射type类型没有意义，建议移除  #  es新版本type移除了

7）es配置文件设置和优化链接
https://www.elastic.co/guide/en/elasticsearch/reference/2.4/cluster-update-settings.html

# 打开最大文件数
ulimit -n 查看打开最大文件数
vi /etc/security/limits.conf 添加下面两行 （重启es即可生效）

* soft nofile 102400
* hard nofile 102400

# 线程池修改（修改配置文件不起作用，需要手动发请求去修改）
https://blog.csdn.net/jianjun200607/article/details/50854209  
一种是persistent，持久的修改；另外一种是transient，就是暂时的修改。
curl -XPUT 'localhost:9200/_cluster/settings' -d '{
    "transient": {
        "threadpool.bulk.type": "fixed",
        "threadpool.bulk.size": 32,
        "threadpool.bulk.queue_size": 6000
    }
}'
 "bootstrap.memory_lock": true

curl -XGET 'localhost:9200/_cat/nodes?v&h=id,host,name,sm'
curl -XGET 'localhost:9200/_nodes?filter_path=**.mlockall'  # 查看内存是否锁住
curl -XGET 'http://localhost:9200/_nodes/stats?pretty'  
curl -XGET "http://localhost:9200/_nodes/thread_pool/?pretty" # (size cpu的倍数，线程池的数目)，修改配置文件即可生效
curl localhost:9200/_cache/clear?pretty


# 调优
调优建议：
	50%的内存给elasticsearch，给ES的内存配置不是越大越好，建议不能超过32GB
	bootstrap.mlockall: true  锁住内存
	将ES的数据目录放到内存文件系统
	操作系统环境调节:ulimit -n 65536
	磁盘是一个服务器最慢的系统，对于写比较重的集群，磁盘很容易成为集群的瓶颈。最好使用SSD盘(64G内存 SSD硬盘 RAID0，不要使用NAS)
	CPU（threadpool）:搜索：int((# of cores * 3) / 2) + 1
	索引时，将副本数设置为0。后续再设置回来。“number_of_replicas”: 0


使用bulk请求；（达到一定的数目或超过一定的时间，进行入库）
使用 multiple workers/threads发送数据到ES （达到一定的数目或超过一定的时间，进行入库）
增加刷新间隔（默认是1s，可以改成30s以减少合并压力）
尽可能使用过滤器上下文（Filter）替代查询上下文（Query）；
增加副本数。Elasticsearch可以在主分片或副本分片上执行搜索。副本越多，搜索可用的节点就越多；一个分片不超过20G。
禁用swapping；
给文件缓存分配内存：缓存是用来缓存I/O操作的，至少用一般的内存来运行ES文件缓存。
使用更快的硬件（使用SSD作为存储设备；使用性能更好的CPU，高并发；使用本地存储，避免使用NFS或者SMB）
可视化：esheader插件

es存储模型和读写操作：http://www.infoq.com/cn/articles/analysis-of-elasticsearch-cluster-part01
es与索引关系：index=database；type=tables；Properties= Schema
ES脑裂：奇数个（一国不容二主）
事务日志：https://www.cnblogs.com/forestwang/p/6731664.html
tanslog记录了所有对索引分片的事务操作（add/update/delete），每个分片对应一个translog文件。
Lucene的段：
为什么搜索使用深层分页很危险：start，size，当start起点很大时，那么这就是一个深度分页查询。es默认max_result_window为10000，并且限制了start+size必须小于1w。想取10000以后的数据，可以采用scroll或scan查询。
计算搜索相关性和权衡：tf-idf词频/逆文档频率计算搜索相关性。tf(某个词在文章中的出现次数/文章的总词数)，idf（它的大小和一个词的常见程度成反比，idf=log(语料库的文档总数/包含该词的文档数+1)，分母越大，你文档频率就越小），TF-IDF=词频（TF）X逆文档频率IDF。
并发性：
如何确保读和写的一致性:
高可用：数据节点挂掉后的自动迁移；主节点挂掉后的自动重选；
悲观并发控制：读一行的数据前锁定这一行，然后确保只有加锁的数据才可以修改
乐观并发控制：偶尔发生，重新尝试或刷新数据。

# other tips
_refresh就可以立即实现内存->文件系统缓存,， 从而使文档可以立即被搜索到。
id设置：默认自动生成HASH串的随机ID;若业务上需要且可以保证索引数据的唯一性，也可以使用业务ID作为索引ID;
index的别名设置;
index映射：已存在的索引是不可以更改它的映射的，存在的索引只有新字段出现时，es会为新字段自适应类型。确实需要修改映射，reindex数据重新导入新建mapping的索引。
以字符串为例(索引有3个选项:analyzed,not_analyzed精确索引,no不索引该字段)：
"myfieldname" : {  
    "type" : "string",  
    "index" : "not_analyzed"  
}

"title" : {  
    "type" : "string",  
    "fields" : {  
        "raw" : {  
            "type" : "string",  
            "index" : "not_analyzed"          
        }  
    }  
} 

分词器：
english对英文更加智能，可以识别单数负数，大小写，过滤stopwords(例如“the”这个词)等
ik(智能分词)		ik_smart(粗粒度拆分)		ik_max_word(细粒度拆分)
pinyin(拼音分词器)
standard一个一个词（汉字）切分
chinese效果差
curl -XPOST 'http://127.0.0.1:9200/_analyze?analyzer=english&pretty' -d'{"text": "elasticsearch is a search tech"}'

采用ik分词器实现同义词搜索：参考网址:http://blog.csdn.net/yusewuhen/article/details/50685685
```

## 6、ES评分机制

```shell
相关度评分(es或Lucene的评分机制):index_options 设置为 docs 可以禁用词频统计及词频位置
1)布尔模型：and or not
2)TF/IDF:tf(t in d) = √frequency;idf(t) = 1 + log ( numDocs / (docFreq + 1))
3)constant_score terms过滤等可以使用，过滤字段不参与评分
function_score 
script_score脚本自定义评分
GET /cars/transactions/_search
{
  "query": {
    "match": {
      "make": "ford"
    }
  },
  "sort": [
    {
      "_score": {
        "order": "desc"
      }
    },
    {
      "_script": {
        "reverse": true,
        "script": {
          "inline": "doc['sold'].value * factor",
          "params": {
            "factor": 2.5
          }
        },
        "type": "number",
        "order": "asc"
      }
    }
  ]
}

```



## 7 、ES集群配置(节点部署建议)

```shell
https://www.cnblogs.com/chenyanbin/p/13493920.html
1 配置文件如何配置：
主节点(184,185,186): 两两之间相互配置 
数据节点(187~191):配三个主节点184,185,186
客户端节点(192~193):配三个主节点184,185,186

2 分角色：master节点(三台)，data节点(随着数据增加而增加)，client(随着查询压力而增加)节点
Master节点：node.master: true  node.data: false
Data节点：node.master: false  node.data: true
Client 节点：node.master: false  node.data: false

客户端节点(负载均衡节点):该节点只能处理路由请求，处理搜索，分发索引操作等。从本质上来说该客户节点表现为智能负载平衡器(他协调主节点和数据节点，客户端节点加入集群可以得到集群的状态，根据集群的状态可以直接路由请求)

数据节点:主要是存储索引数据的节点，主要对文档进行增删改查索引数据等操作，聚合操作等。数据节点对cpu，内存，io要求较高， 在优化的时候需要监控数据节点的状态，当资源不够的时候，需要在集群中添加新的节点。

主节点:主要职责是和集群操作相关的内容，如创建或删除索引，跟踪哪些节点是群集的一部分，并决定哪些分片分配给相关的节点。 稳定的主节点对集群的健康是非常重要的。默认情况下任何一个集群中的节点都有可能被选为主节点。(不存储任何索引数据。该node服务器将使用自身空闲的资源，来协调各种创建索引请求或者查询请求，讲这些请求合理分发到相关 的node服务器上)。为了防止脑裂，常常设置参数为discovery.zen.minimum_master_nodes=N/2+1，其中N为集群中Master节点的个数。建议集群中Master节点的个数为奇数个，如3个或者5个。

3 节点状态和分片扩展
红：部分主分片不可用
黄：所有主分片可用，但部分副本分片不可用
绿：最健康的状态
默认分片：主分片     5     副本分片   1
动态扩展副本分片数目：
PUT /dobbyindex/_settings
{
   "number_of_replicas" : 3
}

curl -XPUT '127.0.0.1:9200/xxxindex/_settings' -d'{ 
   "number_of_replicas" : 1
}'


4 数据恢复
内网ip；10个节点的集群,当启动集群中5~6个节点的时候再允许进行数据恢复。建议设置为集群节点数量的一半以上。gateway.recover_after_nodes: 5。
```

## 8、ES备份迁移

```shell
https://blog.csdn.net/u014431852/article/details/52905821
一.数据备份
1）安装sshfs，创建共享目录
yum install  sshfs 共享sshfs
/app/data/backupes 共享目录
/app/mnt/backupes chmod -R 777 ./mnt

sshfs root@ip:/app/data/backupes /app/mnt/backupes -o allow_other  挂载共享目录
2）修改es配置文件，重启
path.repo: /app/mnt/backupes  修改es配置文件重启
3）创建仓库
curl -XPUT 'localhost:port/_snapshot/my_backup' -d' 
{
    "type": "fs", 
    "settings": {
        "location": "/app/mnt/backupes",
        "compress": true
    }
}'
4）备份索引数据
curl -XPUT 'localhost:port/_snapshot/my_backup/snapshot_name' -d'
{
    "indices": "index1,index2"
}'
5）查看备份状态
curl -XGET 'localhost:port/_snapshot/my_backup/snapshot_name/_status'

二.数据恢复  /app/mnt/backupes/indices
// （将源集群的备份内容（/root/backup里的所有文件），复制到迁移目标的集群仓库目录里）
// 类似批量导入，所以只需要在主节点中恢复仓库中的数据即可？

1）使用RESTful API进行备份的恢复，如果索引已经存在目标的集群，需要先关闭索引，恢复数据后在开启
curl -XPOST 'localhost:port/index1/_close'   关闭索引
curl -XPOST 'localhost:port/index2/_close'
curl -XPOST 'localhost:port/_snapshot/my_backup/snapshot_name/_restore'   索引恢复
curl -XGET 'localhost:port/_snapshot/my_backup/snapshot_name/_status'  查看恢复的状态

POST /index_name/_open
```

## 9、ES安全篇

```shell
https://www.elastic.co/cn/blog/reinforce-the-security-of-elasticsearch-101
https://elasticsearch.cn/article/129
禁用批量删除: action.destructive_requires_name: true  不支持_all,通配符删除索引
snapshot 做好备份
不要暴露 Elasticsearch 在公网上：
采用nginx做层代理：https://www.sojson.com/blog/213.html（ES安全篇）

# 实践记录：
1.当数据条数过少的时候，分片数目过多的时候，es是按照分片上的数据进行打分的，此时会导致打分不准确的情况。
目前解决方案是：当数据量较少的时候，此时设置索引的主分片为1，副本分片为0，让该索引中的所有数据在这一个分片中进行打分。
2.设置映射时尽量不要采用自适应字段类型的方式
```

## 10、ES常见问题解决方案

```shell
# es集群出现unsigned解决办法
curl '127.0.0.1:9200/_nodes/process?pretty'   # 查看集群节点
curl -XGET http://127.0.0.1:9200/_cat/shards|grep UNASSIGNED   # 查看UNASSIGNED分片数目

curl -XPOST 'localhost:9200/_cluster/reroute' -d '{
    "commands" : [ {
        "allocate" : {
            "index" : "graylog_83",
            "shard" : 1,
            "node" : "ii7B1dHJR5iFRRdgKhdlhg ",
            "allow_primary" : true
        }
    }]
}'

index就是索引的名称：也就是graylog_88,graylog_86,graylog_87.....
node:就是在哪个节点上执行；
shared：分片的编号！
```





























































## 备注es2.4

```shell
# 创建新索引并设置映射(备注:索引存在时无法设置mapping)
curl -XPUT 'http://127.0.0.1:9200/mjb2?pretty' -d'
{
    "mappings": {
        "mjb": {
            "properties": {
                "time": {
                    "format": "yyyy-MM-dd HH:mm:ss||strict_date_optional_time||epoch_millis",
                    "type": "date"
                }
            }
        }
    }
}'

# 在原有映射的基础上修改映射(备注:索引已存在,只针对新增字段;url加单引号可双引号都ok，加是为了避免特殊字符出现)
curl -XPUT 'http://127.0.0.1:9200/mjb2/_mapping/mjb?pretty' -d'
{
    "properties": {
        "name": {
            "type": "string",
            "index": "not_analyzed"
        }
    }
}'

{
    "properties": {
        "brandName": {
            "type": "string",
            "analyzer": "ik_smart"
        }
    }
}
备注：采用ik_smart;不考虑索引为"index": "not_analyzed"


# mapping映射的补充
在某个字段的 mapping 中指定 "index": "not_analyzed"，从而直接把原始文本转为 term，实现精准搜索;
index属性:控制string如何被索引，它有三个可选值：analyzed(First analyze the string, then index it.),not_analyzed(Index this field, so it is searchable),no(Don’t index this field at all. This field will not be searchable)
analyzer:对于 string类型的字段,使用 analyzer 属性来指定在搜索阶段和索引阶段使用哪个分词器. 默认, Elasticsearch 使用 standard analyzer
# 创建索引别名
ES的mapping一旦创建，只能增加字段，而不能修改已经mapping的字段(补救方法：修改mapping，那就是重新建立一个index，然后创建一个新的mapping)
程序访问索引库时，采用同义词访问;reindex完数据，修改同义词即可。
curl -XPOST http://127.0.0.1:9200/_aliases -d'
{
    "actions":[
        {"add":{"index":"test_gw6","alias":"test_gw"}}
    ]
}'

{"add":{"index":"indexpd2","alias":"indexpd"}}
{"add":{"index":"indexpd","alias":"indexpd_search"}}
{ "remove": { "alias": "my_index", "index": "my_index_v1" }}

# 分析器
设置方法
curl -XPUT 'http://127.0.0.1:9200/bank2?pretty' -d'
{
    "settings": {
        "number_of_shards": 1,
        "analysis": {
            "filter": {
                "my_shingle_filter": {
                    "type": "shingle",
                    "min_shingle_size": 2,
                    "max_shingle_size": 2,
                    "output_unigrams": false
                }
            },
            "analyzer": {
                "my_shingle_analyzer": {
                    "type": "custom",
                    "tokenizer": "standard",
                    "filter": [
                        "lowercase",
                        "my_shingle_filter"
                    ]
                }
            }
        }
    },
    "mappings": {
        "account": {
            "properties": {
                "address": {
                    "type": "string",
                    "index": "not_analyzed"
                }
            }
        }
    }
}'

# 分析器analyzer种类
standard 分析器是用于全文字段的默认分析器
lowercase 标记过滤器，将所有标记转换为小写。
stop 标记过滤器，删除所有可能会造成搜索歧义的停用词，如 a，the，and，is
html_strip 字符过滤器 来删除所有的 HTML 标签，并且将 HTML 实体转换成对应的 Unicode 字符，比如将 Á 转成 Á


# suggester搜索查询(term表示其为term suggester搜索;missing"实际上就是缺省值,字典中为空,options就不建议),在用户输入过程中进行补全或纠错。
Term Suggester
Phrase Suggester
Completion Suggester
Context Suggester

curl -XPOST 'http://127.0.0.1:9200/blogs/?pretty' 
1.创建索引并设置映射(index:blogs,type:tech,字段:body)
PUT /blogs/           ("text"改为"string")
{
  "mappings": {
    "tech": {
      "properties": {
        "body": {
          "type": "string"
        }
      }
    }
  }
}

//设置映射，手动设置分片数
curl -XPUT 'http://127.0.0.1:9200/dtest1?pretty' -d'
{
    "settings": {
        "index": {
            "number_of_shards": "2",
            "number_of_replicas": "1"
        }
    },
    "mappings": {
        "poi": {
            "properties": {
                "lang": {
                    "type": "string"
                },
                "title": {
                    "type": "multi_field",
                    "fields": {
                        "i18n": {
                            "type": "string",
                            "index": "analyzed",
                            "analyzer": "english"
                        },
                        "org": {
                            "type": "string",
                            "index": "analyzed",
                            "analyzer": "standard"
                        }
                    }
                }
            }
        }
    }
}'


//设置映射和不同分词器
curl -XPUT 'http://127.0.0.1:9200/dtest?pretty' -d'
{
    "mappings": {
        "poi": {
            "properties": {
                "lang": {"type": "string"},
                "title": {
                    "type": "multi_field",
                    "fields":{
                    "i18n": {"type": "string","index": "analyzed","analyzer": "english"},
                    "org": {"type": "string","index": "analyzed","analyzer": "standard"}
                    }
                }
            }
        }
    }
}'


curl -XPUT 'http://127.0.0.1:9200/dtest/poi/1?pretty' -d'
{"title":"The quick brown fox jumps over the lazy dog."}'

curl -XPOST 'http://127.0.0.1:9200/dtest/_search?pretty' -d'
{
  "query": {
    "multi_match": {
      "query":       "jumps",
      "fields":      [ "title.org^1000", "body"]
    }
  }
}'

2.批量添加
POST _bulk/?refresh=true
{ "index" : { "_index" : "blogs", "_type" : "tech" } }
{ "body": "Lucene is cool"}
{ "index" : { "_index" : "blogs", "_type" : "tech" } }
{ "body": "Elasticsearch builds on top of lucene"}
{ "index" : { "_index" : "blogs", "_type" : "tech" } }
{ "body": "Elasticsearch rocks"}
{ "index" : { "_index" : "blogs", "_type" : "tech" } }
{ "body": "Elastic is the company behind ELK stack"}
{ "index" : { "_index" : "blogs", "_type" : "tech" } }
{ "body": "elk rocks"}
{ "index" : { "_index" : "blogs", "_type" : "tech" } }
{  "body": "elasticsearch is rock solid"}
3.文本分析(看看哪些term会存在于词典里)
curl -XPOST 'http://127.0.0.1:9200/_analyze?pretty' -d'
{
  "text": [
    "Lucene is cool",
    "Elasticsearch builds on top of lucene",
    "Elasticsearch rocks",
    "Elastic is the company behind ELK stack",
    "elk rocks",
    "elasticsearch is rock solid"
  ]
}'

备注:分出来的token都会成为词典里一个term,注意有些token会出现多次，因此在倒排索引里记录的词频会比较高。

4.suggester具体举例
1)term suggester
POST /blogs/_search
{ 
  "suggest": {
    "my-suggestion": {
      "text": "lucne rock",
      "term": {
        "suggest_mode": "missing",
        "field": "body"
      }
    }
  }
}

2)Phrase Suggester会考量多个term之间的关系，比如是否同时出现在索引的原文里，相邻程度，以及词频等等。
POST /blogs/_search
{
  "suggest": {
    "my-suggestion": {
      "text": "lucne and elasticsear rock",
      "phrase": {
        "field": "body",
        "highlight": {
          "pre_tag": "<em>",
          "post_tag": "</em>"
        }
      }
    }
  }
}
3)Completion Suggester(自动补全,索引并非通过倒排来完成，而是将analyze过的数据编码成FST和索引一起存放;类型必须定义为completion)
PUT /blogs_completion/
创建索引并设置映射
curl -XPOST 'http://127.0.0.1:9200/blogs_completion/?pretty' -d'       
{
  "mappings": {
    "tech": {
      "properties": {
        "body": {
          "type": "completion"
        }
      }
    }
  }
}'
curl -XPOST 'http://127.0.0.1:9200/tttblogs/?pretty' -d'
{
    "settings": {
        "index": {
            "number_of_shards": "4",
            "number_of_replicas": "2"
        }
    },
    "mappings": {
        "my_type": {
            "properties": {
                "createUsername": {
                    "type": "string"
                },
                "createTime": {
                    "type": "double"
                },
                "id": {
                    "type": "long"
                },
                "content": {
                    "type": "string"
                }
            }
        }
    }
}'
批量添加数据
POST _bulk/?refresh=true
{ "index" : { "_index" : "blogs_completion", "_type" : "tech" } }
{ "body": "Lucene is cool"}
{ "index" : { "_index" : "blogs_completion", "_type" : "tech" } }
{ "body": "Elasticsearch builds on top of lucene"}
{ "index" : { "_index" : "blogs_completion", "_type" : "tech" } }
{ "body": "Elasticsearch rocks"}
{ "index" : { "_index" : "blogs_completion", "_type" : "tech" } }
{ "body": "Elastic is the company behind ELK stack"}
{ "index" : { "_index" : "blogs_completion", "_type" : "tech" } }
{ "body": "the elk stack rocks"}
{ "index" : { "_index" : "blogs_completion", "_type" : "tech" } }
{ "body": "elasticsearch is rock solid"}

自动补全查询
curl -XPOST 'http://127.0.0.1:9200/blogs_completion/_search?pretty' -d'
{
  "size":0,    //搜索条数不返回
  "suggest": {
    "blog-suggest": {
      "text": "elastic",  
      "completion": {
        "field": "body",
        "size": 2     //控制补全返回条数
      }
    }
  }
}'


curl -XPOST "http://127.0.0.1:9200/blogs_completion/_suggest?pretty" -d'
{
    "blog-suggest": {
    "text": "elastic",
    "completion": {
      "field": "body",  //字段和数据表中的字段要保持一致
      "size": 2//返回结果数量
      }
    }
}'
```







 