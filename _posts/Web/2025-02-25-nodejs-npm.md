---
title: "nodejs npm"
description: 
date: 2025-02-25 10:00:00 +0800
categories: [WEB]
tags: [WEB, nodejs, npm]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。 Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。 Node.js 的包管理器 npm，是全球最大的开源库生态系统。

## nodejs安装
1. 安装nodejs
2. 更换淘宝源

## npm

npm i 是 npm install 的别名。

安装单独的依赖包时，`npm install <packageName>`

* --save 等同于 -S

-S, --save 安装包信息将加入到package.json 文件dependencies（生产阶段的依赖,也就是项目运行时的依赖，就是程序上线后仍然需要依赖）

* --save-dev 等同于 -D

-D, --save-dev 安装包信息将加入到package.json 文件devDependencies（开发阶段的依赖，就是我们在开发过程中需要的依赖，只在开发阶段起作用。）

## package

### axios
https://axios-http.com/zh/docs/intro

### js-cookie

npm i js-cookie
npm i @types/js-cookie

### mock

http://mockjs.com/

npm install mockjs
npm i --save-dev @types/mockjs
