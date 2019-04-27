---
title: vue-cli 升级 webpack4 配置 
date: 2019-03-07 17:20:47
categories:
- package
tags: 
- webpack4 
---
### 升级webpack4  先删除之前的webpack
```bash
npm i -D webpack  webpack-cli webpack-dev-server

```

###  build/base.conf.js 

```javascript 
const os = require('os') //node OS模块 可查看主机系统相关信息
const path = require('path') //node path模块 c处理文件路径
const utils = require('./utils') //vue-cli封装的公用模块
const webpack = require('webpack')
const config = require('../config')//vue-cli封装的配置
const HappyPack = require("happypack")//开启多个子进程去并发执行
const HtmlWebpackPlugin = require('html-webpack-plugin')//webpack主要插件，可以简化HTML文件的创建
const CleanWebpackPlugin = require('clean-webpack-plugin');//清空打包好的文件
const ProgressBarPlugin = require('progress-bar-webpack-plugin')//查看进度
const vueLoaderConfig = require('./vue-loader.conf')// 处理less,sass等样式配置文件 
const VueLoaderPlugin = require('vue-loader/lib/plugin');
const argv = require("yargs-parser")(process.argv.slice(2))//获取运行  scripts 里面的指令 --mode 后面参数 
const isPro = argv.mode == "production"
const resolve = dir => path.resolve(__dirname, "..", dir)

module.exports = {
    context: path.resolve(__dirname, '../'),
    entry: {
        app: "./src/main.js"
    },
    output: {
        path: config.build.assetsRoot,
        filename: '[name].js',
        publicPath: isPro ?
            config.build.assetsPublicPath : config.dev.assetsPublicPath
    },
    resolve: {
        extensions: ['.js', '.vue', '.json'],
        alias: {
            'vue': 'vue/dist/vue.esm.js',
            'src': path.resolve(__dirname, '../src/common'),
            '@': resolve('src'),
            '~': resolve('src/components/common'),
            'static': path.resolve(__dirname, '../static'),
        }
    },
    module: {
        rules: [{
                test: /\.vue$/,
                loader: 'vue-loader',
                options: vueLoaderConfig
            },
            {
                test: /\.js$/,
                loaders: 'happypack/loader?id=babel',//配合 happypack插件使用
                exclude: /(node_modules|bower_components)/,
                include: [resolve('src'), resolve('test')]
            },
            {
                test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
                loader: 'url-loader',
                options: {
                    limit: 10000,
                    name: utils.assetsPath('img/[name].[hash:7].[ext]')
                }
            },
            {
                test: /\.(mp4|webm|ogg|mp3|wav|flac|aac)(\?.*)?$/,
                loader: 'url-loader',
                options: {
                    limit: 10000,
                    name: utils.assetsPath('media/[name].[hash:7].[ext]')
                }
            },
            {
                test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
                loader: 'url-loader',
                options: {
                    limit: 10000,
                    name: utils.assetsPath('fonts/[name].[hash:7].[ext]')
                }
            }
        ]
    },
    node: {
        setImmediate: false,
        dgram: 'empty',
        fs: 'empty',
        net: 'empty',
        tls: 'empty',
        child_process: 'empty'
    },
    plugins: [
        new ProgressBarPlugin(),
        new CleanWebpackPlugin(),
        new HappyPack({
            id: 'babel',
            loaders: [{
                loader: 'babel-loader',
                options: {
                    babelrc: true,
                    cacheDirectory: true,
                },
                publicPath: "/"
            }],
            //共享进程池
            threadPool: HappyPack.ThreadPool({
                size: os.cpus().length //cpu nunbers
            }),
            //允许 HappyPack 输出日志
            verbose: true,
        }),
        new webpack.DefinePlugin({
            'process.env': {
                'NODE_ENV': JSON.stringify(argv.mode)
            }
        }),
        new HtmlWebpackPlugin({
            filename: 'index.html',
            template: 'index.html',
            inject: true,
            favicon: './src/assets/img/favicon.ico'
        }),
        new webpack.ProvidePlugin({//引入全局变量
            $: 'jquery',
            jQuery: 'jquery', // 加上这个
        }), new VueLoaderPlugin()
    ]
}

```

###  build/dev.conf.js 

```javascript
'use strict'
const utils = require('./utils')
const webpack = require('webpack')
const config = require('../config')
const merge = require('webpack-merge')//可以合并 base.conf 配置文件
const baseWebpackConfig = require('./webpack.base.conf')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const FriendlyErrorsPlugin = require('friendly-errors-webpack-plugin')//让日志更加友好
const portfinder = require('portfinder') //查找开放端口或域接字的简单工具 

const HOST = process.env.HOST
const PORT = process.env.PORT && Number(process.env.PORT)
const devWebpackConfig = merge(baseWebpackConfig, {
  mode:'development',
  module: {
    rules: utils.styleLoaders({ sourceMap: config.dev.cssSourceMap, usePostCSS: true })
  },
  devtool: config.dev.devtool,

  devServer: {
    clientLogLevel: 'warning',
    historyApiFallback: true,
    hot: true,
    compress: true,
    host: HOST || config.dev.host,
    port: PORT || config.dev.port,
    open: config.dev.autoOpenBrowser,
    overlay: config.dev.errorOverlay
      ? { warnings: false, errors: true }
      : false,
    publicPath: config.dev.assetsPublicPath,
    proxy: config.dev.proxyTable,
    quiet: true, // necessary for FriendlyErrorsPlugin
    watchOptions: {
      poll: config.dev.poll,
    },
    disableHostCheck: true
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ]
})

module.exports = new Promise((resolve, reject) => {
  portfinder.basePort = process.env.PORT || config.dev.port
  portfinder.getPort((err, port) => {
    if (err) {
      reject(err)
    } else {
      process.env.PORT = port
      devWebpackConfig.devServer.port = port

      // Add FriendlyErrorsPlugin
      devWebpackConfig.plugins.push(new FriendlyErrorsPlugin({
        compilationSuccessInfo: {
          messages: [`Your application is running here: http://${devWebpackConfig.devServer.host}:${port}`],
        },
        onErrors: config.dev.notifyOnErrors
        ? utils.createNotifierCallback()
        : undefined
      }))

      resolve(devWebpackConfig)
    }
  })
})

```

### pluguin

```bash
npm i -D happypack html-webpack-plugin  clean-webpack-plugin progress-bar-webpack-plugin yargs-parser  friendly-errors-webpack-plugin  portfinder  webpack-merge extract-text-webpack-plugin@next optimize-css-assets-webpack-plugin  uglifyjs-webpack-plugin chalk rimraf
```
### babel-loader

```bash
  npm i babel-loader @babel/cli  @babel/core @babel/  @babel/preset-env plugin-proposal-class-properties @babel/ plugin-proposal-decorators @babel/ plugin-syntax-dynamic-import @babel/plugin-transform-classes @babel/plugin-transform-runtime -D

```
```bash
 npm i --save   @babel/runtime   @babel/runtime-corejs2
```
### .babelrc

```json
{
    "presets": [
        ["@babel/preset-env", {
            "useBuiltIns": "usage"
        }]
    ],
    "plugins": [
        ["@babel/plugin-proposal-decorators", {
            "legacy": true
        }],
        "@babel/proposal-class-properties",
        ["@babel/plugin-transform-runtime", {
            "corejs": 2
        }],
        "@babel/plugin-syntax-dynamic-import",
        "@babel/plugin-transform-classes"
    ]
}

```

 ###  build/prod.conf.js 

```javascript
'use strict'
const path = require('path')
const utils = require('./utils')
const webpack = require('webpack')
const config = require('../config')
const merge = require('webpack-merge')
const baseWebpackConfig = require('./webpack.base.conf')
const CopyWebpackPlugin = require('copy-webpack-plugin')//复制文件和目录
const HtmlWebpackPlugin = require('html-webpack-plugin')
const ExtractTextPlugin = require('extract-text-webpack-plugin')//提取样式文件，只有extract-text-webpack-plugin@^4.0.0-beta.0 才支持webpack4  
//或者使用 mini-css-extract-plugin  用法详见  ![GitHub](https://github.com/search?q=mini-css-extract-plugin)
const OptimizeCSSPlugin = require('optimize-css-assets-webpack-plugin') //用于优化\最小化 CSS
const UglifyJsPlugin = require('uglifyjs-webpack-plugin') // 压缩JS资源
const webpackConfig = merge(baseWebpackConfig, {
    mode: 'production',
    module: {
        rules: utils.styleLoaders({
            sourceMap: config.build.productionSourceMap,
            extract: true,
            usePostCSS: true
        })
    },
    devtool: config.build.productionSourceMap ? config.build.devtool : false,
    output: {
        path: config.build.assetsRoot,
        filename: utils.assetsPath('js/[name].[chunkhash].js'),
        chunkFilename: utils.assetsPath('js/[id].[chunkhash].js')
    },
    optimization: {
        splitChunks: {
            chunks: 'all',              // initial、async和all
            minSize: 30000,             // 形成一个新代码块最小的体积
            maxAsyncRequests: 5,        // 按需加载时候最大的并行请求数
            maxInitialRequests: 3,      // 最大初始化请求数
            automaticNameDelimiter: '~', // 打包分割符
            name: true
        },

        minimizer: [

            new UglifyJsPlugin({
                uglifyOptions: {
                    compress: {
                        warnings: false
                    }
                },
                sourceMap: config.build.productionSourceMap,
                parallel: true
            }),
            // extract css into its own file
            new ExtractTextPlugin({
                filename: utils.assetsPath('css/[name].[hash].css'),
                allChunks: true,
            }),
            new OptimizeCSSPlugin({
                cssProcessorOptions: config.build.productionSourceMap ? {
                    safe: true,
                    map: {
                        inline: false
                    }
                } : {
                    safe: true
                }
            }),
            new HtmlWebpackPlugin({
                filename: config.build.index,
                template: 'index.html',
                inject: true,
                favicon: './src/assets/img/favicon.ico',
                minify: {
                    removeComments: true,
                    collapseWhitespace: true,
                    removeAttributeQuotes: true

                },

                chunksSortMode: 'dependency'
            }),


            new webpack.optimize.ModuleConcatenationPlugin(),

            new CopyWebpackPlugin([{
                from: path.resolve(__dirname, '../static'),
                to: config.build.assetsSubDirectory,
                ignore: ['.*']
            }])
        ]

    }
})


module.exports = webpackConfig

```

### build.js

```javascript
'use strict'
require('./check-versions')()

process.env.NODE_ENV = 'production'

const rm = require('rimraf')//删除文件
const path = require('path')
const chalk = require('chalk')//终端字符串样式
const webpack = require('webpack')
const config = require('../config')
const webpackConfig = require('./webpack.prod.conf')

rm(path.join(config.build.assetsRoot, config.build.assetsSubDirectory), err => {
  if (err) throw err
  webpack(webpackConfig, (err, stats) => {

    if (err) throw err
    process.stdout.write(stats.toString({
      colors: true,
      modules: false,
      children: false, 
      chunks: false,
      chunkModules: false
    }) + '\n\n')

    if (stats.hasErrors()) {
      console.log(chalk.red(' 构建失败，错误.\n'))
      process.exit(1)
    }

    console.log(chalk.cyan('  Build success.\n'))
    console.log(chalk.yellow(
      ' 打包文件应该通过HTTP服务器启用.\n' 
    ))
  })
})

```

 