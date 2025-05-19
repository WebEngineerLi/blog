---
title: webpack中的各种publicPath
date: 2025-03-01 15:50:04
tags: webpack, publicPath
---

## output中的publicPath
先看一个webpack中output的配置
```
output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, '../dist'),
    publicPath: '/' 
}
```
`publicPath`解释最多的是说访问静态资源时的路径，当我们把资源放到CDN上的时候，把 publicPath设为CDN的值就行了，这种使用方法适用于生产环境。  
`publicPath`在开发环境下指的是通过`html-webpack-plugin`生成的index.html文件中引入的output.filename的时候要不要加上刚才配置的`publicPath`。即${output.publicPath}/${output.filename}。如刚才配置的publicPath是“/”，那么最后的打包后的结果是。

![](https://cdn.mingyangli.com/image/publicPath1_1747660896293.jpg)

我们把publicPath换成`/assets/`, 那么最终打包后html中引入的资源的路径是。

```text
output: {
    path: __dirname + '/dist',
    filename: '[name].bundle.js',
    clean: true,
    publicPath: '/assets',
}
```
![](https://cdn.mingyangli.com/image/publicPath2_1747660938978.jpg)

所有请求的前缀都会加上 /assets/ 所以开发环境配置 output 中的 publicPath 没有任何意义

## devServer中的static.publicPath
devServer 中 static.publicPath 用于指定**静态文件（非 Webpack 打包资源）**的开发服务器访问路径

```text
{
  "devServer": {
    "static": {
      "directory": path.join(__dirname,  'public'), // 静态文件目录 
      "publicPath": "/static-resources/"
    }
  }
}
```
什么叫非 webpack 打包的资源呢，比如说我在项目根目录建立一个 public 的文件夹，代码里面没有引用这个文件夹中的任何内容。也就是该文件夹下的文件不会被 webpack 打包。但是通过 devServer 起服务之后可以通过 `localhost:port/static-resources/xxx` 访问该目录下的任何内容
![](https://cdn.mingyangli.com/image/publicPath3_1747660961440.jpg)

以上测试的版本为 webpack^5.65.0
