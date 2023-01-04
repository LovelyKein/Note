## Webpack ？

> Webpack 是一种前端资源构建工具，一个静态模块打包器(module bundle)；
>
> 前端项目工程化的具体解决方案；



#### 处理流程

> 1. Webpack 将前端的所有资源文件：`JavaScript`、`JSON`、`CSS`、`img`、`LESS` ...等做模块处理；
> 2. 根据模块间的依赖关系进行静态分析，打包生成对应的静态资源(bundle)；



#### 配置文件

> 在项目根目录下创建 `webpack.config.js` 文件；将配置信息写在此文件中；



#### Initialize

> 下载 webpack 打包构建的模块；
>
> ```shell
> npm install webpack webpack-cli --save-dev
> # 下载内存构建打包模块
> npm install webpack-dev-server --save-dev
> ```





## 核心概念



#### Entry

> 入口`Entry`指示 Webpack 以哪个文件为入口起点开始打包;
>
> 分析构建内部依赖图；



###### 单入口

> ```javascript
> // 字符串形式；---> 打包后是一个文件；
> entry = path.join(__dirname,'/public/js/index.js')
> ```



###### 多入口

> ```javascript
> // 数组形式；---> 打包后依然是一个 chunk 文件；
> entry = [
>      path.join(__dirname,'/public/js/index.js'),
>      path.join(__dirname,'/public/js/main.js')
> ]
> 
> // 对象形式；---> 有几个入口文件，打包后就会有几个 chunk 文件；
> entry = {
>      index: path.join(__dirname,'/public/js/index.js'),
>      main: path.join(__dirname,'/public/js/main.js')
> }
> 
> 
> // 输出；
> output: {
>      filename: '[name].js',
>        path: path.join(__dirname,'/bundle')
> }
> ```



#### Output

> 输出`Output`指示 Webpack 打包后的资源`bundle`要输出到哪里去，以及如何命名；
>
> ```javascript
> output = {
>      // 入口文件打包后的文件名称；
>      filename: 'public/js/bundle_[name].js',
>      // 打包后的所有资源的输出路径；
>      path: path.join(__firname,'/bundle'),
>      // 所有公共资源引入公共路径前缀；
>      publicPath: '/',
>      // 非入口 chunk 文件打包后的名称； 
>      chunkFilename: 'public/js/chunk/[name].js'，
>      // 单独打包的库向外暴露时的文件名称；结合 dll 使用；
>      library: '[name]_[hash]'
> }
> ```
>



#### Module

> `Loader`可以让 Webpack 能够去处理那些非 JavaScript 文件；
>
> Webpack 本身只能处理 JavaScript 和 JSON；
>
> `Loader` 加载器可以协助 webpack 处理打包特定的文件模块；
>
> [^Loader]:A loader is a node module exporting a function，是一个 node 导出的方法；
>
> ```javascript
> module: {
>   rules: [
>     // 文件类型检测规则；loader 配置;
>     {
>       test: /\.css$/,
>       use: ['css-loader','style-loader']
>     }
>     // ......
>   ],
>     oneOf: [
>       // 规则......
>     ]
> }
> ```



##### 使用方式

> - [配置](https://doc.webpack-china.org/concepts/loaders/#configuration)（推荐）：在 `webpack.config.js` 文件中指定 `loader`；
> - [内联](https://doc.webpack-china.org/concepts/loaders/#inline)：在每个 `import` 语句中显式指定 `loader`；
> - [CLI](https://doc.webpack-china.org/concepts/loaders/#cli)：在 `shell` 命令中指定它们；
>
> 推荐使用第一种；



#### Plugins

> 插件`Plugins`可以用于执行范围更广的任务；
>
> 插件的范围包括：从打包优化和压缩，一直到重新定义环境中的变量；



#### Mode

> 模式`Mode`指示 Webpack 使用相应模式的配置；
>
> |  配置选项   |            特点            |
> | :---------: | :------------------------: |
> | Development | 能让代码本地调试运行的环境 |
> | Production  | 能让代码优化上线运行的环境 |

[^注意]:创建  `webpack.config.js` 文件，把配置配置都写在这个文件里；



#### resolve

> 解析模块配置；
>
> ```javascript
> resolve: {
>   // 配置解析模块路径别名：可以简写路径，但是输入没有提示；
>   alias: {
>     $css: path.join(__dirname,'/public/css'),
>     // 告诉 webapck ，路径中的中的 @ 表示当前文件目录下的 src 文件夹，简写路径；
>     '@': path.join(__dirname,'/src')
>   },
>   // 配置可以省略文件的后缀名；
>   extensions: [
>     '.js',
>     '.json',
>     '.css',
>   ],
>   // 告诉 webpack 解析模块去找哪个目录；
>   modules: [path.join(__dirname,'/node_modules'),'node_modules']
> }
> ```



#### devServer

> 用于开发环境中使用；
>
> ```javascript
> devServer: {
>      // 指定运行代码的目录，默认是打包后的文件目录；
>      contentBase: path.join(__dirname,'/bundle'),
>      // 监视 contentBase 目录下的所有文件，一旦文件发生变化就会重新打包；
>      watchContentBase: true,
>      // 忽视一些目录下的文件的变化；
>      watchOptions: {
>        ignored: /node_modules/
>      },
>      // 启动代码压缩；
>      compress: true,
>      // 端口号；
>      port: 8000,
>   	// 域名；
>   	host: 'localhost',
>   	// 自动打开浏览器
>   	open: true,
>   	// 开启 HMR 热模块替换
>   	hot: true,
>   	// 不要显示启动服务器的日志信息；
>   	clientLogLevel: 'none',
>   	// 除了一些基本启动信息之外，其他的内容都不要显示；
>   	quiet: true,
>   	// 如果出错了，不要全屏显示错误信息；
>   	overlay: false,
>   	// 服务器代理，解决开发环境时的跨域问题；
>   	proxy: {
>        '/api': {
>          // 把请求转发给 target ;
>          target: 'http://localhost:5000',
>          // 路径重写；
>          pathRewrite: {
>            '^/api': ''
>          }
>        }
>      }
> }
> ```



#### optimization

> production 生产环境时的代码**优化配置**；
>
> ```javascript
> optimization: {
>      // 分裂文件代码；
>      splitChunks: {
>        chunks: 'all',
>        minSize: 30*1024,
>        maxSize: 0,
>        minChunks: 1,
>        maxAsyncRequests: 5,
>      },
>      // 将当前模块中所记录的其他模块的 hash 值单独打包成一个文件：runtime ；
>      // 解决当修该文件 a 时导致文件 b 的 contenthash 值产生变化；
>      runtimeChunk: {
>        name: entrypoint => `runtime-${entrypoint.name}`
>      },
>      minimizer: [
>        // 配置生产环境时的 js 和 css 的压缩方案；
>        // npm install terser-webpack-plugin --save-dev
>        new TerserWebpackPlugin({
>          // 开启缓存；
>          cache: true,
>          // 开启多线程打包；
>          parallel: true,
>          // 启动 source-map 代码调试优化；
>          sourceMap: true
>        })
>      ]
>   }
> ```



#### externals

> 禁止一些依赖模块被打包，而是在 html 页面中用 src 引入；
>
> ```javascript
> module.exports = {
>   externals: {
>     // npm包名: 引入时的名称
>     // 排除 three.js 依赖文件不进行打包，在 html 页面 CDN 引入
>     three: "THREE",
>     // 排除 jquery 不进行打包
>     jquery: 'jQuery'
>   }
> }
> ```
>



#### devtool

> 配置 `source-map` 属性，便于代码调试；
>
> ```javascript
> module.exports = {
>     devtool: 'source-map'
> }
> 
> // development 开发环境下：devtool: 'eval-source-map'
> // 快速，精准定位到源代码具体的错误行；
> 
> // production 生产环境下：devtool: 'nosources-source-map'
> // 或者不开启 source-map；
> // 防止源代码泄露，提高网站的安全性；
> ```





## 开发环境基本配置

> ```javascript
> // 开发环境配置：代码运行即可；
> 
> const path = require('path');
> const HtmlWebpackPlugin = require('html-webpack-plugin');
> 
> module.exports = {
> 	// 模式配置；
> 	mode: 'development',
> 	// 入口文件配置；
> 	entry: path.join(__dirname, '/src/index.js'),
> 	// 输出目录配置；
> 	output: {
> 		filename: 'bundle.js',
> 		// 打包的所有资源都会输出到这个 bundle 目录下；
> 		path: path.join(__dirname, '/bundle')
> 	},
> 	module: {
> 		rules: [
> 			// loader 的配置；
> 			{
> 				// 处理 less 文件资源；
> 				test: /\.less$/,
> 				use: ['style-loader', 'css-loader', 'less-loader']
> 			},
> 			{
> 				// 处理 css 文件资源；
> 				test: /\.css$/,
> 				use: [
>           // 创建 <style> 标签，将编译好的样式放在里面；
>           'style-loader',
>           // 将 css 文件整合到 js 文件中；
>           'css-loader'
>         ]
>       },
>       {
>         // 处理样式文件中图片资源；
> 				test: /\.(jpg|png|jpeg|gif)$/,
> 				loader: 'url-loader',
> 				options: {
>           // 规定 8KB 以下的图片做 base64 处理；
>           // base64 处理的图片的文件大小会增大，因此大图片不适合做 base64 处理；
>           // 限制图片在 8KB 一下才会进行 base64 处理；
> 					limit: 8 * 1024,
> 					// 给图片重新命名：用 hsah 值的前8位做图片名称；
> 					name: '[hash:8].[ext]',
> 					// 关闭 ES Module 模块化模式；避免与 CommonJS 产生冲突；
> 					esModule: false,
> 					outputPath: 'public/imgs'
> 				}
> 			},
> 			{
> 				// 处理 html 文件中的图片资源；
> 				test: /\.html$/,
> 				loader: 'html-loader'
> 			},
> 			{
> 				// 处理其他类型的资源，例如字体文件；
> 				// 排除其他的已经测试过的文件类型；
> 				exclude: /\.(html|js|css|less|jpg|png|jpeg|gif)/,
> 				loader: 'file-loader',
> 				options: {
>           name: '[hash:8].[ext]',
> 					outputPath: 'media'
> 				}
> 			}
> 		]
> 	},
> 	plugins: [
> 		// plugins 配置；
> 		new HtmlWebpackPlugin({
> 			template: path.join(__dirname, '/src/views/index.html'),
> 			filename: 'views/index.html'
> 		})
> 	],
> 	// 本地服务配置：保存文件后自动打包；
> 	// 开启本地服务端口浏览；
> 	devServer: {
>     contentBase: path.join(__dirname,'/bundle'),
> 		compress: true,
> 		open: true,
> 		port: 8000
> 	}
> }
> ```

[^base 64]:网络上最常见的用于传输8Bit[字节码](https://baike.baidu.com/item/字节码/9953683)的编码方式之一，一种基于64个可打印字符来表示[二进制](https://baike.baidu.com/item/二进制/361457)数据的方法





## 处理 css 文件

> 默认是将 css 样式以 <style> 标签放在 html 文件中；
>
> ```shell
> # 提取依赖项：
> npm install mini-css-extract-plugin --save-dev
> ```
>
> ```shell
> # 兼容性依赖项：
> npm install postcss-loader postcss-preset-env --save-dev
> ```
>
> ```shell
> # 压缩依赖项：
> npm install css-minimizer-webpack-plugin --save-dev
> ```
>
> ```javascript
> const path = require('path');
> // html 文件打包模块；
> const HtmlWebpackPlugin = require('html-webpack-plugin');
> // css 文件提取模块；
> const MiniCssExtractPlugin = require('mini-css-extract-plugin');
> // css 文件压缩模块；
> const CssMinimizerWebpackPlugin = require('css-minimizer-webpack-plugin');
> 
> module.exports = {
> 	// 模式配置；
> 	mode: 'development',
> 	// 入口文件配置；
> 	entry: path.join(__dirname, '/entry.js'),
> 	// 输出目录配置；
> 	output: {
> 		filename: 'bundle.js',
> 		// 打包的所有资源都会输出到这个 bundle 目录下；
> 		path: path.join(__dirname, '/bundle')
> 	},
> 	module: {
> 		rules: [{
> 			// 处理 css 文件资源；
> 			test: /\.css$/,
> 			use: [
> 				// 取代 'style-loader' ，不将 css 文件放到 <style> 标签中；
> 				// 提取 js 文件中的 css 提取成单独文件；
> 				MiniCssExtractPlugin.loader,
> 				// 将 css 文件整合到 js 文件中；
> 				'css-loader',
>                 // css 样式兼容性处理：postcss；
>    				{
> 					loader: 'postcss-loader',
> 					options: {
> 						ident: 'postcss',
> 						plugins: () => [
> 							// postcss 插件;
> 							require('postcss-preset-env')()
> 						]
>           }
>    				}
> 			]
> 		}]
> 	},
> 	plugins: [
> 		// plugins 配置；
> // html 文件打包插件；
>  		new HtmlWebpackPlugin({
> 			template: path.join(__dirname, '/views/index.html'),
> 			filename: 'views/index.html'
> 		}),
> // css 文件提取插件；
>  		new MiniCssExtractPlugin({
> 			filename: 'public/css/bundleStyle.css'
> 		}),
> // css 文件压缩插件；
>  new CssMinimizerWebpackPlugin()
>  	]
> }
> ```
> 
>帮 postcss 找到 package.json 文件中的 browserslist 配置，通过配置加载制定的兼容性 css 样式；
> 
>```json
> "browserslist": {
>   // 开发环境；
>   "development": [
>     "last 1 chrome version",
>      "last 1 firefox version",
>      "last 1 safari version"
>    ],
>   // 生产环境；(默认是这个环境)
>   "production": [
>     ">0.2%",
>      "not dead",
>      "not op_mini all"
>    ]
> }
> ```
> 
>将 browserslist 的默认环境更改为开发环境；
> 
>```javascript
> // 在 webpack 的入口文件中配置 node 环境变量；
> process.env.NODE_ENV = development;
> ```
> 





## JS 语法检查

> 下载依赖的模块插件；
>
> ```shell
> npm install eslint-loader eslint --save-dev
> npm install eslint-config-airbnb-base eslint-plugin-import --save-dev
> ```
>
> 在`package.json`文件中配置`eslintConfig`选项；
>
> ```json
> "eslintConfig": {
>     "extends": "airbnb-base",
>     "env": {
>        // 支持浏览器端的全局变量，配合 PWA 一起使用；
>        "browser": true
>     }
> }
> ```
>
> 在`module.rules`中配置规则；
>
> ```javascript
> module: {
>   rules: [
>      {
>          // 设置检查规则：
>          test: /\.js$/,
>          // 注意：只检查自己写的源代码，第三方库是不用检查的；
>          exclude: /node_modules/,
>          loader: 'eslint-loader',
>          options: {
>            // 自动修复 eslint 的错误；
>            fix: true
>          }
>      }
>   ]
> }
> ```





## JS 兼容性

> 下载依赖项；
>
> ```shell
> npm install @babel/preset-env --save-dev
> npm install babel-loader @babel/core --save-dev
> npm install core-js --save-dev
> ```
>
> 在`module.rules`中配置规则；
>
> ```javascript
> module: {
>  rules: [
>     {
>       // 按需加载兼容性处理：npm install core-js --save-dev
>       test: /\.js$/,
>       exclude: /node_modules/,
>       loader: 'babel-loader',
>       options: {
>         presets: [
>           [
>             '@babel/preset-env',
>             {
>               // 按需加载；
>               useBuiltIns: 'usage',
>               // 指定 core-js 版本；
>               corejs: {
>                 version: 3
>               },
>               // 指定兼容性做到哪个浏览器的版本；
>               targets: {
>                 chrome: '60',
>                 firefox: '60',
>                 ie: '8',
>                 safari: '10',
>                 edge: '17'
>               }
>             }
>           ]
>         ]
>       }
>     }
>  ]
> }
> ```





## 压缩 JS 和 HTML 文件



#### JS 文件压缩

> 更改环境模式；
>
> ```javascript
> // production 生产环境自带 JS 文件压缩配置；
> mode: 'production'
> ```



#### HTML 文件压缩

> 在`html-webpack-plugin`模块插件对象中配置`minify`属性；
>
> ```javascript
> plugins: [
>     new HtmlWebpackPlugin({
>        template: path.join(__dirname, '/views/index.html'),
>        filename: 'views/index.html',
>        // 压缩 html 文件；
>        minify: {
>          // 移除空格；
>          collapseWhitespace: true,
>          // 移除注释；
>          removeComments: true
>        }
>     })
> ]
> ```





## 生产环境基本配置

> ```javascript
> // 生产环境基本配置；
> 
> const path = require('path');
> const HtmlWebpackPlugin = require('html-webpack-plugin');
> // css 文件提取模块；
> const MiniCssExtractPlugin = require('mini-css-extract-plugin');
> // css 文件压缩模块；
> const CssMinimizerWebpackPlugin = require('css-minimizer-webpack-plugin');
> 
> const commonCssLoader = [
>     // 将 css 文件整合到 js 文件中；
>     MiniCssExtractPlugin.loader,
>     'css-loader',
>     // css 兼容性；
>     {
>        loader: 'postcss-loader',
>     options: {
>          ident: 'postcss',
>       plugins: () => [
>            // postcss 插件;
>         require('postcss-preset-env')()
>          ]
>        }
>     }
> ]
> 
> module.exports = {
>     mode: 'production',
>   entry: path.join(__dirname, '/public/js/entry.js'),
>   output: {
>        filename: 'public/js/bundle.js',
>     path: path.join(__dirname, '/bundle')
>   },
>   module: {
>     rules: [{
>          test: /\.css$/,
>          use: [...commonCssLoader]
>        },
>                {
>                  test: /\.less$/,
>                  use: [...commonCssLoader, 'less-loader']
>                },
>                // JS 语法检查；
>                {
>                  test: /\.js$/,
>                  exclude: /node_modules/,
>                  // 优先执行；
>                  enforce: 'pre',
>                  loader: 'eslint-loader',
>                  options: {
>                    fix: true
>                  }
>                },
>                // JS 兼容性；
>                {
>                  test: /\.js$/,
>                  exclude: /node_modules/,
>                  loader: 'babel-loader',
>                  option: {
>                    presets: [
>                      [
>                        '@babel/preset-env',
>                        {
>                          // 按需加载；
>                          useBuiltIns: 'usage',
>                          // 指定 core-js 版本；
>                          corejs: {
>                            version: 3
>                          },
>                          // 指定兼容性做到哪个浏览器的版本；
>                          targets: {
>                            chrome: '60',
>                            firefox: '60',
>                            ie: '8',
>                            safari: '10',
>                            edge: '17'
>                          }
>                        }
>                      ]
>                    ],
>                    // 开启 babel 缓存；
>                    // 第二次构建时生效，会读取之前的缓存；
>                    // 没有发生改动的 JS 文件不会被再次打包，而是读取之前的缓存；
>                    cacheDirectory: true
>                  }
>                },
>                {
>                  // 处理样式文件中图片资源；
>                  test: /\.(jpg|png|jpeg|gif)$/,
>                  loader: 'url-loader',
>                  options: {
>                    // 规定 8KB 以下的图片做 base64 处理；
>                    limit: 8 * 1024,
>                    // 给图片重新命名：用 hsah 值的前8位做图片名称；
>                    name: '[hash:8].[ext]',
>                    // 关闭 ES Module 模块化模式；避免与 CommonJS 产生冲突；
>                    esModule: false,
>                    outputPath: 'public/imgs'
>                  }
>                },
>                {
>                  // 处理 html 文件中的图片资源；
>                  test: /\.html$/,
>                  loader: 'html-loader'
>                },
>                {
>                  // 处理其他类型的资源；
>                  // 排除其他的已经测试过的文件类型；
>                  exclude: /\.(html|js|css|less|jpg|png|jpeg|gif)/,
>                  loader: 'file-loader',
>                  options: {
>                    name: '[hash:8].[ext]',
>                    outputPath: 'public/media'
>                  }
>                }
>               ]
>     },
>   plugins: [
>        // 处理 html 文件；
>        new HtmlWebpackPlugin({
>          template: path.join(__dirname, '/views/index.html'),
>          filename: 'views/index.html',
>          // 压缩 html 文件；
>          minify: {
>            // 移除空格；
>            collapseWhitespace: true,
>         // 移除注释；
>         removeComments: true
>          }
>        }),
>        // css 文件提取插件；
>        new MiniCssExtractPlugin({
>          filename: 'public/css/bundleStyle.[contenthash:6].css'
>        }),
>        // css 文件压缩插件；
>        new CssMinimizerWebpackPlugin()
>     ]
> }
> ```





## clean-webpack-plugin

> 在每次打包前自动删除之前打包生成的文件夹，再重新生成一个；

> Install
>
> ```shell
> npm install clean-webpack-plugin --save-dev
> ```
>
> Use
>
> ```javascript
> // 加载模块插件
> const CleanWebpackPlugin = require('clean-webpack-plugin')
> module.exports = {
>    plugins: [
>        new CleanWebpackPlugin()
>      ]
>    }
> ```





## copy-webpack-plugin

> **禁止一些文件被打包，而是保持不变直接复制到打包文件里面**；
>
> Install
>
> ```shell
> npm install copy-webpack-plugin --save-dev
> ```
>
> Use
>
> ```javascript
> // 加载模块文件
> const CopyPlugin = require("copy-webpack-plugin")
> module.exports = {
>   plugins: [
>     // 设置一些文件不需要被打包
>     new CopyPlugin({
>       patterns: [
>         { from: path.join(__dirname, "./font"), to: "font" },
>         { from: path.join(__dirname, "./sound"), to: "sound" },
>         { from: path.join(__dirname, "./image"), to: "image" },
>         { from: path.join(__dirname, "./static"), to: "static" },
>       ]
>     })
>   ]
> }





## 性能优化

> 增加一些配置，提高打包速度和优化代码调试；



#### 开发环境



###### HMR

> HMR：热模块替换（Hot Module Replacement）；
>
> 一个模块内容发生变化，只会重新打包这一个模块；
>
> 而不会再次打包所有模块，极大提升***打包构建速度***；

> 为样式（css、less）文件开启 HMR；
>
> **前提**：样式文件是被 `style-loader`  模块加载的；
>
> ```javascript
> // 在 devServer 本地服务配置对象中添加 hot 属性；
> devServer: {
>     hot: true
> }
> ```

> 为 js 文件（非 entey 入口文件）开启 HMR；
>
> ```javascript
> // 在 entry 入口文件中引入要执行 HMR 功能的其他 js 文件；
> import otherJavaScript from './otherJavaScript.js';
> 
> // 在 entry 入口文件中写入：
> if (module.hot) {
>     // module.hot 为 true 时，则开启 HMR 功能；
>     module.hot.accept('./otherJavaScript.js',function(){
>        // accept() 方法会监听 otherJavaScript.js 文件的变化；
>        // 一旦发生变化，则只再重新打包这一个模块；
>        otherJavaScript();
>     })
> }
> ```

[^提示]:以上为**优化打包构建速度**；



###### source-map

> `source-map`是源代码到打包构建后代码的映射技术；
>
> 如果构建后代码出错了，会通过映射找到源代码的错误；
>
> 极大提升**代码调试速度**；
>
> ```javascript
> // 在 module.exports 对象中添加 devtool 属性；
> module.exports = {
>     devtool: 'source-map'
> }
> 
> // 开发环境：构建速度要快，调试要友好：
> // devtool: 'eval-source-map';
> // devtool: 'eval-cheap-module-source-map';
> 
> // 生产环境：源代码可能需要隐藏：
> // devtool: 'nosources-source-map';
> // devtool: 'hidden-source-map'
> ```

[^提示]:以上为**优化代码调试**；



#### 生产环境



###### oneOf

> 不能有两个配置规则处理同一种类型的文件；
>
> 写在`oneOf`里的规则`loader`只会匹配使用一个；
>
> ```javascript
> module.exports = {
>     module: {
>        rules: [
>          // JS 语法检查；
>          {
>            test: /\.js$/,
>            exclude: /node_modules/,
> 				// 优先执行；
> 				enforce: 'pre',
> 				loader: 'eslint-loader',
> 				options: {
>              fix: true
>            }
>          }
>        ],
>        oneOf: [
>          // 将 rules 数组里面的规则提取出来写在 oneOf 上；
>          {
>            // 其他规则；
>          },
>          {
>            // 其他规则；
>          }
>        ]
>     }
> }
> ```



###### babel 缓存

> HMR 要配置在 devServer 本地服务中，对生产环境无效；
>
> 开启 babel 缓存，对 js 文件优化打包构建速度；
>
> ```javascript
> module.exports = {
>     module: {
>        rules: [
>          // JS 兼容性规则；
>          {
>            test: /\.js$/,
>            exclude: /node_modules/,
>            loader: 'babel-loader',
>            option: {
>              presets: [
>                [
>                  '@babel/preset-env',
>                  {
>                    // 按需加载；
>                    useBuiltIns: 'usage',
>                    // 指定 core-js 版本；
>                    corejs: {
>                      version: 3
>                    },
>                    // 指定兼容性做到哪个浏览器的版本；
>                    targets: {
>                      chrome: '60',
> 									firefox: '60',
> 									ie: '8',
> 									safari: '10',
> 									edge: '17'
>                    }
>                  }
>                ]
>              ],
>           // 开启 babel 缓存；
> 					// 第二次构建时生效，会读取之前的缓存；
> 					// 没有发生改动的 JS 文件不会被再次打包，而是读取之前的缓存；
>              cacheDirectory: true
>            }
>          }
>        ]
>     	}
> }
> ```
>

  

###### 多线程打包

>开启多线程打包，进程大概为 600ms ，进程通信也有时间；
>
>[^注意]:一般文件多，项目大才会使用多线程打包；

>下载模块
>
>```shell
>npm install thread-loader --save-dev
>```

> 给 babel 缓存开启 js 文件多线程打包；
>
> ```javascript
> module.exports = {
>     module: {
>        rules: [
>          // JS 兼容性规则；
>          {
>            test: /\.js$/,
>            exclude: /node_modules/,
>            use: [
>              // 开启多线程打包 loader ;
>              'thread-loader',
>              {
>                loader: 'babel-loader',
>                option: {
>                  presets: [
>                    [
>                      '@babel/preset-env',
>                      {
>                        // 按需加载；
>                        useBuiltIns: 'usage',
>                        // 指定 core-js 版本；
>                        corejs: {
>                          version: 3
>                        },
>                        // 指定兼容性做到哪个浏览器的版本；
>                        targets: {
>                          chrome: '60',
>                          firefox: '60',
>                          ie: '8',
>                          safari: '10',
>                          edge: '17'
>                        }
>                      }
>                    ]
>                  ],
>                  // 开启 babel 缓存；
>                  // 第二次构建时生效，会读取之前的缓存；
>                  // 没有发生改动的 JS 文件不会被再次打包，而是读取之前的缓存；
>                  cacheDirectory: true
>                }
>              }
>            ]
>          }
>        ]
>     }
> }
> ```



###### externals

> 禁止一些依赖模块被打包；
>
> ```javascript
> module.exports = {
>     externals:{
>        // npm包名: 引入时的名称
>        jquery: 'jQuery'
>     }
>    }
> ```

[^提示]:以上为**优化打包构建速度**；



###### 懒加载

> 不全部加载文件代码，等需要的时候再去加载；
>
> 利用 ES6 的动态加载方法 `import()` 实现；
>
> ```javascript
> // 例子：按钮点击后加载；
> document.getElementById('button').onclick = function(){
>   // 懒加载；
>   import(/*webpackChunkName:
> 'fileName'*/'./file.js').then(()=>{
>   console.og('module loading now !');
>   })
> }
> ```



###### 预加载

> 在使用前预先加载好文件；
>
> 等其他资源加载完毕，浏览器性能空闲时，再去加载文件；
>
> 兼容性差；
>
> ```javascript
> // webpackPrefetch: true
> document.getElementById('button').onclick = function(){
>   // 预加载；
>   import(/*webpackChunkName: 'fileName', webpackPrefetch: true*/'./file.js').then(()=>{
>     console.og('module loading now !');
>   })
> }
> ```

[^注意]:正常的加载是并行加载，同一时间加载多个文件；



###### 文件资源缓存

> 给文件重命名，加上 hash 值；
>
> 如果 hash 值一样，则会读取缓存，不会重新打包；
>
> ```javascript
> filename: 'public/css/bundleStyle.[contenthash:6].css',
> filename: 'public/js/bundle.[contenthash:6].js'
> ```

* [^hash]: 每次 Webpack 构建时都会生成一个唯一的 hash 值；

  > **问题：**文件共用一个 hash 值，如果重新打包，会导致所有的缓存都失效；

  

* [^chunkhash]: 根据 chunk 生成的 hash 值，如果打包来源同一个 chunk ，则 hash 值一样；

  > **问题：**js 和 css 文件的 hash 值还是一样，因为 css 被 入口 js 文件引入；

  

* [^contenthash]: 根据文件的内容生成的 hash 值；

  > **优点：**不同文件的内容一定不一样，所以 hash 值也不一样；

  

###### tree shaking

> **作用：**去除无用的、未使用的代码；减少代码体积；
>
> **前提：**处于 production 生产环境；使用 ES6 模块化引入文件；
>
> **问题：**默认是所有文件都会进行 `tree shaking` ，可能会把 css、模块插件删掉；
>
> ```javascript
> // 在 package.json 文件中配置 sideEffects 选项；
> 
> "sideEffects": [
>     "*.css",
>     "*.less"
> ]
> 
> // 这样就不会对配置的文件类型进行 tree shaking ；
> ```



###### code split

> 把文件根据功能不同分成几部分；
>
> ```javascript
> // (1)
> // 多入口 entry 文件：
> module.exports = {
>   entry: {
>      index: path.join(__dirname,'/public/js/index.js'),
>      main: path.join(__dirname,'/public/js/main.js')
>   },
>   output: {
>      filename: 'public/js/[name].[contenthash:6].js',
>      path: path.join(__dirname,'/bundle')
>   }
> }
> 
> 
> // (2)
> // 自动将来自 node_modules 中的模块单独打包成一个 chunk 输出；
> // 自动分析多入口文件中，有没有共同依赖项，有则单独打包成一个 chunk ；
> module.exports = {
>   optimization: {
>      splitChunks: {
>          chunks: 'all'
>      }
>   }
> }
> 
> 
> // (3)
> // 利用 ES6 的动态引入方法 import() ；让文件运行时加载模块；
> // 使加载的文件单独打包成一个 chunk ；
> import(/*webpackChunkName: 'fileName'*/'./page.js').then(()=>{
> console.log('code running success !');
> }).catch(()=>{
> console.log('code running erroe !')
> })
> ```





## PWA

> [^PWA]:渐进式网页应用(**Progressive Web App**)，一种理念；
>
> 使用多种技术来增强 Web App 的功能，可以让网站的体验变得更好，能够模拟一些原生功能；
>
> 离线时也可访问；



#### Webpack 中使用



###### 下载

> ```shell
> npm install workbox-webpack-plugin --save-dev
> ```



###### 配置模块插件

> ```javascript
> module.exports = {
>     plugins: [
>        // 配置模块插件；
>        new WorkboxWebpackPlugin.GenerateSW({
>          // 帮助 serviceWorker 快速启动；
>          // 删除旧的 serviceWorker ；
>          // 会生成一个 service-worker 配置文件；
>          clientsClaim: true,
>          skipWaiting: true
>        })
>     ]
> }
> ```

[^注意]:`serviceWorker` 代码要运行在服务器上才会生效；



###### 注册 serviceworker

> 在入口文件 entry 中注册 serviceWorker ；

> ```javascript
> // 处理兼容性问题；
> if('serviceWorker' in navigator){
>     window.addEventListener('load',function(){
>        navigator.serviceWorker.register('/service-worker.js').then(()=>{
>          console.log('serviceWorker success for register !');
>     }).catch(()=>{
>          console.log('serviceWorker failure !');
>        })
>     })
> }
> ```



###### 增加全局变量

> eslint 不认识 window、navigator 全局变量；
>
> ```json
> // 在 package.json 文件中的 eslintConfig 选项中添加全局变量；
> {
>     "eslintConfig": {
>        "extends": "airbnb-base",
>        "env": {
>          // 支持浏览器端的全局变量；
>          "browser": true
>        }
>     }
> }
> ```





## dll

> node_modules 文件中的库，一般是打包成一个文件，效率低；
>
> 使用` dll`技术，对某些第三方库：jquery、react、vue 进行单独打包；



#### 创建 dll 配置文件

> 项目根目录下创建 `webpack.dll.js`  文件，单独打包的配置写在这个文件里；



#### webpack.dll.js

> ```javascript
> const path = require('path');
> const webpack = require('webpack');
> 
> module.exports = {
>     entry: {
>        // 要对哪些库进行单独打包；
>         // jquery 表示单独打包后的文件名；
>        // ['jquery'] 表示要打包的库是 jquery ；
>        jquery: ['jquery']
>      },
>     output: {
>       filename: '[name].js',
>        // 单独打包的文件都会输出到项目目录下的 dll 文件里；
>        path: path.join(__dirname,'/dll'),
>        // 单独打包的库向外暴露时的文件名称；
>        library: '[name]_[hash]'
>      },
>     plugins: [
>       // 生成一个 manifest.json 文件，保存了与单独打包的库的映射关系；
>        new webpack.DllPlugin({
>          name: '[name]_[hash]',
>          // 该映射文件的输出路径；
>          path: path.join(__dirname,'/dll/manifest.json')
>        })
>      ],
>     mode: 'production'
>   }
> ```



#### webpack.config.js

> 配置模块和插件，让 dll 生效；
>
> ```javascript
> const path = require('path');
> const webpack = require('webpack');
> const AddAssetHtmlWebpackPlugin = require('add-asset-html-webpack-plugin');
> 
> module.exports = {
>     plugins: [
>        // 告诉 webpack 哪些库不参与打包，且使用时的名称也得按照 webpack.dll.js 的配置来；
>        new webpack.DllReferencePlugin({
>          manifest: path.join(__dirname,'/dll/manifest.json')
>        }),
>        // 使库被单独打包出去后，会在 html 文件中自动引用该资源库；
>        new AddAssetHtmlWebpackPlugin({
>          filepath: path.join(__dirname,'/dll/jquery.js')
>        })
>     ],
>     mode: 'production'
> };
> ```





## Webpack 5

