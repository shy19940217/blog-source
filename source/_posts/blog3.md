---
title: webpack成神之路
date: 2018-03-19 17:48:32
tags: webpack
---
现在的前端网页功能丰富，特别是SPA（single page web application 单页应用）技术流行后，JavaScript的复杂度增加和需要一大堆依赖包，还需要解决SCSS，Less......新增样式的扩展写法的编译工作。所以现代化的前端已经完全依赖于WebPack的辅助了。

## 什么是WebPack？

WebPack可以看做是模块打包机：它做的事情是，分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Sass，TypeScript等），并将其转换和打包为合适的格式供浏览器使用。在3.0出现后，Webpack还肩负起了优化项目的责任。

## 配置文件入口和出口
在我所经历的项目里有多页打包也有单页入口打包，无外乎就是入口不一样
```
module.exports={
    //入口文件的配置项
    entry:{},
    //出口文件的配置项
    output:{},
    //模块：例如解读CSS,图片如何转换，压缩
    module:{},
    //插件，用于生产模版和各项功能
    plugins:[],
    //配置webpack开发服务功能
    devServer:{}
}
```
## 入口配置
```
//入口文件的配置项
entry:{
    //里面的entery是可以随便写的
    entry:'./src/entry.js'
}
```
## output选项（出口配置）
```
//出口文件的配置项
output:{
    //打包的路径文职
    path:path.resolve(__dirname,'dist'),
    //打包的文件名称
    filename:'bundle.js'
    
}
其实path.resolve(__dirname,'dist')就是获取了项目的绝对路径
filename:是打包后的文件名称，这里我们起名为bundle.js
```
## 多入口、多出口配置
Webpack在版本1的时候很难设置多出口文件，但是在2版本开始就变的很方便了。直接看多入口和多出口的文件配置，然后可以和单一出口对比一下，你会发现这种设置非常简单（只需改动两点配置就可以）。
```
const path = require('path');
module.exports={
    //入口文件的配置项
    entry:{
        entry:'./src/entry.js',
        //这里我们又引入了一个入口文件
        entry2:'./src/entry2.js'
    },
    //出口文件的配置项
    output:{
        //输出的路径，用了Node语法
        path:path.resolve(__dirname,'dist'),
        //输出的文件名称
        filename:'[name].js'
    },
    //模块：例如解读CSS,图片如何转换，压缩
    module:{},
    //插件，用于生产模版和各项功能
    plugins:[],
    //配置webpack开发服务功能
    devServer:{}
}
```
## 模块：CSS文件打包
### Loaders
简单的举几个Loaders使用例子：

可以把SASS文件的写法转换成CSS，而不在使用其他转换工具。
可以把ES6或者ES7的代码，转换成大多浏览器兼容的JS代码。
可以把React中的JSX转换成JavaScript代码。

注意：所有的Loaders都需要在npm中单独进行安装，下面我们对Loaders的配置型简单梳理一下。
test：用于匹配处理文件的扩展名的表达式，这个选项是必须进行配置的；
use：loader名称，就是你要使用模块的名称，这个选项也必须进行配置，否则报错；
include/exclude:手动添加必须处理的文件（文件夹）或屏蔽不需要处理的文件（文件夹）（可选）；
query：为loaders提供额外的设置选项（可选）。

举个例子

```
module:{
        rules: [
            {
              test: /\.css$/,
              use: [ 'style-loader', 'css-loader' ]
            }
          ]
    }
```
## 插件机制

我觉得webpack打包工具的最大贡献就是可以处理缓存策略

有些时候我们不希望将一些第三方库都打包到业务模块里 需要单独抽离出来打包，而且还希望缓存这些包
### 独立打包
```
module.exports = {
    entry: {
        main: './app/index.js',
        vendor: ['jquery']
    },
    output: {
        filename: '[name].js',
        path: path.resolve(__dirname, 'dist')
    },
    plugins:[
        new webpack.optimize.CommonsChunkPlugin({
            name: 'vendor'
        }),
    ]
}
```
上方我们将原本的单入口文件改成了多入口文件，并加入了vendor属性。vendor属性用于配置打包第三方类库，写入数组的类库名将统一打包到一个文件里。

同时我们将输出的filename用[name]变量来自动生成文件名，最后我们添加了一个CommonsChunkPlugin的插件，用于提取vendor。

```
Hash: ee1daf95c1986768927a
Version: webpack 2.3.2
Time: 573ms
        Asset       Size  Chunks                    Chunk Names
      main.js  340 bytes       0  [emitted]         main
vendor.js     274 kB       1  [emitted]  [big]  vendor
   [0] ./~/jquery/dist/jquery.js 267 kB {1} [built]
   [1] ./app/hello.js 53 bytes {0} [built]
   [2] ./app/index.js 114 bytes {0} [built]
   [3] multi jquery 28 bytes {1} [built]
```
最终发现我们成功将jQuery打包到了vendor.js中，实现了独立打包，但是问题又来了：每次打包后生成的文件名都是一样的，浏览器可能缓存上一次的结果而无法加载最新数据。
### 添加hash
为了解决上述问题，我们需要为打包后的文件名添加hash值，这样每次修改后打包的文件hash值将改变，修改配置文件如下：
```
module.exports = {
    ...
        output: {
            filename: '[name].[chunkHash:5].js',
            path: path.resolve(__dirname, 'dist')
        },
    ...
}
```
上方我们在输出文件名中增加了[chunkHash:5]变量，表示打包后的文件中加入保留5位的hash值。我们再次运行打包命令：
```
Hash: c7d1295f2f9a27c412d2
Version: webpack 2.3.2
Time: 603ms
          Asset       Size  Chunks                    Chunk Names
  main.2a7ad.js  337 bytes       0  [emitted]         main
vendor.49eb4.js     274 kB       1  [emitted]  [big]  vendor
   [0] ./~/jquery/dist/jquery.js 267 kB {1} [built]
   [1] ./app/hello.js 50 bytes {0} [built]
   [2] ./app/index.js 114 bytes {0} [built]
   [3] multi jquery 28 bytes {1} [built]
```
上方我们发现打包后的文件成功加上了hash值，这样每次修改文件后hash值也会跟着变，就不怕浏览器缓存了，但是当我们尝试去修改一个js文件后再次打包，问题又来了：vendor.js的hash值也变了，我们并没有修改jQuery的源码。

### 修改vendor配置
上述问题产生的原因是因为CommonsChunkPlugin插件是用于提取公共代码的，上方我们只是提取了vendor作为公共代码。为了继续解决上述问题，其实方法很简单，我们需要修改CommonsChunkPlugin的配置，如下：
```
module.exports = {
    ...
        plugins:[
            new webpack.optimize.CommonsChunkPlugin({
                names: ['vendor', 'manifest']
            }),
        ]
    ...
}
```
如此我们修改一下hello.js中的代码，发现vendor的hash值并未改变，并且多了一个manifest.js的小文件。manifest.js为webpack的启动文件代码，它会直接影响到hash值，用mainfest单独抽出来了，这样vendor的hash就不会变了。
### 生成index.html
通过以上对webpack配置文件的一系列修改，我们成功实现了webpack的独立打包与缓存处理，但是还差最后一步。

因为我们最终打包后生成的文件名中带有hash值，每次都是会变的，所以我们不能像目前这样在index.html中写死路径。

```
...
<body>
    <script src="./dist/main.js"></script>
    <script src="./dist/vendor.js"></script>
    <script src="./dist/manifest.js"></script>
</body>
...
```
以上写法是不对的，因为缺少了可变的hash值，因此我们希望每次打包后index.html中的路径也会自动加上hash值，解决方法如下：
```
var HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    ...
        plugins:[
            ...
            new HtmlWebpackPlugin({
                title: 'demo',
                template: 'index.html' // 模板路径
            }),
            ...
        ]
    ...
}
```
安装和配置完毕后，运行打包命令
```
Hash: 0c4b91e206579b31544d
Version: webpack 2.3.2
Time: 856ms
            Asset       Size  Chunks                    Chunk Names
  vendor.e1868.js     268 kB       0  [emitted]  [big]  vendor
    main.44412.js  337 bytes       1  [emitted]         main
manifest.ed186.js    5.81 kB       2  [emitted]         manifest
       index.html  292 bytes          [emitted]
   [0] ./~/jquery/dist/jquery.js 267 kB {0} [built]
   [1] ./app/hello.js 50 bytes {1} [built]
   [2] ./app/index.js 114 bytes {1} [built]
   [3] multi jquery 28 bytes {0} [built]
```
我们发现在dist目录下生成了一个index.html文件，打开该文件后代码如下：

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>demo</title>
</head>
<body>
<script type="text/javascript" src="manifest.ed186.js"></script>
<script type="text/javascript" src="vendor.e1868.js"></script>
<script type="text/javascript" src="main.44412.js"></script>
</body>
</html>
```