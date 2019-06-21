
    webpack4 搭建过程和思路

#### 目录结构

 大致上目录结构可以分为两个，一个是build文件夹，一个是src文件夹，build下目录可以这么划分

    webpack.base.conf.js
    webpack.dev.conf.js
    webpack.prod.conf.js
    webpack.test.conf.js

#### webpack-merge

 分别对应开发环境，生产环境，测试环境。webpack.base.conf.js则是一些公共配置。我们可以使用webpack-merge把公共配置和对应环境配置合并

    const merge = require('webpack-merge')
    const baseWebpackConfig = require('./webpack.base.conf')

    const devWebpackConfig = merge(baseWebpackConfig, {
       ...
    })

#### webpack.DefinePlugin

 我们可以利用 webpack.DefinePlugin 设置环境

    //设置
    plugins: [
      new webpack.DefinePlugin({
        'process.env': require('../config/dev.env.js')
      }),
    ]

    //使用
    if (process.env.NODE_ENV !== 'production') {
      console.warn("error!")
    }

 另外 webpack4 加入 mode 选项，可以设置成 production 或者 development。但是不能设置 test ，而 test 对应的mode环境是 development
 所以我们还需要 webpack.DefinePlugin 插件来设置 test 环境

#### optimization.splitChunks 代码分片

 曾经有过这样的项目，js只有一个，一次加载所有的js代码，包括每个界面的和所有的UI和组件，导致产出的js特别的大。后来，人们开始分包，把不会改变的
 node_modules 的代码打包在一起，自己的核心公共代码打包在一个js，单独的业务逻辑经常修改的代码打包成一个js来按需加载，这样整个打包的体积会小很多，
 并且充分的利用了缓存。最早在使用gulp，就是自己通过管道来控制js包，现在webpack4提供了代码分片直接来配置达到这种目的。

 webpack4 废弃了 CommonsChunkPlugin ，用 optimization.splitChunks 替代。optimization.splitChunks 默认不需要配置，如果 mode 是
 production，则开启Code Splitting。

 Webpack 4 的 Code Splitting 最大的特点就是配置简单（0配置起步）。默认规则如下：

    splitChunks: {
        chunks: "async”,//默认作用于异步chunk，值为all/initial/async/function(chunk),值为function时第一个参数为遍历所有入口chunk时的chunk模块，chunk._modules为gaichunk所有依赖的模块，通过chunk的名字和所有依赖模块的resource可以自由配置,会抽取所有满足条件chunk的公有模块，以及模块的所有依赖模块，包括css
        minSize: 30000,  //默认值是30kb
        minChunks: 1,  //被多少模块共享
        maxAsyncRequests: 5,  //所有异步请求不得超过5个
        maxInitialRequests: 3,  //初始话并行请求不得超过3个
        name: true,  //打包后的名称，默认是chunk的名字通过分隔符（默认是～）分隔开，如vendor~
        cacheGroups: { //设置缓存组用来抽取满足不同规则的chunk,下面以生成common为例
           common: {
             name: 'common',  //抽取的chunk的名字
             chunks(chunk) { //同外层的参数配置，覆盖外层的chunks，以chunk为维度进行抽取
             },
             test(module, chunks) {  //可以为字符串，正则表达式，函数，以module为维度进行抽取，只要是满足条件的module都会被抽取到该common的chunk中，为函数时第一个参数是遍历到的每一个模块，第二个参数是每一个引用到该模块的chunks数组。自己尝试过程中发现不能提取出css，待进一步验证。
             },
             priority: 10,  //优先级，一个chunk很可能满足多个缓存组，会被抽取到优先级高的缓存组中
             minChunks: 2,  //最少被几个chunk引用
             reuseExistingChunk: true，//	如果该chunk中引用了已经被抽取的chunk，直接引用该chunk，不会重复打包代码
             enforce: true  // 如果cacheGroup中没有设置minSize，则据此判断是否使用上层的minSize，true：则使用0，false：使用上层minSize
           }
        }
    }

 默认情况下，Webpack 会把代码中的公共模块自动抽出来，变成一个包，前提是这个包大于 30kb，不然 Webpack 是不会抽出公共代码的，因为增加一次请求的成本是不能忽视的。

 splitChunks 有一个参数叫 cacheGroups，cacheGroups 里每个对象就是一个用户定义的 chunk。每个 cacheGroups 都可以定义自己抽取模块的范围，
 也就是哪些文件中的公共代码会抽取到自己这个 chunk 中。不同的 cacheGroups 之间的模块范围如果有交集，我们可以用 priority 属性控制优先级。
 Webpack 4 默认的抽取的优先级是最低的，所以模块会优先被抽取到用户的自定义 chunk 中。

    vendors1: {
        test: /[\\/]node_modules[\\/]/,
        name: 'vendor',
        chunks: 'all',
    }

 test 属性可以以 module 为单位控制 chunk 的抽取范围，是一种细粒度比较小的方式。splitChunksPlugin 的第二种控制抽取模块范围的方式就是 chunks 属性。
 chunks 可以是字符串，比如 'all'|'async'|'initial'，分别代表了全部 chunk，按需加载的 chunk 以及初始加载的 chunk。chunks 也可以是一个函数，在这个函数里我们可以拿到 chunk.name。
 这给了我们通过入口来分割代码的能力。这是一种细粒度比较大的方式，以 chunk 为单位。

 举个例子，比如我们有 a, b, c 三个入口。我们希望 a，b 的公共代码单独打包为 common。也就是说 c 的代码不参与公共代码的分割。
 我们可以定义一个 cacheGroups，然后设置 chunks 属性为一个函数，这个函数负责过滤这个 cacheGroups 包含的 chunk 是哪些。示例代码如下：

     optimization: {
         splitChunks: {
           cacheGroups: {
             common: {
               chunks(chunk) {
                 return chunk.name !== 'c';
               },
               name: 'common',
               minChunks: 2,
             },
           },
         },
       },

 使用 import() 加载的模块及其依赖模块会构建一个 async chunk，并在页面上演示加载

 #### vue 项目

 再vue项目中，vue提供vue.common.js和vue.runtime.common.js供开发者使用，先说一下作用runtime不含编译器，他不会编译template模板。而我们在项目中
 使用的vue-loader又会帮我们把模板编译成render渲染函数，而runtime比完整的代码打包后少了100多kb，那么就尽量使用vue.runtime.common.js包吧。

 ##### 另附带 ssr 的 github 地址 [vue-project-ssr](https://github.com/528066535/vue-project-ssr)
