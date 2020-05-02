## 1、代码块

```shell
// ```shell （反引号+语言名）
cd dirname
ls dirname  
```

```java
public class Test {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}   
```

```go
func PrintMsg(str string){
    fmt.Println("hello ",str)
}   // ```go
```

## 2、标题

```java
// #与文字之间有空格
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```

## 3、字体

```java
**加粗**  __加粗__
*斜体*   _斜体_
***粗斜体***   ___粗斜体___  
==高亮==
`你好`
~~被删除的文字~~
上标：X<sub>2</sub>，下标：O<sup>2</sup>    
```

## 4、引用

```java
>作者:Lily
>>作者:Lily
>>>作者:Lily  
```

## 5、分割线

```java
//形式1
--- 
//形式2
***   
```

## 6、图片插入与页面内跳转

```java
//在线图片插入
![图片1](https://image.baidu.com/search/dog.jpg)
//本地图片
![图片1](./image/测试图片.jpg)
![图片1](E:\document_it\image\测试图片.jpg)
       
//页面内跳转
[跳转个人标题](# 个人标题)
##### 个人标题

[跳转到开发指南](# tagNotice)
<p id="tagNotice">开发指南</p>

[目录1](#目录测试)
### 目录测试       
```

## 7、超链接

```java
//typoa软件中不能直接跳转，写到博客中可以实现跳转
[百度网址]("https://www.baidu.com/")
[百度网址](https://www.baidu.com/ "百度的主页")
<a href="https://www.baidu.com/" title="百度的主页">百度网址</a>
<a href="https://www.baidu.com/" title="百度的主页">百度网址</a>
```



## 8、列表

```java
// 无序列表
- 列表一
* 列表一  
+ 列表一
    
+ 列表二
    + 列表二-1
+ 列表三
    * 列表三-1    
// 有序列表
1. 第一行
2. 第二行
// 任务列表
- [x] 任务1
- [x] 任务2
- [ ] 任务3
    - [ ] 任务3-1
    - [ ] 任务3-2
```

## 9、表格

```java
//表格 左对齐，居中，右对齐
| 成绩 | 语文 | 数学 |
| :--- | :--: | ---: |
| 小明 |  96  |  100 |
| `destroy()`   | **Destroy your computer!** | Display the help window.|   

```



## 10、公式

```java
// 常用数学公式   参考markdown在线编辑器网址: http://www.mdeditor.com/
$$E=mc^2$$
$$x > y$$
$$\(\sqrt{3x-1}+(1+x)^2\)$$
$$\sin(\alpha)^{\theta}=\sum_{i=0}^{n}(x^i + \cos(f))$$

​```math
\displaystyle
\left( \sum\_{k=1}^n a\_k b\_k \right)^2
\leq
\left( \sum\_{k=1}^n a\_k^2 \right)
\left( \sum\_{k=1}^n b\_k^2 \right)
​```
​```katex
\displaystyle
    \frac{1}{
        \Bigl(\sqrt{\phi \sqrt{5}}-\phi\Bigr) e^{
        \frac25 \pi}} = 1+\frac{e^{-2\pi}} {1+\frac{e^{-4\pi}} {
        1+\frac{e^{-6\pi}}
        {1+\frac{e^{-8\pi}}
         {1+\cdots} }
        }
    }
​```
​```latex
f(x) = \int_{-\infty}^\infty
    \hat f(\xi)\,e^{2 \pi i \xi x}
    \,d\xi
​```
        
![公式图片](./image/公式图片.png)
```



![公式图片](./image/公式图片.png)

**markdown编辑器推荐： typora，参考链接：[typora网址](https://www.typora.io/)**











