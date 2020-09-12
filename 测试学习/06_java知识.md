## 1、java语法

[java菜鸟教程](https://www.runoob.com/java/java-character.html)

```java
double[] myList;         // 首选的方法 声明数组
arrayRefVar = new dataType[arraySize]; //创建数组

dataType[] arrayRefVar = new dataType[arraySize];
dataType[] arrayRefVar = {value0, value1, ..., valuek}; //申明并创建数组



```

## 2、tika提取

```java
cmd窗口：
文本复制:右键，标记。
echo %JAVA_HOME%
一 windows下java jdk安装:
1.下载 http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
2.配置环境变量 在PATH中配置
C:\Program Files\Java\jdk1.8.0_121\bin
3.验证安装效果
搜索框中输入cmd，进入dos命令框中输入javac
java -version查看对应版本
javac
java

二 windows下设置JAVA_HOME
JAVA_HOME D:\professorsoftware\javaJDK

三 Apache Tika环境配置：
1.下载源代码 http://tika.apache.org/download.html
下载
tika-app-1.19.1.jar
tika-1.19.1-src.zip (源码)
2.配置环境变量
CLASSPATH D:\professorsoftware\jars\tika-app-1.19.1.jar
3.打开Tika对应的gui，将文本拖拽入gui进行解析即可。打开view-xxxx即可查看提取的文本。
java -jar D:\professorsoftware\jars\tika-app-1.19.1.jar --gui


maven介绍
https://blog.csdn.net/shuzhe66/article/details/45009175
maven核心功能是合理叙述项目间的依赖关系。
```

