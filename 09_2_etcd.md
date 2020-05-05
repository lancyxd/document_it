## 1、etcd指令

`etcd是一个分布式的，一致的 key-value 存储，主要用途是共享配置和服务发现。是可靠的共享存储服务(/bin)。当前的大多数使用方式是通过watch得知变更，然后通过get重新获取数据。`

```shell
1.key操作
etcdctl --version
etcdctl --help
etcdctl get mykey
etcdctl set test 11 -ttl 10 //设置超时时间为10s
etcdctl rm mykey
etcdctl watch key //监控值的改动
etcdctl watch test --forever //不退出一直监测键值的改动(set和delete)
etcdctl watch key  --recursive --forever //返回所有的键值和子键值
etcdctl exec-watch test -- sh -c 'echo value has changed' //相比watch，增加了监测到改动可以执行自定义的操作,输出value has changed
etcdctl member list(list,add,remove命令列出,添加,删除etcd实例到etcd集群中)
2.目录相关操作(目录类型的key可以用–recursive 来检测目录下的子keys的改动)
etcdctl set /home/lxd/godir/etcd_test/mykey1 1111 //目录路径(/home/lxd/godir/etcd_test)
etcdctl ls /home/lxd/godir/etcd_test
curl -X POST http://localhost:2379/v2/keys/home/lxd/godir/etcd_test -d value=333 //设置值会自动创建目录
etcdctl rmdir /home/lxd/godir/etcd_test //移除目录，保证该目录下没有keys
etcdctl rm /home/lxd/godir/etcd_test --recursive=true //目录和keys都删除可以用rm指令
3.状态查看
curl http://127.0.0.1:2379/v2/stats/leader  //查看leader
curl http://127.0.0.1:2379/v2/stats/self  //查看自身状态
curl http://127.0.0.1:2379/v2/stats/store //查看store的状态
```

