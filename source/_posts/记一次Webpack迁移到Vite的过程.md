---
title: 记一次Webpack迁移到Vite的过程
date: 2025-04-10 14:44:15
tags: wp2vite, webpack迁移vite, vite
---
## 背景
项目之前是自己搭建的 Webpack 脚手架，随着项目的迭代工程量也越来越大，写了一个脚本跑了一下，项目 **src** 目录下有 **1540** 个文件，这个数量还在不断增加再加上大量的 **node_modules**。Webpack 首次启动编译一遍需要 **2+  Minutes** 的时间，以至于开发期间换个 **proxy**，restart 的时候真是个痛苦的事情。且热更新的响应速度也不是很理想。所以通过不懈的努力(~~一通乱搞~~)将项目迁移到了 Vite，下面记录一下遇到的几点问题。
## 迁移神器 wp2vite
```
npm i wp2vite -g
```
`wp2vite`会自动分析Webpack的配置然后移植到在根目录创建的 `vite.config.js `文件中。   
![](https://cdn.mingyangli.com/image/1637054821559_1637054840431.jpg)
但是移植到 vite.config.js 只是基础的配置，很多还需要自己手动更改
## 开发代理(proxy)
**vite** 与 **webpack** 在代理方面**重写路径**的写法是不一样的，**vite** 用的是 [rewrite](https://cn.vitejs.dev/config/#server-proxy) 函数，而 **webpack** 是 [pathWrite](https://webpack.docschina.org/configuration/dev-server/#devserverproxy) 对象。
## Sass
之前 **Webpack** 中使用的是 `node-sass` 和 `sass-loader` 的组合。但是从 2020.4 开始 sass 官网开始推出了 `dart sass` 来代替 `node-sass`。

- Sass的主要实现有Ruby Sass 11和LibSass 20（node-sass底层使用的是LibSass），它们都有各自的优缺点。
  Ruby Sass的实现语言是高级语言Ruby，更容易迭代，但存在运行速度慢，不易安装的缺点。LibSass虽然速度快，但它的开发语言是C/C++，迭代速度慢，无法快速地添加各种功能。
- Dart的运行速度是真的快，对于大型样式文件，Dart Sass的处理速度是Ruby Sass的5~10倍，且只比LibSass慢1.5倍左右。
  同时，Dart是一门具备静态类型的动态语言，对比C/C++甚至是Ruby，Dart相对更容易上手且代码也更易于编写和维护。此外，Dart具备编译输出为JavaScript的能力，使得Dart Sass可以兼容NodeJS平台。
  所以 Vite 使用了 `dart sass` 作为 sass 的预处理器。但是 `dart sass` 不支持 `/deep/ `的写法，需要替换成 `::v-deep`


