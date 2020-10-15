[HTML教程](https://www.runoob.com/html/html-tutorial.html)

[HTML实例](https://www.runoob.com/html/html-examples.html)

html编辑器：[VS Code](https://code.visualstudio.com/)

[html速查列表](https://www.runoob.com/html/html-quicklist.html)

# 1 HTML语法

html后缀名： **.htm** 也可以使用 **.html** 扩展名，建议统一用 **.html**。HTML 标签对大小写不敏感：<P> 等同于 <p>。菜鸟使用小写，因为万维网联盟（W3C）在 HTML 4 中**推荐**使用小写，而在未来 (X)HTML 版本中**强制**使用小写。 HTML5 是下一代 HTML 标准。

- **html实例解析**

  ```html
  <!DOCTYPE html>				<!-- 声明为HTML5文档 -->
  <html>						<!-- HTML页面的根元素 -->
  <head>						<!-- 包含了文档的元（meta）数据 -->
  <meta charset="utf-8">		<!-- 定义网页编码格式为utf-8 -->
  <title>菜鸟教程(runoob.com)</title>	<!-- 描述了文档的标题 -->
  </head>
  <body>						<!-- 包含了可见的页面内容 -->
   
  <h1>我的第一个标题</h1>		<!-- 定义一个大标题 -->
   
  <p>我的第一个段落。</p>			<!-- 定义一个段落 -->
   
  </body>
  </html>
  ```

- **HTML 基础**

  标题（Heading）是通过<h1> - <h6> 标签来定义的。

  段落通过标签 <p> 来定义的(开始标签 <p> 以及一个结束标签 </p>)。

  链接是通过标签 <a> 来定义的。图像通过<img>来定义。

  ```html
  <h1>这是一个标题</h1>
  <h2>这是一个标题</h2>
  <h3>这是一个标题</h3>
  
  <p>这是一个段落。</p>  
  <p>这是另外一个段落。</p>
  
  <a href="https://www.runoob.com">菜鸟教程链接</a>
  
  <img loading="lazy" src="/images/logo.png" width="258" height="39" />
  ```

 - **html语法**

   - **html元素语法**：开始标签；结束标签；元素的内容（开始标签和结束标签的内容）；某些html具有空内容；空元素在开始标签中进行关闭（以开始标签的结束而结束）；大多数 HTML 元素可拥有属性。

   - **属性：**属性值应该始终被包括在引号内。双引号是最常用的，不过使用单引号也没有问题。

     html属性：元素可设置属性；属性可以在元素中添加附加信息；属性一般描述于开始标签；属性总是以名称/值对的形式出现，**比如：name="value"**。

     | 属性  | 描述                                                         |
     | :---- | :----------------------------------------------------------- |
     | class | 为html元素定义一个或多个类名（classname）(类名从样式文件引入) |
     | id    | 定义元素的唯一id，只能填写一个，多个无效                     |
     | style | 规定元素的行内样式（inline style）                           |
     | title | 描述了元素的额外信息 (作为工具条使用)                        |

     ```html
     <!-- 这是一个注释 -->
     <html> <!-- 定义 HTML 文档 -->  
     <body> <!-- 定义文档主题 --> 
     <br>   <!-- 换行符 -->  
     <hr> <!-- 标签在 HTML 页面中创建水平线 -->
     <h1> - <h6>  <!-- 定义html标题 -->
     <p>    <!-- 定义了一个段落 -->  
     <a href="url">链接文本</a>   <!-- 链接文本 -->  
     <meta name="author" content="Runoob">    <!-- meta属性，定义网页作者 -->
     <meta http-equiv="refresh" content="30"> <!-- meta属性，每30s刷新当前页面 -->
     
     <!-- 字体相关 -->
     <p>这是一个普通的文本- <b>这是一个加粗文本</b>。</p>  
     <em>强调文本</em><br>
     <p>He named his car <i>The lightning</i>, because it was very fast.</p>
     <small> Copyright 1999-2050 by Refsnes Data.</small>
     <strong>加粗文本</strong><br>
     <p>这个文本包含 <sub>下标</sub>文本。</p>
     <p>这个文本包含 <sup>上标</sup> 文本。</p>
     <p>My favorite color is <del>blue</del> <ins>red</ins>!</p>
     
     <!-- html计算机输出标签 -->
     <code>一段电脑代码 print("Hello World")</code>
     <kbd>键盘输入</kbd>
     <var>变量</var>
     <pre>定义预格式文本</pre>
     
     <!-- 引文、引用标签定义 -->    
     The<abbr title="World Health Organization">WHO</abbr> was founded in 1948.
     <address>
     Written by <a href="mailto:webmaster@example.com">Jon Doe</a>.<br> 
     Visit us at:<br>
     Example.com<br>
     Box 564, Disneyland<br>
     USA
     </address>
     <dfn>定义项目</dfn>
     
  <!-- 无序列表 -->  
     <ul>
       <li>Coffee</li>
       <li>Tea</li>
       <li>Milk</li>
     </ul>
     ```
   
   - html查看源代码：  只需要单击右键，然后选择"查看源文件"（IE）或"查看页面源代码"（Firefox）


# 2 html样式- CSS

css用于渲染HTML元素标签的样式。

```html
<p style="color:blue;margin-left:20px;">这是一个段落。</p>

<body style="background-color:yellow;">
<h1 style="text-align:center;">居中对齐的标题</h1>
<h2 style="background-color:red;">这是一个标题</h2>
<p style="background-color:green;">这是一个段落。</p>
<p style="font-family:arial;color:red;font-size:20px;">一个段落。</p>
</body>
```

- 内部样式表：单个文件需要特别的样式时，就需要用内部样式表。可以在head内部通过<style>标签定义内部样式表。

  ```html
  <head>
  <style type="text/css">
  body {background-color:yellow;}
  p {color:blue;}
  </style>
  </head>
  ```

- 外部样式表：当样式要被应用到很多页面时，外部样式表将是理想的选择。使用外部样式表，你就可以通过更改一个文件来改变整个站点的外观。

  ```html
  <head>
  <link rel="stylesheet" type="text/css" href="mystyle.css">
  </head>
  ```

- html表单

  ```html
  <!-- html表单 -->
  <form>
  First name: <input type="text" name="firstname"><br>
  Last name: <input type="text" name="lastname">
  Password: <input type="password" name="pwd">
  </form>
  
  <!-- 单选按钮 -->
  <form>
  <input type="radio" name="sex" value="male">Male<br>
  <input type="radio" name="sex" value="female">Female
  </form>
  
  <!-- 复选框（Checkboxes） -->
  <form>
  <input type="checkbox" name="vehicle" value="Bike">I have a bike<br>
  <input type="checkbox" name="vehicle" value="Car">I have a car
  </form>
  
  <!-- 提交按钮 -->
  <form name="input" action="html_form_action.php" method="get">
  Username: <input type="text" name="user">
  <input type="submit" value="Submit">
  </form>
  
  ```


