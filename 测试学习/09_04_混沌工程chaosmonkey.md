## 1 Chaos Monkey

[官方doc](https://netflix.github.io/chaosmonkey/Configuration-file-format/)

[chaosmonkey的github官网](https://github.com/Netflix/chaosmonkey)

### 1.1 文档学习记录

```shell
1) 配置文件
2）通过Spinnaker配置行为  # Spinnaker介绍  https://www.debian.cn/archives/2577
# Chaos Monkey终止每个应用程序实例的频率
Chaos Monkey的平均终止时间为2天，这意味着平均而言，Chaos Monkey每两天就会为该应用中的每个组终止一个实例；最短的终止时间，默认为1天。这就意味着混沌猴可以保证每组每天只杀一次人。
# 分组
应用程序，堆栈，集群。如果分组设置为“app”，那么不管这些实例是如何组织成集群的，Chaos Monkey每天都会终止每个应用一个实例。如果分组设置为“堆栈”，每天每个堆栈最多终止一个实例。如果分组设置为“cluster”，Chaos Monkey每天最多终止每个集群一个实例。
#异常
异常字段还支持通配符*，它可以匹配所有内容。您可以选择退出帐户、区域、堆栈和详细信息的组合。
3）解密器
在运行时加密存储您的密码并使用解密系统，需要给你的解密器起个名字，在go中编写实现解密器接口的类型。
4) 错误计数器
5）如何部署 https://netflix.github.io/chaosmonkey/How-to-deploy/
6）停机检查
7）本地运行
docker容器启动  docker run -e MYSQL_ROOT_PASSWORD=password -p3306:3306 mysql:5.6
8）运行test
go test ./...
```

## 2 混沌工程

```shell

```



