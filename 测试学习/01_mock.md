### 1 、 uniittest.mock库

[使用python的mock库进行pyspark单元测试](https://www.cnblogs.com/hhelibeb/p/10508692.html#_label0)

https://www.cnblogs.com/Zzbj/p/10594633.html

```python

```



### 2 、mock测试

- [接口测试-Mock测试方法](https://blog.csdn.net/qq_35716699/article/details/90581880)

```pyhton
mock测试： 测试过程中不容易构造或者不容易获取的对象用虚拟对象（mock对象）来创建以便测试的测试方法。
mock优势：遇到依赖接口未准备好，可以借助mock;用mock模拟无法访问的资源;数据演示（用mock接口把敏感信息替换）;
用mock模拟返回；
mock工具注意事项：数据要管理好；mock接口和真实接口设计一致，只需要切换hosts就可以切换mock接口和真实接口；mock接口跨平台

```

- [Moco已经在github上开源](https://github.com/dreamhead/moco.git)

```python
jar下载： https://repo1.maven.org/maven2/com/github/dreamhead/moco-runner/1.1.0/moco-runner-1.1.0-standalone.jar

模拟httpserver命令：  
java -jar <path-to-moco-runner> http -p <monitor-port> -c <configuration -file>
java -jar <path-to-moco-runner> https -p <monitor-port> -c <configuration -file> --https <path-to-cert.jks > --cert mocohttps --keystore mocohttps  # https方式
# <path-to-moco-runner>：moco-runner-0.11.0-standalone.jar包的路径
# <monitor-port>：http服务监听的端口
# <configuration -file>：配置文件路径
# <path-to-cert.jks>：证书路径

http方式举例：
java -jar ./moco-runner-1.1.0-standalone.jar http -p 12345 -c ./foo.json 
json文件参考链接: https://github.com/dreamhead/moco/blob/master/moco-doc/apis.md
```

- **使用Fiddler进行Mock测试**

  参考链接 ： https://blog.csdn.net/qq_35716699/article/details/90581880

