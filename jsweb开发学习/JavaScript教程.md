[JavaScript 教程](https://www.runoob.com/js/js-tutorial.html)

html定义了网页的内容; css描述了网页的布局；js定义了网页的行为。

# 1 JavaScript简介和语法规范

js是一种轻量级的编程语言，可插入html页面的编程代码，单行注释以 // 开头。

- **JavaScript版本**

  ECMAScript 是该语言的官方名称。从 2015 年起，ECMAScript 按年命名（ECMAScript 2015）。 ES 是官方叫法，而 JS 则是民间叫法。

- html中使用js

  在 HTML 中，JavaScript 代码必须位于 <script> 与 </script> 标签之间。脚本可放置在html页面的<body>或<head>部分中，或者兼而有之。

  ```html
  <!DOCTYPE html>
  <html>
  <body> 
  
  <h1>A Web Page</h1>
  <p id="demo">一个段落</p>
  <button type="button" onclick="myFunction()">试一试</button>
  
  <script>
  function myFunction() {
     document.getElementById("demo").innerHTML = "段落被更改。";
  }
  </script>
  
  </body>
  </html>
  ```

- 外部脚本(优势：分离了html和代码；使html和js更易阅读和维护；已缓存的js文件可加速页面加载)

  - 脚本可放置与外部文件中(myScript.js)。JavaScript 文件的文件扩展名是 *.js*。可以在 <head> 或 <body> 中放置外部脚本引用。

  ```javascript
  function myFunction() {
     document.getElementById("demo").innerHTML = "段落被更改。";
  }
  ```

  - 使用外部脚本(在 <script> 标签的 src (source) 属性中设置脚本的名称)：

  ```html
  <script src="myScript1.js"></script>
  <script src="myScript2.js"></script>
  <script src="https://www.w3school.com.cn/js/myScript1.js"></script>  <!-- 使用完整的 URL 来链接至脚本 -->
  <script src="/js/myScript1.js"></script>  <!-- 当前网站上指定文件夹中的脚本 -->
  ```

- **[样式指南和代码约定](https://www.w3school.com.cn/js/js_conventions.asp)** 

  - **样式方针**

    变量和函数的命名和声明规则：变量和函数使用驼峰式大小写 firstName = "Bill"; 全局变量使用大写。常量（比如 PI）使用大写。

    使用空格、缩进和注释的规则：始终在运算符（ = + - * / ）周围以及逗号之后添加空格；使用对代码块缩进使用 4 个空格。行长度小于 80。

    编程习惯和准则：请始终以分号结束单条语句;**请避免全局变量、new、===、eval()**。请不要声明数值、字符串或布尔对象，请始终将数值、字符串或布尔值视作原始值。而非对象。请勿使用 new Object()。使用 === 比较。使用参数默认值。用 default 来结束 switch。用逗号来结束定义。减少循环中的活动。避免不必要的变量。延迟JavaScript的加载(请把脚本放在页面底部，使浏览器首先加载页面)。避免使用with关键字。

    ```javascript
    function myFunction(x, y) {
        if (y === undefined) {
            y = 0;
        }
    } // 缺失参数的值会被设置为 undefined,undefined值会破坏代码，为参数设置一个默认值是好习惯。
    
    // 避免使用 eval()。eval() 函数用于将文本作为代码来允许。
    
    var i;
    var l = arr.length;
    for (i = 0; i < l; i++) { // 若arr.length替代for中的l，则代码不好
    
    // 避免不必要的变量  var fullName = firstName + " " + lastName;
    // document.getElementById("demo").innerHTML = fullName; 
    document.getElementById("demo").innerHTML = firstName + " " + lastName
     
    
    ```

    

  - **确保质量（改善和提升代码可读性）**


# 2 JavaScript语法

- 书写规范

  ```javascript
  first_name, last_name, master_card, inter_city //下划线式
  FirstName, LastName, MasterCard, InterCity //驼峰式大小写（Camel Case）
  firstName, lastName, masterCard, interCity // js程序员倾向于使用以小写字母开头的驼峰大小写
  ```

## **2.1 基本语法**

[js保留字](https://www.w3school.com.cn/js/js_reserved.asp)


分号分隔JavaScript语句。JavaScript 标识符对大小写敏感。

JavaScript空白字符：会忽略多个空格。可以向脚本中添加多个空格，以增强可读性。

行长度和折行：若JavaScript 语句太长，对其进行折行的最佳位置是某个运算符。

JavaScript关键字：break,continue,debugger(停止执行 JavaScript，并调用调试函数),do ... while,for,function,if ... else,return,switch,try ... catch,var(声明变量)。

JavaScript数值属性：MAX_VALUE(返回 JavaScript 中可能的最大数)，MIN_VALUE( JavaScript 中可能的最小数)，NEGATIVE_INFINITY(表示负的无穷大溢出返回)， NaN(表示非数字值)，POSITIVE_INFINITY(表示无穷大，溢出返回)。

```javascript
"use strict"; // JavaScript 1.8.5 中的新指令，是一段文字表达式，其作用是指示js代码应该以"严格模式执行"
x = 3.14;       // 这会引发错误，因为 x 尚未声明


双斜杠 // 或 /* 与 */ 之间的代码被视为注释。

console.log() // 写入浏览器控制台 
window.alert() //写入警告框		window.alert(5 + 6);
document.write() //写入HTML输出		document.write(5 + 6);
innerHTML //写入HTML元素

// js语句: 由值、运算符、表达式、关键词和注释构成。
var x,y; //声明变量
x = 22; //赋值
y = 11; //计算值
var z = x + y; 
document.getElementById("demo").innerHTML = "Hello Kitty."; // 告诉浏览器在id="demo"的html元素中输出"Hello Kitty."
a = 5; b = 6; c = a + b; // 若有分号分隔，允许在同一行写多条语句
var pi = 3.14;
var person = "Bill Gates", carName = "porsche", price = 15000; //以var开头，以逗号分隔变量
var x = "Bill" + " " + "Gates"; // 字符串是文本，由双引号或单引号包围。
var x = 3 + 5 + "8"; // 结果是88
var z = "Hello" + 7; // 结果是Hello7
var x = "Porsche" + 911 + 7; // 结果是Porsche9117

// 全局变量和局部变量：在 JavaScript 函数中声明的变量，会成为函数的局部变量。局部变量只能在函数内访问。
// let关键词声明拥有块作用域的变量。在块{}内声明的变量无法从快外访问。const 定义的变量与 let 变量类似，但不能重新赋值。
{ 
  let x = 10;
}
// 此处不可以使用 x

const PI = 3.141592653589793;
PI = 3.14;      // 会出错


// 数据类型
var length = 7;	// 数字
var lastName = "Gates";	// 字符串
var cars = ["Porsche", "Volvo", "BMW"];	// 数组，数组索引基于零
var x = {firstName:"Bill", lastName:"Gates"};	// 对象 
var x = true; // 布尔值  false
typeof ""	 //  typeof返回变量的类型，返回 "string" 
typeof (7)  // 返回 "number"
typeof true // 返回 "boolean"
var person; // 值是 undefined，类型是 undefined
var person = null;	// 值是 null，但是类型仍然是对象 
// null和undefined的区别：Undefined 与 null 的值相等，但类型不相等。
(3.14).constructor // 返回 "function Number()  { [native code] }" 。 constructor 属性返回所有 JavaScript 变量的构造器函数。

// 运算符
var x = 7;
x += 8;
var z = 7 % 2;	// 结果是 1
var z = 5 ** 2;	// 结果是 25  Math.pow(x,2)
instanceof;// 返回 true，如果对象是对象类型的实例。

// 数字及数字方法
var x = 123e5;    // 12300000
var y = 123e-5;   // 0.00123
var x = 123;
x.toString();            // 从变量 x 返回 123
(123).toString();        // 从文本 123 返回 123
(100 + 23).toString();   // 从表达式 100 + 23 返回 123
5 + null    // 返回 5         因为 null 被转换为 0
"5" + 2     // 返回 52        因为 2 被转换为 "2"
var x = 9.656;
x.toFixed(0);           // 返回 10 返回字符串值，它包含了指定位数小数的数字
x.toFixed(2);           // 返回 9.66
x.toPrecision();        // 返回 9.656  toPrecision() 返回字符串值，它包含了指定长度的数字：
x.toPrecision(2);       // 返回 9.7
var x = 9.656;  // toExponential() 返回字符串值，它包含已被四舍五入并使用指数计数法的数字
x.toExponential(2);     // 返回 9.66e+0
x.toExponential(4);     // 返回 9.6560e+0

Number(new Date("2019-04-15"));    // 返回 1506729600000  日期转数字
parseInt("10.33");      // 返回 10
parseFloat("10");        // 返回 10


// 日期及获取方法: https://www.w3school.com.cn/js/js_date_methods.asp
var d = new Date(); // Tue Apr 02 2019 09:01:19
var d = new Date(2018, 11, 24, 10, 33, 30, 0); // Sat Jan 25 2020 10:33:30 GMT+0800
getDate()
setDate()

// js折行
document.getElementById("demo").innerHTML =
 "Hello Kitty.";
document.getElementById("demo").innerHTML = "Hello \
Kitty!";
document.getElementById("demo").innerHTML = "Hello" + 
"Kitty!";

// js代码块
function myFunction() {
    document.getElementById("demo").innerHTML = "Hello Kitty.";
    document.getElementById("myDIV").innerHTML = "How are you?";
}

```

## **2.2 js函数**和闭包

- **函数**

```javascript
var x = myFunction(7, 8);        // 调用函数，返回值被赋值给 x

function myFunction(a, b) {
    return a * b;                // 函数返回 a 和 b 的乘积
}

// 华氏转摄氏
function toCelsius(fahrenheit) {
    return (5/9) * (fahrenheit-32);
}

// 函数表达式,在变量中保存函数表达式之后，此变量可用作函数。
var x = function(a, b) {return a * b};
var z = x(3,4)

//借助于Function() 的内建 JavaScript 函数构造器来定义
var myFunction = new Function("a", "b", "return a * b");
var x = myFunction(4, 3);

document.getElementById("demo").innerHTML = toCelsius(77);


// call()方法是预定义的JavaScript方法，它可以用来调用所有者对象作为参数的方法。
var person = {
    fullName: function() {
        return this.firstName + " " + this.lastName;
    }
}
var person1 = {
    firstName:"Bill",
    lastName: "Gates",
}

person.fullName.call(person1);  // 将返回 "Bill Gates"

// 带参数的call()方法
var person = {
  fullName: function(city, country) {
    return this.firstName + " " + this.lastName + "," + city + "," + country;
  }
}
var person1 = {
  firstName:"Bill",
  lastName: "Gates"
}
person.fullName.call(person1, "Seattle", "USA");// call() 方法分别接受参数
person.fullName.apply(person1, ["Oslo", "Norway"]); // apply() 方法接受数组形式的参数

```

- **闭包**

  js变量属于本地或者全局作用域。全局变量能够通过闭包实现局部私有。全局变量：函数能够访问函数内部定义的所有变量。闭包: 闭包常常用来间接访问一个变量，换句话说隐藏一个变量(还剩几条命，可用一个全局变量，万一不小心改为-1，我们不能让别人直接访问这个变量，用局部变量。局部变量别人访问不到？暴露一个访问器函数，让别人可以间接访问)。

```javascript
function foo(){
  var local = 1
  function bar(){
    local++
    return local
  }
  return bar // 让外面可以访问到这个 bar 函数
}

var func = foo()
func()

// local变量和bar函数就组成了一个闭包Closure
```



## 2.3 js对象

真实生活中，汽车是一个对象。汽车拥有车重、颜色等属性。有启动、停止等方法。所有汽车均拥有同样的属性，但属性值因车而异。所有汽车都拥有相同的方法，但是方法会在不同时间被执行。

```javascript
// 汽车
// 属性
car.name = porsche
car.model = 911
car.length = 4499mm
car.color = white
// 方法
car.start()
car.drive()
car.brake()
car.stop()

var person = {
  firstName: "Bill",
  lastName : "Gates",
  id       : 678,
  fullName : function() {
    return this.firstName + " " + this.lastName;
  }
}; // this关键字，this 引用该函数的“拥有者”。本例中this 指的是“拥有” fullName 函数的 person 对象

person.lastName; // 访问对象属性  objectName.propertyName 或 objectName["propertyName"]
person["lastName"];

name = person.fullName(); // 访问对象方法	objectName.methodName()
name = person.fullName; // 不使用 () 访问 fullName 方法，则将返回函数定义。

// 请不要把字符串、数值和布尔值声明为对象！
var x = new String();	// 把 x 声明为 String 对象
var y = new Number();   // 把 y 声明为 Number 对象
var z = new Boolean();  // 把 z 声明为 Boolean 对象

var person = {
  firstName: "Bill",
  lastName : "Gates",
  language : "EN" 
};

// 更改属性
Object.defineProperty(person, "language", {value : "ZH"});
```

## **2.4 js字符串**

转义字符： \' 单引号  \" 双引号  \\ 反斜杠

```javascript
var x = 'It\'s good to see you again';
var answer = "It's good to see you again!";
var answer = "He is called 'Bill'";
var answer = 'He is called "Bill"';
var txt = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";

var y = new String("Bill"); // typeof y 将返回 object
var x = "Bill"; // typeof x 将返回 string
var sln = txt.length; // 返回字符串长度

var str = "The full name of China is the People's Republic of China.";
var pos = str.indexOf("China"); // 返回字符串中指定文本首次出现的索引（位置）。未找到，返回-1。
var pos = str.lastIndexOf("China"); // 返回指定文本在字符串中最后一次出现的索引。未找到，返回 -1。
var pos = str.search("locate"); // 搜索特定值的字符串，并返回匹配的位置。

var str = "Apple, Banana, Mango"; // 提取字符串
var res = str.slice(7,13); // Banana
var res = str.slice(-13,-7); // Banana
var res = str.substring(7,13); // Banana
var res = str.substr(7,6); // Banana

str = "Please visit Microsoft and Microsoft!"; // 替换字符串
var n = str.replace("Microsoft", "W3School");  // replace() 对大小写敏感
var n = str.replace(/MICROSOFT/i, "W3School"); // 执行大小写不敏感的替换，请使用正则表达式 /i
str = "Please visit Microsoft and Microsoft!";
var n = str.replace(/Microsoft/g, "W3School"); // 替换所有匹配，正则表达式的g标志（用于全局搜索）
var patt = /w3school/i; // i是修饰符（把搜索改为大小写不敏感）;w3school是模式（在搜索中使用）

var text1 = "Hello World!";       // 字符串 ， toLowerCase() 把字符串转换为小写
var text2 = text1.toUpperCase();  // text2 是被转换为大写的 text1
var text = "Hello" + " " + "World!";
var text = "Hello".concat(" ","World!"); // 连接字符串
var str = "HELLO WORLD";
str.charAt(0);            // 返回 H
str[0];                   // 返回 H
str.charCodeAt(0);         // 返回 72
var txt = "a,b,c,d,e";   // 字符串
txt.split(",");          // 用逗号分隔
txt.split(" ");          // 用空格分隔
txt.split("|");          // 用竖线分隔
```

## **2.5 js数组**

```js
var cars = ["Saab", "Volvo", "BMW"]; // 出于简洁、可读性和执行速度的考虑,无需使用 new Array()。
var cars = new Array("Saab", "Volvo", "BMW");

var firstName = cars[0]; // 访问数组的第一个元素
var lastName = cars[cars.length-1] // 访问最后一个元素
cars[0] = "Opel"; // 修改数组的首个元素

var cars = ["Saab", "Volvo", "BMW"];
document.getElementById("demo").innerHTML = cars;  // 访问完整数组

//数组的属性和方法
var x = cars.length;   // length 属性返回元素的数量
var y = cars.sort();   // sort() 方法对数组进行排序

var fruits = ["Banana", "Orange", "Apple", "Mango"];
var x = fruits.pop();      // 从数组中删除最后一个元素，x 的值是 "Mango"
var x =  fruits.push("Kiwi");   //  从数组中删除最后一个元素， x 的值是 5
fruits.push("Lemon");                // 向 fruits 添加一个新元素 (Lemon)
fruits[fruits.length] = "Lemon";     // 向 fruits 添加一个新元素 (Lemon)
fruits.unshift("Lemon");    // 向 fruits 添加新元素 "Lemon", （在开头）向数组添加新元素，并“反向位移”旧元素。
var myGirls = ["Cecilie", "Lone"];
var myBoys = ["Emil", "Tobias", "Linus"];
var myChildren = myGirls.concat(myBoys);   // 连接 myGirls 和 myBoys
fruits.sort();            // 对 fruits 中的元素进行排序
fruits.reverse();         // 反转元素顺序

typeof fruits;             // 返回 object   判断是否为数组
Array.isArray(fruits);     // 返回 true
fruits instanceof Array     // 返回 true
document.getElementById("demo").innerHTML = fruits.toString();//Banana,Orange,Apple,Mango
document.getElementById("demo").innerHTML = fruits.join(" * "); //Banana * Orange * Apple * Mango
```

## **2.6 js数学**

```javascript
Math.PI;            // 返回 3.141592653589793
Math.round(2.3);    // 返回 2，返回四舍五入接近的整数
Math.pow(8, 2); // 返回 64， Math.pow(x, y) 的返回值是 x 的 y 次幂
Math.sqrt(64);      // 返回 8，Math.sqrt(x) 返回 x 的平方根
Math.abs(-4.7);     // 返回 4.7，Math.abs(x) 返回 x 的绝对（正）值
Math.ceil(6.4);     // 返回 7， Math.ceil(x) 的返回值是 x 上舍入最接近的整数
Math.floor(2.7);    // 返回 2，Math.floor(x) 的返回值是 x 下舍入最接近的整数
Math.min(0, 450, 35, 10, -8, -300, -78);  // 返回 -300，Math.min() 和 Math.max() 可用于查找参数列表中的最低或最高值。
Math.random();     // 返回小于 1 的数
Math.floor(Math.random() * 10);		// 返回 0 至 9 之间的数

```

## **2.7 js条件，switch，for，while**

```javascript
// if条件
if (time < 10) {
    greeting = "Good morning";
 } else if (time < 18) {
    greeting = "Good day";
 } else {
    greeting = "Good evening";
 } 

// switch
switch (new Date().getDay()) {
    case 6:
        text = "今天是周六";
        break; 
    case 0:
        text = "今天是周日";
        break; 
    default: 
        text = "期待周末~";
} 

// for
for (i = 0; i < cars.length; i++) { 
    text += cars[i] + "<br>";
 }

var i = 2;  // 可以省略语句 1（比如在循环开始前设置好值）
var len = cars.length;
var text = "";
for (; i < len; i++) { 
    text += cars[i] + "<br>";
}

// for in
var person = {fname:"Bill", lname:"Gates", age:62}; 
var text = "";
var x;
for (x in person) {
    text += person[x];
}


// while 
while (i < 10) {
    text += "数字是 " + i;
    i++;
}

// do while
do {
    text += "The number is " + i;
    i++;
 }
while (i < 10);
```



## **2.7 js事件**

借助于JavaScript代码，html允许您向html元素添加事件处理程序。

<element event='一些 JavaScript'>
<element event="一些 JavaScript">

```html
<!-- JavaScript 代码改变了 id="demo" 的元素的内容 -->
<button onclick='document.getElementById("demo").innerHTML=Date()'>现在的时间是？</button>

```

## 2.8 js异常

try语句使您能够测试代码块中的错误。

catch语句允许您处理错误。

throw语句允许您创建自定义错误。

finally语句使您能够执行代码，在try和catch之后，无论结果如何。

```javascript
try {
     供测试的代码块
}
catch(err) {
     处理错误的代码块
} 
finally {
     无论 try / catch 结果如何都执行的代码块
}
```

## 2.9 js JSON

```javascript
//json 实例
{
"employees":[
    {"firstName":"Bill", "lastName":"Gates"}, 
    {"firstName":"Steve", "lastName":"Jobs"},
    {"firstName":"Alan", "lastName":"Turing"}
]
}

// 数据是名称/值对
"firstName":"Bill"  

// JSON 对象是在花括号内书写的
{"firstName":"Bill", "lastName":"Gates"}  

// json数组
"employees":[
    {"firstName":"Bill", "lastName":"Gates"}, 
    {"firstName":"Steve", "lastName":"Jobs"}, 
    {"firstName":"Alan", "lastName":"Turing"}
]

var obj = JSON.parse(text); // 使用js的内建函数JSON.parse() 来把这个字符串转换为 JavaScript 对象
```

- **js json相关函数用法**

  ```javascript
  var myJSON = '{ "name":"Bill Gates",  "age":62, "city":"Seattle" }';
  var myObj =  JSON.parse(myJSON);
  document.getElementById("demo").innerHTML = myObj.name;
  
  //存储数据：
  myObj = { name:"Bill Gates",  age:62, city:"Seattle" };
  myJSON =  JSON.stringify(myObj);
  localStorage.setItem("testJSON", myJSON);
  
  //接收数据：
  text = localStorage.getItem("testJSON");
  obj =  JSON.parse(text);
  document.getElementById("demo").innerHTML = obj.name;
  ```

- **json和xml实例**

  [json解析](https://www.w3school.com.cn/js/js_json_parse.asp)

  相同点：JSON 和 XML 都是自描述的；都是分级的（值有中值）；都能被大量编程语言解析和使用；都能被XMLHttpRequest 读取。

  区别：json不使用标签；json更短；json的读写速度更快；json可使用数组。

  ```json
  {"employees":[
      { "firstName":"Bill", "lastName":"Gates" },
      { "firstName":"Steve", "lastName":"Jobs" },
      { "firstName":"Elon", "lastName":"Musk" }
  ]}
  
  // 读取json字符串; JSON.Parse JSON 字符串
  ```

  ```xml
  <employees>
      <employee>
           <firstName>Bill</firstName>
           <lastName>Gates</lastName>
       </employee>
       <employee>
           <firstName>Steve</firstName>
           <lastName>Jobs</lastName>
       </employee>
       <employee>
           <firstName>Elon</firstName>
           <lastName>Musk</lastName>
       </employee>
  </employees>
  
  // 读取xml文档；使用 XML DOM 遍历文档；提取变量中存储的值
  ```

  

- 

# 3 JavaScript调试器

 F12 键启动浏览器中的调试器或，然后在调试器菜单中选择“控制台”。或着（右键—检查—console)。**主流浏览器调试工具：在浏览器中通过 F12 键启用调试，并在调试器菜单中选择“控制台”。**

- **Chrome**

  1 打开浏览器 2 从菜单中选择工具 3 从工具中选择开发者工具 4 最后，选择控制台。

- **FireFox Firebug**

  1 打开浏览器 2 前往网页：http://www.getfirebug.com 3 根据如下指令：如何安装 Firebug

- **Internet Explorer**

  1 打开浏览器 2 从菜单选择工具 3 从工具选择开发者工具 4 最后选择控制台

- **Opera**

  1 打开浏览器 2 请前往网页：http://dev.opera.com 3 根据如下指令：如何安装 Firebug Lite

- **Safari Develop Menu**

  1 点击 Safari 菜单，偏好设置，高级 2 选中“在菜单栏中启用开发菜单” 3 当菜单中出现新选项“开发”时，选择“显示错误控制台”

  

# 4 JS Html DOM

DOM ：全称是Document Object Model(文档对象模型)，提供给js用来动态修改文档状态。**HTML DOM 是关于如何获取、更改、添加或删除 HTML 元素的标准**。

```html
<html>
<body>

<p id="demo"></p>

<script>
document.getElementById("demo").innerHTML = "Hello World!"; 
</script>

</body>
</html>

<!-- getElementById是方法，而innerHTML是属性 --> 

document.getElementById(id) <!-- 通过元素id查找元素 -->
document.getElementsByTagName(name) <!-- 通过标签名来查找元素 -->
document.getElementsByClassName(name) <!-- 通过类名来查找元素 -->
var x = document.querySelectorAll("p.intro"); <!-- 通过css选择器查找html元素，返回 class="intro" 的所有 <p> 元素列表 -->

element.innerHTML = new html content <!-- 改变元素的 inner HTML -->
document.createElement(element) <!-- 创建HTML元素 -->
document.getElementById(id).onclick = function(){code} <!-- 向onclick事件添加事件处理程序 -->

document.getElementById("p2").style.color = "blue"; <!-- 改变html的样式 -->

```

- 使用事件

```html
<!DOCTYPE html>
<html>
<body>

<h1 id="id1">我的标题 1</h1>

<button type="button" onclick="document.getElementById('id1').style.color = 'red'">
点击我！
</button>

</body>
</html>
```

  

# 5 js Browser BOM

BOM（Browser Object Model）： 浏览器对象模型。允许js与浏览器对话。BOM 的核心就是 window 对象。

window 是浏览器内置的一个对象，里面包含着操作浏览器的方法。

可以操作：获取一些浏览器的相关信息；操作浏览器进行页面跳转；获取当前浏览器地址栏的信息；操作浏览器的滚动条；浏览器的版本；让浏览器出现一个弹出框。

```html
var w = window.innerWidth  <!-- 窗口 -->
var h = window.innerHeight
screen.width  <!-- 屏幕宽度 -->
screen.height
screen.pixelDepth <!-- Window Screen 像素深度 -->
window.location.href <!-- 返回当前页面的 href (URL) -->
window.location.hostname <!-- 返回（当前页面的）因特网主机的名称 -->
window.history.back()  <!-- 加载历史列表中前一个 URL -->
navigator.appName <!-- 返回浏览器的应用程序名称 -->
navigator.appCodeName <!-- 返回浏览器的应用程序代码名称 -->
navigator.product <!-- 返回浏览器引擎的产品名称 -->
alert("我是一个警告框！"); <!-- 弹出框：警告框 -->
var r = confirm("请按按钮"); <!-- window.confirm() 方法可以不带 window 前缀来编写 -->
var person = prompt("请输入您的姓名", "比尔盖茨"); <!-- 弹出框：提示框 -->
<button onclick="setTimeout(myFunction, 3000)">试一试</button>  <!-- 设置超时时间 -->

<!-- js cookie让您在网页中存储用户信息 -->
document.cookie = "username=Bill Gates";  <!-- 通过js创建cookie -->
<!-- 通过js创建cookie，并添加有效期，通过path参数，您可以告诉浏览器 cookie 属于什么路径 -->
document.cookie = "username=Bill Gates; expires=Sun, 31 Dec 2017 12:00:00 UTC; path=/"; 
var x = document.cookie; <!-- 通过js读取cookie -->
document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;"; <!-- 删除cookie，直接把 expires 参数设置为过去的日期即可 -->
```

# 6 js AJAX

**Ajax(Asynchronous JavaScript and XML) 异步JavaScript和XML。**客户端和服务器可以在不必刷新整个浏览器的情况下，与服务器进行异步通讯的技术。**Ajax就是能够做到局部刷新**！

[AJAX入门这一篇就够了](https://zhuanlan.zhihu.com/p/33809782)