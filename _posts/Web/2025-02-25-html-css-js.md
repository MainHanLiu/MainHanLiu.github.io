---
title: "HTML CSS JS"
description: 
date: 2025-02-25 10:00:00 +0800
categories: [WEB]
tags: [WEB, html, css, js]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

HTML 定义了网页的内容  
CSS 描述了网页的布局  
JavaScript 控制了网页的行为  


## HTML

```html
<html>      定义 HTML 文档
<body>      定义文档的主体
<h1> - <h6> 定义 HTML 标题
<hr>        定义水平线
<p>         定义一个段落
<br>        插入单个折行（换行）
<table>     定义表格
<th>        定义表格的表头
<tr>        定义表格的行
<td>        定义表格单元
<ol>        定义有序列表
<ul>        定义无序列表
<li>        定义列表项
<div>       定义了文档的区域，块级 (block-level)
<span>      用来组合文档中的行内元素， 内联元素(inline)
<form>      定义供用户输入的表单
<input>     定义输入域
<label>     定义了 <input> 元素的标签，一般为输入标题
<select>    定义了下拉选项列表
<optgroup>  定义选项组
<option>    定义下拉列表中的选项
<button>    定义一个点击按钮
```

[HTML 速查列表](https://www.runoob.com/html/html-quicklist.html)

对于 HTML，您无法通过在 HTML 代码中添加额外的空格或换行来改变输出的效果。  
当显示页面时，浏览器会移除源代码中多余的空格和空行。所有连续的空格或空行都会被算作一个空格。需要注意的是，HTML 代码中的所有连续的空行（换行）也被显示为一个空格。

当样式需要被应用到很多页面的时候，外部样式表将是理想的选择。使用外部样式表，你就可以通过更改一个文件来改变整个站点的外观。
```HTML
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```

## CSS
如果你要在HTML元素中设置CSS样式，你需要在元素中设置"id" 和 "class"选择器。

- id 选择器
id 选择器可以为标有特定 id 的 HTML 元素指定特定的样式。  
HTML元素以id属性来设置id选择器,CSS 中 id 选择器以 "#" 来定义。  

- class 选择器
class 选择器用于描述一组元素的样式，class 选择器有别于id选择器，class可以在多个元素中使用。  
class 选择器在 HTML 中以 class 属性表示, 在 CSS 中，类选择器以一个点 `.` 号显示  


**外部样式表**  
当样式需要应用于很多页面时，外部样式表将是理想的选择。在使用外部样式表的情况下，你可以通过改变一个文件来改变整个站点的外观。每个页面使用 `<link>` 标签链接到样式表。 `<link>` 标签在（文档的）头部：
```HTML
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```
浏览器会从文件 mystyle.css 中读到样式声明，并根据它来格式文档。

不要在属性值与单位之间留有空格

## JavaScript 
JavaScript 是一种轻量级的编程语言。  
JavaScript 是可插入 HTML 页面的编程代码。  
JavaScript 插入 HTML 页面后，可由所有的现代浏览器执行。  

>- JavaScript 与 Java 是两种完全不同的语言，无论在概念上还是设计上。  
>- Java（由 Sun 发明）是更复杂的编程语言。  
>- ECMA-262 是 JavaScript 标准的官方名称。  
>- JavaScript 由 Brendan Eich 发明。它于 1995 年出现在 Netscape 中（该浏览器已停止更新），并于 1997 年被 ECMA（一个标准协会）采纳。  

使用 JavaScript 来处理 HTML 内容是非常强大的功能。

您会经常看到 `document.getElementById("some id")`。这个方法是 HTML DOM 中定义的。
>DOM (Document Object Model)（文档对象模型）是用于访问 HTML 元素的正式 W3C 标准。

Javascript 脚本代码可被放置在 HTML 页面的 `<body>` 和 `<head>` 部分中。

HTML 中的 Javascript 脚本代码必须位于 `<script>` 与 `</script>` 标签之间。也可以把脚本保存到外部文件中。外部文件通常包含被多个网页使用的代码。外部 javascript 文件不使用 `<script>` 标签，直接写 javascript 代码。
```HTML
<script src="myScript.js"></script>
```

JavaScript 可以通过不同的方式来输出数据：
- 使用 window.alert() 弹出警告框。
- 使用 document.write() 方法将内容写到 HTML 文档中。
- 使用 innerHTML 写入到 HTML 元素。
- 使用 console.log() 写入到浏览器的控制台。

---
### **JavaScript 字面量**

数字（Number）字面量 可以是整数或者是小数，或者是科学计数(e):  
`1001`  
`3.14`  
`123e5`  

表达式字面量 用于计算：  
`5 + 6`  
`5 * 10`  

字符串（String）字面量 可以使用单引号或双引号:  
`"John Doe"`  
`'John Doe'`  

数组（Array）字面量 定义一个数组：  
`[40, 100, 1, 5, 25, 10]`

对象（Object）字面量 定义一个对象：  
`{firstName:"John", lastName:"Doe", age:50, eyeColor:"blue"}`

函数（Function）字面量 定义一个函数：  
`function myFunction(a, b) { return a * b;}`

---
### **JavaScript 变量**
JavaScript 使用关键字 var 来定义变量， 使用等号来为变量赋值：
```js
var x, lengthx = 5;
length = 6;
```
>var 关键字告诉浏览器创建一个新的变量

---
### **JavaScript 数据类型**  
在 JavaScript 中有 6 种不同的数据类型：
1. string
2. number
3. boolean
4. object
5. function
6. symbol

3 种对象类型：
1. Object
2. Date
3. Array

2 个不包含任何值的数据类型：
1. null
2. undefined

你可以使用 typeof 操作符来查看 JavaScript 变量的数据类型。
```JavaScript
typeof "John"                 // 返回 string
typeof 3.14                   // 返回 number
typeof NaN                    // 返回 number
typeof false                  // 返回 boolean
typeof [1,2,3,4]              // 返回 object
typeof {name:'John', age:34}  // 返回 object
typeof new Date()             // 返回 object
typeof function () {}         // 返回 function
typeof myCar                  // 返回 undefined (如果 myCar 没有声明)
typeof null                   // 返回 object
```
在JavaScript中，数组是一种特殊的对象类型。 因此 typeof [1,2,3,4] 返回 object。
>请注意：
NaN 的数据类型是 number  
数组(Array)的数据类型是 object  
日期(Date)的数据类型为 object  
null 的数据类型是 object  
未定义变量的数据类型为 undefined  

你可以使用 typeof 操作符来检测变量的数据类型。


当 JavaScript 尝试操作一个 "错误" 的数据类型时，会自动转换为 "正确" 的数据类型。

当你尝试输出一个对象或一个变量时 JavaScript 会自动调用变量的 toString() 方法

```JavaScript
var length = 16;                                  // Number 通过数字字面量赋值
var points = x * 10;                              // Number 通过表达式字面量赋值
var lastName = "Johnson";                         // String 通过字符串字面量赋值
var cars = ["Saab", "Volvo", "BMW"];              // Array  通过数组字面量赋值
var person = {firstName:"John", lastName:"Doe"};  // Object 通过对象字面量赋值
```

>JavaScript 中，常见的是驼峰法的命名规则，如 lastName (而不是lastname)。

分号用于分隔 JavaScript 语句。通常我们在每条可执行的语句结尾添加分号。
>您也可能看到不带有分号的案例。在 JavaScript 中，用分号来结束语句是可选的。

JavaScript 会忽略多余的空格。您可以向脚本添加空格，来提高其可读性。下面的两行代码是等效的：
```JavaScript
var person="runoob";
var person = "runoob";
```

您可以在文本字符串中使用反斜杠对代码行进行换行。
```JavaScript
document.write("你好 \
世界!");
```

当您向变量分配文本值时，应该用双引号或单引号包围这个值。

当您向变量赋的值是数值时，不要使用引号。如果您用引号包围数值，该值会被作为文本来处理。

如果重新声明 JavaScript 变量，该变量的值不会丢失：

在以下两条语句执行后，变量 carname 的值依然是 "Volvo"：
```JavaScript
var carname="Volvo";
var carname;
```

在 2015 年以前，我们使用 var 关键字来声明 JavaScript 变量。

在 2015 后的 JavaScript 版本 (ES6) 允许我们使用 const 关键字来定义一个常量，使用 let 关键字定义的限定范围内作用域的变量。


JavaScript 中的所有数据都是以 64 位浮点型数据(float) 来存储。


---
### **JavaScript 对象**  

JavaScript 对象是拥有属性和方法的数据。

JavaScript 对象是变量的容器。

你可以使用字符来定义和创建 JavaScript 对象:
```JavaScript
var person = {firstName:"John", lastName:"Doe", age:50, eyeColor:"blue"};
```
定义 JavaScript 对象可以跨越多行，空格跟换行不是必须的：
```JavaScript
var person = {
    firstName:"John",
    lastName:"Doe",
    age:50,
    eyeColor:"blue"
};
```

访问对象属性:`person.lastName;`或`person["lastName"];`

对象的方法定义了一个函数，并作为对象的属性存储。对象方法通过添加 () 调用 (作为一个函数)。
```JavaScript
var person = {
    firstName: "John",
    lastName : "Doe",
    id : 5566,
    fullName : function() 
    {
       return this.firstName + " " + this.lastName;
    }
};
```
```JavaScript
person.fullName()
person.fullName
```
>加括号输出函数执行结果:`John Doe`  
>不加括号输出函数表达式代码:`function() { return this.firstName + " " + this.lastName; }`

---
### **JavaScript 函数**

函数就是包裹在花括号中的代码块，前面使用了关键词 function
```JavaScript
function functionname_1()
{
    // 执行代码
}

// 在调用函数时，您可以向其传递值：
function functionname_2(var1,var2)
{
    // 执行代码
}

// 有时，我们会希望函数将值返回调用它的地方
function myFunction()
{
    var x=5;
    return x;
}
var myVar=myFunction();

// 在您仅仅希望退出函数时 ，也可使用 return 语句。返回值是可选的
```

在 JavaScript 函数内部声明的变量（使用 var）是局部变量，所以只能在函数内部访问它。（该变量的作用域是局部的）。您可以在不同的函数中使用名称相同的局部变量，因为只有声明过该变量的函数才能识别出该变量。只要函数运行完毕，本地变量就会被删除。

在函数外声明的变量是全局变量，网页上的所有脚本和函数都能访问它。

---
### **事件**

常见的HTML事件

| 事件        | 描述                                 |
|-------------|--------------------------------------|
| onchange    | HTML 元素改变                        |
| onclick     | 用户点击 HTML 元素                   |
| onmouseover | 鼠标指针移动到指定的元素上时发生     |
| onmouseout  | 用户从一个 HTML 元素上移开鼠标时发生 |
| onkeydown   | 用户按下键盘按键                     |
| onload      | 浏览器已完成页面的加载               |

---
### **JavaScript 字符串**

可以使用索引位置来访问字符串中的每个字符。字符串的索引从 0 开始，这意味着第一个字符索引值为 [0]，第二个为 [1]，以此类推。

可以使用内置属性 length 来计算字符串的长度：
```JavaScript
var txt = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
var sln = txt.length;
```
---
JavaScript for/in 语句循环遍历对象的属性

---
### **JavaScript 正则表达式**
在 JavaScript 中，正则表达式通常用于两个字符串方法 : search() 和 replace()。

search() 方法用于检索字符串中指定的子字符串，或检索与正则表达式相匹配的子字符串，并返回子串的起始位置。
>search 方法可使用字符串作为参数。字符串参数会转换为正则表达式

replace() 方法用于在字符串中用一些字符串替换另一些字符串，或替换一个与正则表达式匹配的子串。

[# 正则表达式 - 教程](https://www.runoob.com/regexp/regexp-tutorial.html)

---

### **JavaScript 调试**

内置的调试工具可以开启或关闭，严重的错误信息会发送给用户。

有了调试工具，我们就可以设置断点 (代码停止执行的位置), 且可以在代码执行时检测变量。


console.log() 方法
浏览器启用调试工具一般是按下 F12 键，并在调试菜单中选择 "Console" 。如果浏览器支持调试，你可以使用 console.log() 方法在调试窗口上打印 JavaScript 值。

debugger 关键字用于停止执行 JavaScript，并调用调试函数。这个关键字与在调试工具中设置断点的效果是一样的。如果没有调试可用，debugger 语句将无法工作。

---
### **JavaScript 声明提升**
JavaScript 中，变量可以在使用后声明，也就是变量可以先使用再声明。
>函数声明和变量声明总是会被解释器悄悄地被"提升"到方法体的最顶部。

JavaScript 初始化不会提升，JavaScript 只有声明的变量部分会提升，初始化部分不会。

对于大多数程序员来说并不知道 JavaScript 声明提升。

如果程序员不能很好的理解声明提升，他们写的程序就容易出现一些问题。

为了避免这些问题，通常我们在每个作用域开始前声明这些变量，这也是正常的 JavaScript 解析步骤，易于我们理解。

---
### JavaScript let 和 const
ES2015(ES6) 新增加了两个重要的 JavaScript 关键字: let 和 const。

let 声明的变量只在 let 命令所在的代码块内有效。

const 声明一个只读的常量，一旦声明，常量的值就不能改变。

---
### JavaScript JSON
JSON 是用于存储和传输数据的格式。

JSON 英文全称 JavaScript Object Notation

JSON 通常用于服务端向网页传递数据 。

JSON 语法规则：
* 数据为 键/值 对
* 数据由逗号分隔
* 大括号保存对象
* 方括号保存数组
```JavaScript
"sites":[
    {"name":"Runoob", "url":"www.runoob.com"}, 
    {"name":"Google", "url":"www.google.com"},
    {"name":"Taobao", "url":"www.taobao.com"}
]
```

JSON 格式化后为 JavaScript 对象。JSON 格式在语法上与创建 JavaScript 对象代码是相同的。

由于它们很相似，所以 JavaScript 程序可以很容易的将 JSON 数据转换为 JavaScript 对象。

JSON.parse()	用于将一个 JSON 字符串转换为 JavaScript 对象。
JSON.stringify()	用于将 JavaScript 值转换为 JSON 字符串。

---
### 箭头函数
ES6 新增了箭头函数。

箭头函数表达式的语法比普通函数表达式更简洁。
```JavaScript
(参数1, 参数2, …, 参数N) => { 函数声明 }

// 相当于：(参数1, 参数2, …, 参数N) =>{ return 表达式; }
(参数1, 参数2, …, 参数N) => 表达式(单一)

// 当只有一个参数时，圆括号是可选的：
(单一参数) => {函数声明}
单一参数 => {函数声明}

// 没有参数的函数应该写成一对圆括号:
() => {函数声明}
```
