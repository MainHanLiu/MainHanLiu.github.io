---
title: "TypeScript 拾慧"
description: 
date: 2025-03-22 10:00:00 +0800
categories: [WEB]
tags: [WEB, Typescript]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## instanceof 运算符
instanceof 运算符用于判断对象是否是指定的类型，如果是返回 true，否则返回 false。
```ts
class Person{ } 
var obj = new Person() 
var isPerson = obj instanceof Person; 
console.log("obj 对象是 Person 类实例化来的吗？ " + isPerson);
```

## any类型
如果数组声明时未设置类型，则会被认为是 any 类型，在初始化时根据第一个元素的类型来推断数组的类型。
```ts
var sites;
sites = ["Google", "Runoob", "Taobao"];
console.log(sites[0]);
console.log(sites[1]);
```

## 元组
我们知道数组中元素的数据类型都一般是相同的（any[] 类型的数组可以不同），如果存储的元素数据类型不同，则需要使用元组。

TypeScript 中的元组（Tuple）是一种特殊类型的数组，它允许在数组中存储不同类型的元素，与普通数组不同，元组中的每个元素都有明确的类型和位置。元组可以在很多场景下用于表示**固定长度、且各元素类型已知**的数据结构。

push 方法可以向元组的末尾添加一个元素，类型必须符合元组定义中的类型约束。如果超出元组的类型约束，TypeScript 会报错。
```ts
var tuple = [42, "Hello"];
// 添加符合类型的元素
tuple.push("World"); // 合法，因为元组定义了可选的 string 类型
console.log(tuple); // 输出: [42, "Hello", "World"]
```

元组常用于函数返回多个值的场景，或者表示某些固定结构的数据，比如：
```ts
function getUserInfo(): [number, string] {
    return [1, "John Doe"];
}

const [userId, userName] = getUserInfo(); // 解构
console.log(userId);   // 输出: 1
console.log(userName); // 输出: John Doe
```

## 接口
接口是一系列抽象方法的声明，是一些方法特征的集合，这些方法都应该是抽象的，需要由具体的类去实现，然后第三方就可以通过这组抽象方法调用，让具体的类执行具体的方法。
>和类的区别？  
>接口可以被类和接口继承，而类只能单继承。接口的成员只能是方法、属性、索引器、事件，而类可以包含方法、属性、索引器、事件、**构造函数、析构函数**等成员。


## 继承
TypeScript 支持继承类，即我们可以在创建类的时候继承一个已存在的类，这个已存在的类称为父类，继承它的类称为子类。

类继承使用关键字 extends，子类除了不能继承父类的私有成员(方法和属性)和构造函数，其他的都可以继承。

TypeScript 一次只能继承一个类，不支持继承多个类，但 TypeScript 支持多重继承（A 继承 B，B 继承 C）。


## 对象
TypeScript 对象：
对象是包含一组键值对的实例。 值可以是标量、函数、数组、对象等，如下实例：
```ts
var object_name = { 
    key1: "value1", // 标量
    key2: "value",  
    key3: function() {
        // 函数
    }, 
    key4:["content1", "content2"] //集合
}
```

## 泛型  
泛型（Generics）是一种编程语言特性，允许在定义函数、类、接口等时使用占位符来表示类型，而不是具体的类型。

泛型是一种在编写可重用、灵活且类型安全的代码时非常有用的功能。

使用泛型的主要目的是为了处理不特定类型的数据，使得代码可以适用于多种数据类型而不失去类型检查。

## async和await
[理解异步函数async和await的用法](https://blog.csdn.net/weixin_45811256/article/details/123638582)
