---
title: "Typescript"
description: 
date: 2025-03-06 10:00:00 +0800
categories: [WEB]
tags: [WEB, Typescript]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

[# TypeScript中文网](https://www.tslang.cn/)

TypeScript是JavaScript类型的超集，它可以编译成纯JavaScript。

TypeScript可以在任何浏览器、任何计算机和任何操作系统上运行，并且是开源的。

## Tips
setTimeout会在若干毫秒的延时后执行一个函数（等待其它代码执行完毕）。




## 基本类型

**字符串**  
模版字符串，它可以定义多行文本和内嵌表达式。 这种字符串是被反引号包围（ `），并且以${ expr }这种形式嵌入表达式
```ts
let name: string = `Gene`;
let age: number = 37;
let sentence: string = `Hello, my name is ${ name }.

I'll be ${ age + 1 } years old next month.`;
```
这与下面定义sentence的方式效果相同：
```ts
let sentence: string = "Hello, my name is " + name + ".\n\n" +
    "I'll be " + (age + 1) + " years old next month.";
```

**数组**  
TypeScript像JavaScript一样可以操作数组元素。 有两种方式可以定义数组。 第一种，可以在元素类型后面接上 []，表示由此类型元素组成的一个数组；第二种方式是使用数组泛型，Array<元素类型>。
```ts
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];
```

**元组 Tuple**  
元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。 比如，你可以定义一对值分别为 string和number类型的元组。
```ts
// Declare a tuple type
let x: [string, number];
// Initialize it
x = ['hello', 10]; // OK
// Initialize it incorrectly
x = [10, 'hello']; // Error
```

**枚举**  
enum类型是对JavaScript标准数据类型的一个补充。 像C#等其它语言一样，使用枚举类型可以为一组数值赋予友好的名字。
```ts
enum Color {Red = 1, Green, Blue}
let colorName: string = Color[2];

console.log(colorName);  // 显示'Green'因为上面代码里它的值是2
```

**Any**  
有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型。 这些值可能来自于动态的内容，比如来自用户输入或第三方代码库。 这种情况下，我们不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。 那么我们可以使用 any类型来标记这些变量

**Void**  
某种程度上来说，void类型像是与any类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 void
```ts
function warnUser(): void {
    console.log("This is my warning message");
}
```

**Object**  
object表示非原始类型，也就是除number，string，boolean，symbol，null或undefined之外的类型。

**类型断言**  
有时候你会遇到这样的情况，你会比TypeScript更了解某个值的详细信息。 通常这会发生在你清楚地知道一个实体具有比它现有类型更确切的类型。

通过类型断言这种方式可以告诉编译器，“相信我，我知道自己在干什么”。 类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。TypeScript会假设你已经进行了必须的检查。
```ts
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
```

**关于let**  
你可能已经注意到了，我们使用let关键字来代替大家所熟悉的JavaScript关键字var。 let关键字是JavaScript的一个新概念，TypeScript实现了它。 我们会在以后详细介绍它，很多常见的问题都可以通过使用 let来解决，所以**尽可能地使用let来代替var吧**。

## 变量声明
let和const是JavaScript里相对较新的变量声明方式。 像我们之前提到过的， let在很多方面与var是相似的，但是可以帮助大家避免在JavaScript里常见一些问题。 const是对let的一个增强，它能阻止对一个变量再次赋值。

**var 声明**  
对于熟悉其它语言的人来说，var声明有些奇怪的作用域规则:
var声明可以在包含它的函数，模块，命名空间或全局作用域内部任何位置被访问，包含它的代码块对此没有什么影响/限制。 有些人称此为var作用域或函数作用域。 函数参数也使用函数作用域。

这些作用域规则可能会引发一些错误。 其中之一就是，多次声明同一个变量并不会报错。

**let 声明**  
现在你已经知道了var存在一些问题，这恰好说明了为什么用let语句来声明变量。 

当用let声明一个变量，它使用的是词法作用域或块作用域。 不同于使用 var声明的变量那样可以在包含它们的函数外访问，块作用域变量在包含它们的块或for循环之外是不能访问的。

拥有块级作用域的变量的另一个特点是，它们不能在被声明之前读或写。虽然这些变量始终“存在”于它们的作用域里，但在直到声明它的代码之前的区域都属于 暂时性死区。 它只是用来说明我们不能在 let语句之前访问它们。

注意一点，我们仍然可以在一个拥有块作用域变量被声明前获取它。 只是我们不能在变量声明前去调用那个函数。
```ts
function foo() {
    // okay to capture 'a'
    return a;
}

// 不能在'a'被声明前调用'foo'
// 运行时应该抛出错误
foo();

let a;
```

var声明时，它不在乎你声明多少次；你只会得到1个。而let声明要求块级作用域变量需要在明显不同的块里声明，在一个嵌套作用域内用let重复声明一个同名变量会屏蔽上一个作用域的变量而视为一个新变量。

当let声明出现在循环体里时拥有完全不同的行为。 不仅是在循环里引入了一个新的变量环境，而是针对 每次迭代都会创建这样一个新作用域。 这就是我们在使用立即执行的函数表达式时做的事。

**const 声明**  
const 声明是声明变量的另一种方式。它们与let声明相似，但是就像它的名字所表达的，它们被赋值后不能再改变。 换句话说，它们拥有与 let相同的作用域规则，但是不能对它们重新赋值。除非你使用特殊的方法去避免，实际上const变量的内部状态是可修改的。
```ts
const numLivesForCat = 9;
const kitty = {
    name: "Aurora",
    numLives: numLivesForCat,
}

// Error
kitty = {
    name: "Danielle",
    numLives: numLivesForCat
};

// all "okay"
kitty.name = "Rory";
kitty.name = "Kitty";
kitty.name = "Cat";
kitty.numLives--;
```

**解构数组**  
```ts
let input = [1, 2];
let [first, second] = input;
console.log(first); // outputs 1
console.log(second); // outputs 2
```
这创建了2个命名变量 first 和 second。 相当于使用了索引，但更为方便
```ts
first = input[0];
second = input[1];
```
解构作用于已声明的变量会更好：
```ts
// swap variables
[first, second] = [second, first];
```
作用于函数参数：
```ts
function f([first, second]: [number, number]) {
    console.log(first);
    console.log(second);
}
f(input);
```
你可以在数组里使用...语法创建剩余变量：
```ts
let [first, ...rest] = [1, 2, 3, 4];
console.log(first); // outputs 1
console.log(rest); // outputs [ 2, 3, 4 ]
```
当然，由于是JavaScript, 你可以忽略你不关心的尾随元素或其它元素：
```ts
let [first] = [1, 2, 3, 4];
console.log(first); // outputs 1
let [, second, , fourth] = [1, 2, 3, 4];
```

## 类


## 函数
和JavaScript一样，TypeScript函数可以创建有名字的函数和匿名函数。 你可以随意选择适合应用程序的方式，不论是定义一系列API函数还是只使用一次的函数。
```ts
// Named function
function add(x, y) {
    return x + y;
}

// Anonymous function
let myAdd = function(x, y) { return x + y; };
```

函数定义方式举例：
```js
function function_name()
{
    // 执行代码
}

// 带返回值
function function_name():return_type { 
    // 语句
    return value; 
}

// 带参数  []含义为类型声明可选？
function func_name( param1 [:datatype], param2 [:datatype]) 
{   
    // 执行代码
}

// 带参数和返回值
function func_name( param1 :datatype, param2 :datatype):return_type 
{   
    // 执行代码
}

// 带可选参数
function func_name( param1: datatype, param2?: datatype) 
{   
    // 执行代码
}

// 带默认参数
function func_name( param1: datatype, param2:number = 0.5) 
{   
    // 执行代码
}

// 匿名函数 
// '()'前省略函数名，其余无区别
var res = function ( param1 :datatype, param2 :datatype):return_type 
{ 
    // 语句
    return value; 
}
```


**Lambda 函数**  
Lambda 函数也称之为箭头函数。

箭头函数表达式的语法比函数表达式更短。

1.函数只有一行语句：

```ts
( [param1, param2, param n] )=>statement;
```

传入param1等参数，返回statement的结果。

以下实例声明了 lambda 表达式函数，函数返回两个数的和：
```ts
var foo = (x:number)=>10 + x 
console.log(foo(100))      //输出结果为 110
```
编译以上代码，得到以下 JavaScript 代码：
```js
var foo = function (x:number) { return 10 + x; };
console.log(foo(100)); //输出结果为 110
```

2.函数是一个语句块：  

```ts
( [param1, param2, param n] )=> {
    // 代码块
}
```
传入param1等参数，执行代码块。


以下实例声明了 lambda 表达式函数，函数返回两个数的和：
```ts
var foo = (x:number)=> {    
    x = 10 + x 
    console.log(x)  
} 
foo(100)
```
编译以上代码，得到以下 JavaScript 代码：
```js
var foo = function (x:number) {
    x = 10 + x;
    console.log(x);
};
foo(100);
```

**函数类型**  
我们可以给每个参数添加类型之后再为函数本身添加返回值类型。 TypeScript能够根据返回语句自动推断出返回值类型，因此我们通常省略它。
```ts
function add(x: number, y: number): number {
    return x + y;
}

let myAdd = function(x: number, y: number): number { return x + y; };
```
函数类型包含两部分：参数类型和返回值类型。 

对于返回值，我们在函数和返回值类型之前使用( =>)符号，使之清晰明了。 如之前提到的，返回值类型是函数类型的必要部分，如果函数没有返回任何值，你也必须指定返回值类型为 void而不能留空。

让我们写出函数的完整类型
```ts
let myAdd: (baseValue: number, increment: number) => number =
    function(x: number, y: number): number { return x + y; };
```
尝试这个例子的时候，你会发现如果你在赋值语句的一边指定了类型但是另一边没有类型的话，TypeScript编译器会自动识别出类型：
```ts
// myAdd has the full function type
let myAdd = function(x: number, y: number): number { return x + y; };

// The parameters `x` and `y` have the type number
let myAdd: (baseValue: number, increment: number) => number =
    function(x, y) { return x + y; };
```
这叫做“按上下文归类”，是类型推论的一种。 它帮助我们更好地为程序指定类型。

**可选参数和默认参数**  
TypeScript里的每个函数参数都是必须的。 JavaScript里，每个参数都是可选的，可传可不传。 没传参的时候，它的值就是undefined。 在TypeScript里我们可以在参数名旁使用 ?实现可选参数的功能。 
```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}

let result1 = buildName("Bob");  // works correctly now
let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result3 = buildName("Bob", "Adams");  // ah, just right
```
在TypeScript里，我们也可以为参数提供一个默认值当用户没有传递这个参数或传递的值是undefined时。 它们叫做有默认初始化值的参数。
```ts
function buildName(firstName: string, lastName = "Smith") {
    return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // works correctly now, returns "Bob Smith"
let result2 = buildName("Bob", undefined);       // still works, also returns "Bob Smith"
let result3 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result4 = buildName("Bob", "Adams");         // ah, just right
```
与普通可选参数不同的是，带默认值的参数不需要放在必须参数的后面。 如果带默认值的参数出现在必须参数前面，用户必须明确的传入 undefined值来获得默认值。

**剩余参数**  
必要参数，默认参数和可选参数有个共同点：它们表示某一个参数。 有时，你想同时操作多个参数，或者你并不知道会有多少参数传递进来。 在JavaScript里，你可以使用 arguments来访问所有传入的参数。

在TypeScript里，你可以把所有参数收集到一个变量里：
```ts
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```
剩余参数会被当做个数不限的可选参数。 可以一个都没有，同样也可以有任意个。 编译器创建参数数组，名字是你在省略号（ ...）后面给定的名字，你可以在函数体内使用这个数组。

**重载**  
JavaScript本身是个动态语言。 JavaScript里函数根据传入不同的参数而返回不同类型的数据是很常见的。 但是这怎么在类型系统里表示呢？

方法是为同一个函数提供多个函数类型定义来进行函数重载。 编译器会根据这个列表去处理函数的调用。 为了让编译器能够选择正确的检查类型，它与JavaScript里的处理流程相似。 它查找重载列表，尝试使用第一个重载定义。 如果匹配的话就使用这个。 因此，在定义重载的时候，一定要把最精确的定义放在最前面。

## 枚举

**数字枚举**  
首先我们看看数字枚举，如果你使用过其它编程语言应该会很熟悉。
```ts
enum Direction {
    Up = 1,
    Down,
    Left,
    Right
}
```

使用枚举很简单：通过枚举的属性`Direction.Down`来访问枚举成员，和枚举的名字`Direction`来访问枚举类型：

**字符串枚举**  
在一个字符串枚举里，每个成员都必须用字符串字面量，或另外一个字符串枚举成员进行初始化。
```ts
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}
```

## 类型推论
TypeScript里，在有些没有明确指出类型的地方，类型推论会帮助提供类型。
```ts
let x = 3;
```
变量x的类型被推断为数字。 这种推断发生在初始化变量和成员，设置默认参数值和决定函数返回值时。


## interface 接口
接口是一系列抽象方法的声明，是一些方法特征的集合，这些方法都应该是抽象的，需要由具体的类去实现，然后第三方就可以通过这组抽象方法调用，让具体的类执行具体的方法。

以下实例中，我们定义了一个接口 IPerson，接着定义了一个变量 customer，它的类型是 IPerson。  
customer 实现了接口 IPerson 的属性和方法。
```ts
interface IPerson { 
    firstName:string, 
    lastName:string, 
    sayHi: ()=>string 
} 
 
var customer:IPerson = { 
    firstName:"Tom",
    lastName:"Hanks", 
    sayHi: ():string =>{return "Hi there"} 
} 
 
console.log("Customer 对象 ") 
console.log(customer.firstName) 
console.log(customer.lastName) 
console.log(customer.sayHi())  
 
var employee:IPerson = { 
    firstName:"Jim",
    lastName:"Blakes", 
    sayHi: ():string =>{return "Hello!!!"} 
} 
 
console.log("Employee  对象 ") 
console.log(employee.firstName) 
console.log(employee.lastName)
```
**需要注意接口不能转换为 JavaScript。 它只是 TypeScript 的一部分。**

编译以上代码，得到以下 JavaScript 代码：
```js
var customer = {
    firstName: "Tom",
    lastName: "Hanks",
    sayHi: function () { return "Hi there"; }
};
console.log("Customer 对象 ");
console.log(customer.firstName);
console.log(customer.lastName);
console.log(customer.sayHi());
var employee = {
    firstName: "Jim",
    lastName: "Blakes",
    sayHi: function () { return "Hello!!!"; }
};
console.log("Employee  对象 ");
console.log(employee.firstName);
console.log(employee.lastName);
```

## 泛型
1.泛型函数（Generic Functions）  
使用泛型来创建一个可以处理不同类型的函数：

```js
function identity<T>(arg: T): T {
    return arg;
}

// 使用泛型函数
let result = identity<string>("Hello");
console.log(result); // 输出: Hello

let numberResult = identity<number>(42);
console.log(numberResult); // 输出: 42
```
解析： 以上例子中，identity 是一个泛型函数，使用 `<T>` 表示泛型类型。它接受一个参数 arg 和返回值都是泛型类型 T。在使用时，可以通过尖括号 <> 明确指定泛型类型。第一个调用指定了 string 类型，第二个调用指定了 number 类型。

2.泛型接口（Generic Interfaces）
可以使用泛型来定义接口，使接口的成员能够使用任意类型：

```ts
// 基本语法
interface Pair<T, U> {
    first: T;
    second: U;
}

// 使用泛型接口
let pair: Pair<string, number> = { first: "hello", second: 42 };
console.log(pair); // 输出: { first: 'hello', second: 42 }
```
解析： 这里定义了一个泛型接口 Pair，它有两个类型参数 T 和 U。然后，使用这个泛型接口创建了一个对象 pair，其中 first 是字符串类型，second 是数字类型。

3.泛型类（Generic Classes）
