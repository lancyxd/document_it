##  1、redis命令

redis指定配置文件启动： redis-server /var/data/redis/redis.conf &

[Redis 命令参考](http://redisdoc.com/index.html)

```shell
redis-cli -h localhost -a "passwd"
redis-cli -h localhost
auth "passwd"
"tcp://usename:passwd@xx.xxx.xxx.xxx:6379/0?timeout=1m0s&maxidle=10"  #格式
cat d1.txt | redis-cli # 将d1.txt文件中的redis命令进行执行
cat d1.txt | redis-cli --pipe  # 减少返回结果的输出，更快的执行命令
cat d1.txt | redis-cli -p 6379 -h localhost --pipe
redis事务:  multi   set vcount 1   incr vcount      exec

# 常用数据类型
1) string: 是redis基础数据类型，Redis无Int、Float、Boolean，所有的基本类型在Redis中都以String体现。redis把String作为整型或浮点型数字来使用，主要体现在INCR、DECR类的命令。进行INCR/DECR命令的value，必须在[-2^63 ~ 2^63 – 1]范围内。
set testkey 11      get testkey
setnx testkey 12  # SETNX(SET if Not exists)
getset testkey 1  # getset 命令用于设置指定 key 的值，并返回 key 的旧值。
mset key1 1 key2 2 key3 3 # 批量set
mget key1 key2 key3  # 批量get
expire testkey 9
del key3
keys *
incr key  # 将key对应的value值自增1，并返回自增后的值
incrby key 2 # 每次增加2, 将key对应的value值自增指定的整型数值，并返回自增后的值
# 设置键的生存时间(键可以存在多久)或过期时间(键什么时候会被删除) 
EXPlRE <key> <ttl> 命令用于将键key 的生存时间设置为ttl 秒。
PEXPIRE <key> <ttl> 命令用于将键key 的生存时间设置为ttl 毫秒。
EXPIREAT <key> < timestamp> 命令用于将键key 的过期时间设置为timestamp所指定的秒数时间戳。
PEXPIREAT <key> < timestamp > 命令用于将键key 的过期时间设置为timestamp所指定的毫秒数时间戳。

2) list列表   插入排序、双向链表（应用：最新消息排行，粉丝列表等都可以用Redis的list结构来实现；消息队列：Lists的PUSH操作，将任务存在Lists中，然后工作线程再用POP操作将任务取出进行执行）
lpush testlist 10
lpop testlist
lrange testlist 0 3
lindex testlist 0
lset testlist 0 111
linsert testlist before 7 7777

LPUSH：向指定List的左侧（即头部）插入1个或多个元素，返回插入后的List长度。时间复杂度O(N)
RPUSH：同LPUSH，向指定List的右侧（即尾部）插入1或多个元素
LPOP：从指定List的左侧（即头部）移除一个元素并返回，时间复杂度O(1)
RPOP：同LPOP，从指定List的右侧（即尾部）移除1个元素并返回
LPUSHX/RPUSHX：与LPUSH/RPUSH类似，区别在于，LPUSHX/RPUSHX操作的key如果不存在，则不会进行任何操作
LLEN：返回指定List的长度，时间复杂度O(1)
LRANGE：返回指定List中指定范围的元素（双端包含，即LRANGE key 0 10会返回11个元素），时间复杂度O(N)。应尽可能控制一次获取的元素数量，一次获取过大范围的List元素会导致延迟，同时对长度不可预知的List，避免使用LRANGE key 0 -1这样的完整遍历操作。
LINDEX：返回指定List指定index上的元素，如果index越界，返回nil。index数值是回环的，即-1代表List最后一个位置，-2代表List倒数第二个位置。时间复杂度O(N)
LSET：将指定List指定index上的元素设置为value，如果index越界则返回错误，时间复杂度O(N)，如果操作的是头/尾部的元素，则时间复杂度为O(1)
LINSERT：向指定List中指定元素之前/之后插入一个新元素，并返回操作后的List长度。如果指定的元素不存在，返回-1。如果指定key不存在，不会进行任何操作，时间复杂度O(N)

3)hash哈希（散列）：(键值对的集合)
hmset user_2 id 2 name 张丹 age 16  sex 0
hmget user_2 id age sex
hgetall user_2
hset user_3 name lily
hget user_3 name
hexists user_3 name
hset stuinfo name lily age 5
hgetall stuinfo  hget stuinfo name

HSET：将key对应的Hash中的field设置为value。如果该Hash不存在，会自动创建一个。时间复杂度O(1)
HGET：返回指定Hash中field字段的值，时间复杂度O(1)
HMSET/HMGET：同HSET和HGET，可以批量操作同一个key下的多个field，时间复杂度O(N)，N为一次操作的field数量
HSETNX：同HSET，但如field已经存在，HSETNX不会进行任何操作，时间复杂度O(1)
HEXISTS：判断指定Hash中field是否存在，存在返回1，不存在返回0，时间复杂度O(1)
HDEL：删除指定Hash中的field（1个或多个），时间复杂度：O(N)，N为操作的field数量
HINCRBY：同INCRBY命令，对指定Hash中的一个field进行INCRBY，时间复杂度O(1)
应谨慎使用的Hash相关命令：
HGETALL：返回指定Hash中所有的field-value对。返回结果为数组，数组中field和value交替出现。时间复杂度O(N)
HKEYS/HVALS：返回指定Hash中所有的field/value，时间复杂度O(N)

4)set集合(Redis Set是无序的，不可重复的String集合)
sadd testset 1
srem testset 1
srandmember testset / srandmember testset 3
spop testset
scard testset
sismember testset 2
smove  testset1  testset 11 //将11元素从testset1集合移动至testset集合
smembers testset
sunion  testset1 testset
sunionstore myset testset1  testset //并集存储到myset中
sinter testset1 testset 
sinterstore myset testset1  testset
sscan testset1  0 match *

SADD：向指定Set中添加1个或多个member，若指定Set不存在，会自动创建。时间复杂度O(N)，N为添加的member个数
SREM：从指定Set中移除1个或多个member，时间复杂度O(N)，N为移除的member个数
SRANDMEMBER：从指定Set中随机返回1个或多个member，时间复杂度O(N)，N为返回的member个数
SPOP：从指定Set中随机移除并返回count个member，时间复杂度O(N)，N为移除的member个数
SCARD：返回指定Set中的member个数，时间复杂度O(1)
SISMEMBER：判断指定的value是否存在于指定Set中，时间复杂度O(1)
SMOVE：将指定member从一个Set移至另一个Set
慎用的Set相关命令(计算量大，应谨慎使用，特别是在参与计算的Set尺寸不可知的情况下，应严格避免使用)
SMEMBERS：返回指定Hash中所有的member，时间复杂度O(N)
SUNION/SUNIONSTORE：计算多个Set的并集并返回/存储至另一个Set中，时间复杂度O(N)，N为参与计算的所有集合的总member数
SINTER/SINTERSTORE：计算多个Set的交集并返回/存储至另一个Set中，时间复杂度O(N)，N为参与计算的所有集合的总member数
SDIFF/SDIFFSTORE：计算1个Set与1或多个Set的差集并返回/存储至另一个Set中，时间复杂度O(N)，N为参与计算的所有集合的总member数

5)Sorted Set有序集合，非常适合用于实现排名。orted Sets来做带权重的队列
 ZADD KEY_NAME SCORE1 VALUE1.. SCOREN VALUEN
 zadd mysortset 2 "world" 3 "bar"
 zrem mysortset 1 "foo"
 zrange 0 -1 mysortset withscores
 zcard mysortset 
 zcount mysortset 1 3
 zincrby mysortset 2 "hello"
 zrank mysortset "hello"
 ZREVRANK mysortset bar

ZADD：向指定Sorted Set中添加1个或多个member，时间复杂度O(Mlog(N))，M为添加的member数量，N为Sorted Set中的member数量
ZREM：从指定Sorted Set中删除1个或多个member，时间复杂度O(Mlog(N))，M为删除的member数量，N为Sorted Set中的member数量
ZCOUNT：返回指定Sorted Set中指定score范围内的member数量，时间复杂度：O(log(N))
ZCARD：返回指定Sorted Set中的member数量，时间复杂度O(1)
ZSCORE：返回指定Sorted Set中指定member的score，时间复杂度O(1)
ZRANK/ZREVRANK：返回指定member在Sorted Set中的排名，ZRANK返回按升序排序的排名，ZREVRANK则返回按降序排序的排名。时间复杂度O(log(N))
ZINCRBY：同INCRBY，对指定Sorted Set中的指定member的score进行自增，时间复杂度O(log(N))
慎用的Sorted Set相关命令：
ZRANGE/ZREVRANGE：返回指定Sorted Set中指定排名范围内的所有member，ZRANGE为按score升序排序，ZREVRANGE为按score降序排序，时间复杂度O(log(N)+M)，M为本次返回的member数
ZRANGEBYSCORE/ZREVRANGEBYSCORE：返回指定Sorted Set中指定score范围内的所有member，返回结果以升序/降序排序，min和max可以指定为-inf和+inf，代表返回所有的member。时间复杂度O(log(N)+M)
ZREMRANGEBYRANK/ZREMRANGEBYSCORE：移除Sorted Set中指定排名范围/指定score范围内的所有member。时间复杂度O(log(N)+M)

6)发布与订阅
eg：订阅subscribe channel1		发布publish channel1  [hello,world]
    退订 unsubscribe channel1
getrange mykey 0 5 获取mykey的前六个字符           getrange mykey 0 -1获取全部字符
exists aaa 判断aaa是否存在
ttl aaa 查看aaa的过期时间
type aaa 查看aaa的类型
del aaa;persist aaa; 删除过期时间方法
randomkey 随机返回一个key
rename aaa aaanew 将aaa改名为aaanew
renamenx 命令用于在新的 key 不存在时修改 key 的名称
flushdb清除当前数据库的所有key
flushall删除所有数据库中的key
randomkey返回任意的key
redis默认使用数据库 0，为了清晰起见，这里再显式指定一次
select 1 使用数据库1
move greet1 0 将greet1移动到数据库0
```



## 2、应用场景

```she
1.缓存
热点数据（经常被查询，但不经常被修改或删除的数），首选redis。
选数据前查询redis，存在，使用redis数据，放弃select数据库；不存在，select数据库，然后将数据插入redis。
update或delete数据库前，查询redis中是否存在该数据，存在的话先删除redis中的数据，然后再更新或删除数据库中的数据。
2.计数器  命令：INCRBY
3.队列
4.位操作
用于数据量上亿的场景下，例几亿用户系统的签到，去重登录次数统计，某用户是否在线状态等等。
用到的命令是：setbit、getbit、bitcount
setbit peter 1 1，setbit peter 100 1 //第100天1的个数    bitcount peter统计  //用户上线天数统计
setbit isonline 1001 1 //1001代表userid，1上线，0离线
5.分布式和单线程机制
秒杀系统，基于redis是单线程特征，防止出现数据库“爆破”；
全局增量ID生成，类似“秒杀”。
6.最新列表
新闻列表页最新的新闻列表。redis的 LPUSH命令构建List，一个个顺序都塞进去就可以啦。不过万一内存清掉了咋办？也简单，查询不到存储key的话，用mysql查询并且初始化一个List到redis中即可。
7.排行榜（有序集合）
谁得分高谁排名往上。命令：ZADD（有续集，sorted set）。
 Zadd 命令用于将一个或多个成员元素及其分数值加入到有序集当中。
 ZADD KEY_NAME SCORE1 VALUE1.. SCOREN VALUEN
```

**分布式锁**

```shell
redis能用的的加锁命令分表是INCR、SETNX、SET。
1 incr方式
key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 INCR 操作进行加一。
然后其它用户在执行 INCR 操作进行加一时，如果返回的数大于 1 ，说明这个锁正在被使用当中。

客户端A请求服务器的key值为1表示获取锁；客户端B也去请求服务器获取key的值为2表示获取锁失败； 客户端A执行代码完成，删除锁； 客户端B在等待一段时间后在去请求的时候获取key的值为1表示获取锁成功； 客户端B执行代码完成，删除锁。
$redis->incr($key);
$redis->expire($key, $ttl); //设置生成时间为1秒

2 setnx方式
若 key 不存在，将 key 设置为 value。如果 key 已存在，则 SETNX 不做任何动作若key已存在，则setnx不做任何操作。
客户端A请求服务器设置key的值，如果设置成功就表示加锁成功；
客户端B也去请求服务器设置key的值，如果返回失败，那么就代表加锁失败；
客户端A执行代码完成，删除锁；
客户端B在等待一段时间后在去请求设置key的值，设置成功；
客户端B执行代码完成，删除锁。
$redis->setNX($key, $value);
$redis->expire($key, $ttl);

3.set方式加锁
前两种方式加锁的弊端：都需要设置key过期时间，原因如下：若请求执行因为某些原因意外退出了，导致创建了锁但是没有删除锁，那么这个锁将一直存在，以至于以后缓存再也得不到更新。于是乎我们需要给锁加一个过期时间以防不测。但是借助 Expire 来设置就不是原子性操作了。所以还可以通过事务来确保原子性，但是还是有些问题。

客户端A请求服务器设置key的值，如果设置成功就表示加锁成功
客户端B也去请求服务器设置key的值，如果返回失败，那么就代表加锁失败
客户端A执行代码完成，删除锁
客户端B在等待一段时间后在去请求设置key的值，设置成功
客户端B执行代码完成，删除锁

$redis->set($key, $value, array('nx', 'ex' => $ttl)); //ex表示秒
```



## 3、redis持久化

` 建议至少开启RDB方式的数据持久化`

- **快照:** redis落盘的方式(Redis DataBase)，持久化方式能够在指定的时间间隔能对你的数据进行快照存储。

  ```shell
  # 缺点是定时快照只是代表一段时间内的内存映像，所以系统重启会丢失上次快照与重启之间所有的数据
  save 60 100 每60秒检查一次数据变更，若发生了100次或以上的数据变更，则进行RDB快照保存，自动触发BGSAVE
  
  save 60 1000  多久执行一次自动快照操作，60s之内有1000次操作写入时执行
  stop-writes-on-bgsave-error no 创建快照失败后是否仍然继续执行写命令
  rdbcompression yes 是否对快照文件进行压缩
  dbfilename dump.rdb  该种配置方式快照文件存放位置不固定，而是存放在启动redis的当前目录
  
  dir ./  快照文件或AOF文件的保存位置
  ```

  - **RDB优劣分析：**

    ```shell
    # 优势
    1)RDB方式的持久化几乎不损耗Redis本身的性能，在进行RDB持久化时，Redis主进程唯一需要做的事情就是fork出一个子进程，所有持久化工作都由子进程完成。
    2)Redis无论因为什么原因crash掉之后，重启时能够自动恢复到上一次RDB快照中记录的数据。这省去了手工从其他数据源（如DB）同步数据的过程，而且要比其他任何的数据恢复方式都要快。
    3)现在硬盘那么大，真的不缺那一点地方。每次快照会生成一个完整的数据快照文件，所以可以辅以其他手段保存多个时间点的快照（例如把每天0点的快照备份至其他存储媒介中），作为非常可靠的灾难恢复手段。
    # 劣势
    1)快照是定期生成的，所以在Redis crash时或多或少会丢失一部分数据。
    2)如果数据集非常大且CPU不够强（比如单核CPU），Redis在fork子进程时可能会消耗相对较长的时间（长至1秒），影响这期间的客户端请求。
    3)定时快照只是代表一段时间内的内存映像，所以系统重启会丢失上次快照与重启之间所有的数据。
    ```

- **只追加文件AOF：**类似于mysql的基于语句的binlog方式，持久化记录每次对服务器写的操作， 当服务器重启的时候会重新执行这些命令来恢复原始的数据，AOF命令以redis协议追加保存每次写的操作到文件末尾。

  Redis还能对AOF文件进行后台重写，使AOF文件的体积不至于过大

  ```shell
  # AOF rewrite功能，可重写AOF文件，只保留能够把数据恢复到最新状态的最小写操作集；AOF日志大小在该基础上增长了100%后，自动进行AOF rewrite。同时如果增长的大小没有达到64mb，则不会进行rewrite。always/everysec/no，always表示每写入一条日志就进行一次fsync操作，数据安全性最高，但速度最慢
  appendonly yes  开启AOF
  appendfsync everysec  折中的做法，交由后台线程每秒fsync一次
  appendfsync no 不进行fsync，将flush文件的时机交给OS决定，速度最快
  auto-aof-rewrite-percentage 100 
  auto-aof-rewrite-min-size 64mb   
  ```

  - **AOF优缺点**

    ```shell
    # 优点
    1)最安全，在启用appendfsync always时，任何已写入的数据都不会丢失，使用在启用appendfsync everysec也至多只会丢失1秒的数据。
    2)AOF文件在发生断电等问题时也不会损坏，即使出现了某条日志只写入了一半的情况，也可以使用redis-check-aof工具轻松修复。
    3)AOF文件易读，可修改，在进行了某些错误的数据清除操作后，只要AOF文件没有rewrite，就可以把AOF文件备份出来，把错误的命令删除，然后恢复数据。
    # 缺点
    1)AOF文件通常比RDB文件更大
    2)性能消耗比RDB高
    3)数据恢复速度比RDB慢
    ```

## 4、redis优化

```shell
Redis是一个开源的，基于内存的结构化数据存储媒介，可以作为数据库、缓存服务或消息服务使用。Redis采用单线程模型，天然是线程安全的。但当某个命令执行耗时较长时，会拖慢其后的所有命令。
Redis支持多种数据结构，包括字符串、哈希表、链表、集合、有序集合、位图、Hyperloglogs等。
redis内存数据库，速度快，但是内存贵。主存储和辅助存储的抉择（费用和性能）
# 注意事项
1.key
1)不要使用过长的Key。如使用一个1024字节的key不好，不仅会消耗更多的内存，还会导致查找的效率降低
2)Key短到缺失是不好的，如u1000flw比user:1000:followers，节省了存储空间，却引发可读性和可维护性上的麻烦
3)最好用统一的规范来设计Key，如object-type:id:attr，以该规范设计的Key可为user:1000或comment:1234:reply-to
4)Redis允许的最大Key长度是512MB（对Value的长度限制也是512MB）
2 内存管理
默认情况下，在32位OS中，Redis最大使用3GB的内存，在64位OS中则没有限制。防止Redis会无限制地占用内存，
maxmemory 100mb  //在内存占用达到了maxmemory后，再向Redis写入数据时，Redis会：根据配置的数据淘汰策略尝试淘汰数据，释放空间如果没有数据可以淘汰，或者没有配置数据淘汰策略，那么Redis会对所有写请求返回错误，但读请求仍然可以正常执行
3 数据淘汰策略(推荐使用volatile-lru)
maxmemory-policy volatile-lru   #默认是noeviction，即不进行数据淘汰
volatile-lru（常用淘汰算法）：用LRU算法进行淘汰（淘汰上次使用时间最早的，且使用次数最少的key），只淘汰设定有效期的key
allkeys-lru：使用LRU算法进行数据淘汰，所有的key都可以被淘汰
volatile-random：随机淘汰数据，只淘汰设定了有效期的key
allkeys-random：随机淘汰数据，所有的key都可以被淘汰
volatile-ttl：淘汰剩余有效期最短的key

# 优化 
1 尽可能采用物理机部署redis
2 长耗时命令。O(1)的命令是安全的，O(N)命令在使用时需要注意，如果N的数量级不可预知，则应避免使用。
避免在使用这些O(N)命令时发生问题主要有几个办法：
不要把List当做列表使用，仅当做队列来使用;通过机制严格控制Hash、Set、Sorted Set的大小
可能的话，将排序、并集、交集等操作放在客户端执行;绝对禁止使用KEYS命令;
避免一次性遍历集合类型的所有成员，而应使用SCAN类的命令进行分批的，游标式的遍历
3 网络引发的延迟(尽可能使用长连接和连接池，避免频繁的创建销毁连接；客户端进行的批量数据操作，应使用 Pipeline特性在一次交互中完成) 减少维护网络连接和传输数据所消耗的资源和时间。优先使用批量设置。RTT(数据包必须从client发送到server端，然后再从server端返回到client端，这个时间叫做RTT)
4 数据持久化引发的延迟
5 swap引发的延迟 不要开启Redis的VM选项，即虚拟内存功能，vm-enabled 为 no。
6 maxmemory选项，该选项是告诉Redis当使用了多少物理内存后就开始拒绝后续的写入请求。#分配512M内存
7 不要让你的Redis所在机器物理内存使用超过实际内存总量的3/5
8 主从复制和集群分片
Redis支持一主多从的主从复制架构。一个Master实例负责处理所有的写请求，Master将写操作同步至所有Slave。
借助于主从复制，可以实现读写分离。实时性要求不是特别高的读请求，可以在Slave上完成，提升效率。借助Redis Sentinel可实现高可用，当Master crash后，Redis Sentinel能够自动将一个Slave晋升Master，继续提供服务。
slaveof 192.168.1.1 6379  #指定Master的IP和端口
Redis Sentinel是Redis官方开发的监控组件，可以监控Redis实例的状态，通过Master节点自动发现Slave节点， 并在监测到Master节点失效时选举出一个新的Master，并向所有Redis实例推送新的主从配置。
Redis Sentinel至少部署3个实例才能形成选举关系（参考下图）。

# 快的原因
Redis是完全基于内存。Redis是纯内存数据库，相对于读写磁盘，读写内存的速度就不是几倍几十倍了，一般，hash查找可以达到每秒百万次的数量级
```

![05_4Redis Sentinel部署图](./image/05_4Redis Sentinel部署图.png)

## 5、性能测试命令

```shell
redis-benchmark进行redis性能测试
redis-benchmark -h 192.168.1.201 -p 6379 -c 100 -n 100000
100个并发连接，100000个请求，检测host为localhost 端口为6379的redis服务器性能
redis-benchmark -h 192.168.1.201 -p 6379 -q -d 100  //测试存取大小为100字节的数据包的性能
redis-benchmark -t set,lpush -n 100000 –q  //只测试某些操作的性能
redis-benchmark -n 100000 -q script load "redis.call('set','foo','bar')" //只测试某些数值存取的性能
```



 [redis与memcache区别](https://baijiahao.baidu.com/s?id=1628669085694391797&wfr=spider&for=pc)

[Redis与Memcache区别最全整理](https://baijiahao.baidu.com/s?id=1665364123307535771&wfr=spider&for=pc)

## 6、参考链接

[Redis 内存使用优化与存储](https://www.infoq.cn/article/tq-redis-memory-usage-optimization-storage/)

[Redis 基础、高级特性与性能调优](https://mp.weixin.qq.com/s?__biz=MzI5ODQ2MzI3NQ==&mid=2247485738&idx=1&sn=c0abb62e77e4a4ba86ffaa4ee2d2c305&chksm=eca4306edbd3b978c060b530ef1d0d37bd2d6406ae565fbaceb3677708f3b4f943583bb48863&scene=27#wechat_redirect)

[redis中文网站](http://www.redis.cn/)

[redis加锁的几种实现](https://blog.csdn.net/mojiezuo123/article/details/81205773)

[Redis的8大应用场景](https://cloud.tencent.com/developer/article/1415674)

