---
title: "Web浏览器存储加密"
description: 
date: 2025-03-22 10:00:00 +0800
categories: [WEB]
tags: [WEB]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

## 背景
vue3使用pinia进行存储管理时，可以结合pinia-plugin-persistedstate插件进行数据持久化时，将数据保存到浏览器的本地存储localStorage和会话存储sessionStorage中，但是数据都是以明文的形式保存的，可以通过浏览器开发者工具直接进行修改，可能会有修改提升权限等风险。为了保护数据的安全性，需要对数据进行加密处理。

## Encrypt Storage
https://www.npmjs.com/package/encrypt-storage#pinia-plugin-persist  

The Encrypt Storage is a wrapper for native Storage of browser.

Using the crypto-js library as an encryption engine, it saves the encrypted data on the selected storage in the same way as the native Storage.

Features：Save encrypted data in localStorage, sessionStorage and cookies.

Encrypt Storage 支持对localStorage, sessionStorage and cookies进行加密

## 安装
```
npm install encrypt-storage
```

## 使用
Encrypt Storage支持结合pinia-plugin-persistedstate等插件使用，使用时必须设置stateManagementUse选项为true。
```
This library can be used to encrypt data from state management persisters like vuex-persist, redux-persist and pinia-plugin-persist. Below are their respective implementations:

NOTE: the stateManagementUse option must be used in the EncryptStorage instance to work correctly.
```

创建加密Storage实例，第一个参数为加密的秘钥（自定义），第二个参数为加密的配置option
```ts
import { EncryptStorage } from 'encrypt-storage'

// 加密localStorage和sessionStorage
export const localStorageEncrypt = new EncryptStorage('secret-key-value-local', {
  stateManagementUse: true,
  storageType: 'localStorage',
})
export const sessionStorageEncrypt = new EncryptStorage('secret-key-value-session', {
  stateManagementUse: true,
  storageType: 'sessionStorage',
})

```

```ts
import { defineStore } from 'pinia'

import { localStorageEncrypt, sessionStorageEncrypt } from '../encryptStorage/encryptStorage'
import type { StorageLike } from 'pinia-plugin-persistedstate'

export const useDevStore = defineStore('dev', {
  state: () => ({
    param1: '',
    param2: '',
    param3: 0xffff,
    param4: 0xffff,
  }),
  getters: {},
  actions: {},
  persist: [
    {
      pick: ['param1', 'param2'],
      storage: localStorageEncrypt as StorageLike,
    },
    {
      pick: ['param3', 'param4'],
      storage: sessionStorageEncrypt as StorageLike,
    },
  ],
})

```

## 参考
https://github.com/michelonsouza/encrypt-storage?tab=readme-ov-file#pinia-plugin-persistedstate

https://juejin.cn/post/7362196417278099471
