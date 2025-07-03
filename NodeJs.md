# Node.js ?

Node.js 不是一门语言，不是库，不是框架，而是一个基于`Chrome V8`引擎的 JavaScript 运行环境（平台）
简单来说就是 Node.js 可以解析和执行 JavaScript 代码，可以代替浏览器，使用`C++`语言开发
特性：

- non-blocking I/O model（非阻塞 IO 模型，异步）
- lightweight and efficient（轻量和高效）



## 搭建 Web 服务器后台

```javascript
//加载 http 核心模块
var http = require('http')
//使用createServer（）方法创建一个 web 服务器
var server = http.createServer()
//注册 request 请求事件，当有客户端请求过来时，自动触发事件，然后执行第二个参数（回调函数）
server.on('request'，function(request, response) {
  console.log('收到客户端的请求了')
  response.end('这是响应的数据内容！')

  // 解决向客户端发送响应数据时出现乱码的情况：
 // 告诉浏览器按照 utf-8 编码格式去解析响应数据
  response.setHeader('Content-Type', 'text/html;charset=utf-8')
 // 在 http 协议中，Content-Type 就是告诉客户端收到的数据是什么类型的；
})
//绑定端口号，启动服务器：
server.listen(8000，function() {
  console.log('服务器已启动，可以通过 http://127.0.0.1:8000/ 来进行访问')
})
```



## 命令行工具

* npm（node）
* git（C 语言）
* hexo（node）



## 运行 JavaScript 代码

在文件位置打开终端，输入：`node FileName.js`



## REPL

在终端中以Node.JS来运行JavaScript代码

```shell
node + enter(回车键)
```



# `CommonJS`规范

**在node中，由于有且仅有一个入口文件（启动文件）**
而开发一个应用肯定会涉及到多个文件配合，因此，node对模块化的需求比浏览器端要大的多

![image-20250628163322200](./assets/image-20250628163322200.png)

由于node刚刚发布的时候，前端没有统一的、官方的模块化规范，因此它选择使用社区提供的`CommonJS`作为模块化规范
CommonJS具体规范如下：

* **使用`require()`方法来加载模块，使用`exports`接口对象来导出模块中的成员**
* 如果一个JS文件中存在`exports`或`require`，该JS文件是一个模块
* 模块内的所有代码均为隐藏代码，包括全局变量、全局函数，这些全局的内容均不应该对全局变量造成任何污染
* 如果一个模块需要暴露一些API提供给外部使用，需要通过`exports`导出
  `exports`是一个空的对象，你可以为该对象添加任何需要导出的内容
* 如果一个模块需要导入其他模块，通过`require`函数实现，传入模块的url即可返回模块导出的整个内容

而为了实现`CommonJS`规范，node对模块做出了以下处理：

- 为了保证高效执行，node只有执行到`require`函数时才会进行加载并执行模块

- 为了隐藏模块中的代码，node执行模块时会将模块中的所有代码放置到一个函数环境中执行，以保证不污染全局变量

  ```js
  (function() {
    // 模块中的代码
  })()
  ```

- 为了保证顺利的导出模块内容，node做了以下处理：

  1. 在模块开始执行前，初始化一个值`module.exports = {}`

  2. `module.exports`即模块的导出值

  3. 为了方便导出，node在初始化完`module.exports`后，又声明了一个变量`exports = module.exports`

     ```js
     (function() {
       module.exports = {}
       var exports = module.exports
       /** 模块中的代码 **/
       return module.exports
     })()
     ```

- 为了避免反复加载同一个模块，node默认开启了模块缓存
  如果加载的模块已经被加载过了，则会自动使用之前的导出结果

> [!NOTE]
>
> `CommonJS`是同步执行的，必须要等到加载完文件并执行完代码后才能继续向后执行



## 导入`require()`

`require`既可以加载node中的核心模块，也可以加载引入js文件
其作用是：执行被加载模块中的代码，并得到被加载模块中的`exports`导出

> [!NOTE]
>
> 引入js文件时，一定要用相对路径书写，且路径中要以`./`或`../`开头

```javascript
// 导入核心模块
const path = require('path')
// 导入第三方模块
const axios = require('axios')
// 导入js文件
const module = require('./module_file.js')
```

查找规则：

1. 查找是否有内置模块`path`
2. 查找当前目录中有没有`node_modules/path.js`文件
3. 查找当前目录中有没有`node_modules/path/入口文件`
4. 依次查找上级目录中有没有`node_modules/path/入口文件`，直到根目录



## 导出`module.exports`

node中是模块作用域，默认文件中所有的成员只在当前文件模块有效
如果希望可以被其他模块访问，就需要把要公开的元素挂载到`exports`接口对象上
`exports`初始化时是一个空对象，将要导出的数据写在`exports`对象身上

```javascript
/** 使用 exports 导出 **/
exports.a = 'Kein'
exports.b = ['22','19','52','13','14']
exports.c = {
  name: 'Kein',
  agr: 22,
  sex: 'male'
}
```

```javascript
/** 同样也可以使用 module.exports 来导出 **/

module.exports = 'Kein'

module.exports = {
  name: 'Kein',
  agr: 22,
  sex: 'male',
  print: function(){
    console.log(this.name + this.age)
  }
}
```

原理解析：

每个模块都有一个`module`对象，`module`对象中有一个`exports`对象
**`exports`是`module.exports`的一个引用，模块真正导出的是`module.exports`而不是`exports`**

```javascript
// 模块源码里有这样一行代码，为了方便书写
var exports = module.exports
```

```javascript
console.log(exports === module.exports) // true
exports.name = 'Kein'
// 等价于
module.exports.name = 'Kein'
```



# 前端包管理器

包管理工具解决了在使用第三方库时遇到的问题

- 下载过程繁琐
  1. 进入官网或 github主页
  2. 找到并下载相应的版本
  3. 拷贝到工程的目录中
  4. 如果遇到有同名的库，需要更改名称
- 如果该库需要依赖其他库，还需要按照要求先下载其他库
- 开发环境中安装的大量的库如何在生产环境中还原，又如何区分
- 更新一个库极度麻烦
- 自己开发的库，如何在下一次开发使用



## `npm`

npm是`node package manager`的简称，[官方网站](https://www.npmjs.com)
npm 由三部分组成：

- 入口`registry`

  可以把它想象成一个庞大的数据库
  第三方库的开发者将自己的库按照npm的规范，打包上传到数据库中
  使用者通过统一的地址下载第三方包、查询包

- 注册、登录、管理个人信息

- 命令行接口`CLI(command-line interface) `

  安装好npm后，通过`CLI`来使用npm的各种功能



### 常用命令

配置仓库源

```shell
# 由于npm的官方registry服务器位于国外，可能受网速影响导致下载缓慢或失败
# 因此安装好npm之后，需要重新设置registcry的地址为国内的镜像地址
# 淘宝https://registry.npm.taobao.org提供了
npm config set registry https://registry.npm.taobao.org # 设置仓库源
npm config get registry # 获取仓库源
```

本地安装一个包的时候，npm会自动管理依赖，它会下载该包的依赖目录`node_modules`中
如果依赖包中有`CLI`，npm会将`CLI`脚本文件放到`node_modules/.bin`目录下，使用命令`npx`命令名即可调用

```shell
npm i mocha
# 使用 mocha 中的命令工具
npx mocha
```

查看版本

```shell
npm --version
# 简写
npm -v
```

全局安装

```shell
# 全局安装增加参数 --global 或 -g
npm install --global npm
# 全局安装的包放置在一个特殊的全局目录，查看该目录
npm config get prefix
```

卸载模块

```shell
npm uninstall react
npm uninstall yarn -g
# 简写
npm un react
```

查看npm使用帮助

```shell
npm --help
```

清除缓存

```shell
npm cache clear --force
```

安装模块
此时第三方模块的信息就会被`package.json`文件的`dependencies`选项所记录

```shell
npm install react --save
# 或
npm install react -S
# 安装指定版本的依赖包
npm install react@19.1 -S
```

安装到开发环境
```shell
npm install mocha --save-dev
# 或
npm install mocha -D
```

如果项目中的`node_modules`文件丢失，通过` npm install `方式重新加载
根据`package.json`文件里的`dependencies`选项所记录的第三方模块信息

```shell
npm install
# 仅安装生产环境下的依赖
npm install --production
```

查询依赖包安装路径

```shell
node root # 项目中 node_modules 的位置
node root -g # 全局安装的 node_modules 的位置
```

查看包信息
```shell
npm view 包名 [子信息] # 命令语法
npm view react
npm view react version # 查看 react 中的 version 信息
```

查看包列表

```shell
npm list
npm list -g # 全局
npm list --depth=0 # --depth 参数查看第几级依赖，0 就是只看第一级
```

更新包版本

```shell
npm update
npm update lodash
npm update npm -g
```

查看目前生效的npm配置

```shell
npm config ls [-l] [--json]

# 获取某个配置项
npm config get 配置项
# 设置配置项
npm config set 配置项=值
# 删除配置项
npm config delete 配置项
```



### 运行脚本

```json
{
  "scripts": {
    "serve": "vue-cli-service serve --open",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
}
```

运行`package.json`文件中`scripts`中的脚本

```shell
npm run 脚本名称

# 'serve' 为脚本名称，'npx vue-cli-service serve --open' 为实际执行的指令
npm run serve
```

> [!NOTE]
>
> npm还对某些常用的脚本名称进行了简化，下面的脚本名称是不需要加`run`的：`start`、`stop`、`test`
> 脚本中可以省略`npx`，即如果在控制台中直接写实际指令，则为`npx vue-cli-service serve --open`



## 配置文件`package.json`

建议每个项目的根目录下都要有一个`package.json`文件
项目的描述文件，就像产品的说明书一样，存有项目的各类信息

```shell
# 通过npm自动创建package.json文件
npm init
# 或者
npm init --yes
```

可以手动创建该文件，而更多的时候是通过合命令`npm init`创建的
配置文件中可以描述大量的信息，包括：

- `name`：包的名称，该名称必须是英文单词字符，支持连接符

- `version`：版本，版本规范：`主版本号.次版本号.补丁版本号`

  - 主版本号：仅当程序发生了重大变化时才会增长，如新增了重要动能、新增了大量的API、技术架构发生了重大变化
  - 次版本号：仅当程序发生了一些小变化时才会增长，如新增了一些小功能、新增了一些辅助型的API
  - 补丁版本号：仅当解决了一些bug或进行了一些局部优化时更新，如修复了某个函数的bug、提升了某个函数的运行效率

- `description`：包的描述

- `homepage`：官网地址

- `author`：包的作者，必须是有效的npm账户名，书写规范是`account <mail>`
  例如：`zhangsan <zhangsan@gmail.com>`，**不正确的账号和邮箱可能导致发布包时失败**

- `repository`：包的仓储地址，通常指git或svn的地址，它是个对对象

  - `type`：仓储类型，git或svn
  - `url`：地址

- `main`：包的入口文件，使用包的人默认从该入口文件导入包的内容

- `keywords`：搜索关键字，发布包后可以通过该数组中的关键字搜索到

- `dependencies`：生产环境的依赖包，记录了项目所加载依赖的第三方模块的信息

  ```json
  "dependencies": {
    "art-template": "^4.13.2"
  }
  ```

- `devDependencies`：仅开发环境的依赖包

- `license`： 协议

  ![image-20250701111951317](./assets/image-20250701111951317.png)

也可以在配置文件中书写**自定义属性**，然后可以在其他文件中通过`require`获取对象配置

```json
{
  'custom_key': "nodejs"
}
```

```js
const config = require('../package.json')
console.log(config.custom_key) // 'nodejs'
```



### 语义版本

语义版本的书写规则非常丰富，下面列出了一些常见的书写方式

| 符号 | 描述                 | 示例          | 实例描述                                                  |
| ---- | -------------------- | ------------- | --------------------------------------------------------- |
| `>`  | 大于某个版本         | `>1.2.1`      | 大于`1.2.1`版本                                           |
| `>=` | 大于等于某个版本     | `>=1.2.1`     | 大于等于`1.2.1`版本                                       |
| `<`  | 小于某个版本         | `<1.2.1`      | 小于`1.2.1`版本                                           |
| `<=` | 小于等于某个版本     | `<=1.2.1`     | 小于等于`1.2.1`版本                                       |
| `-`  | 介于两个版本之间     | `1.2.1-1.4.5` | 介于`1.2.1`和`1.4.5`之间                                  |
| `x`  | 不固定的版本号       | `1.3.x`       | 只要保证主版本号是`1`，次版本号是`3`即可                  |
| `~`  | 补丁版本号可增       | `~1.3.4`      | 保证主版本号是`1`，次版本号是`3`，补丁版本大于等于`4`     |
| `^`  | 次版本和补丁版本可增 | `^1.3.4`      | 保证主版本号是`1`，次版本大于等于`3`，补丁版本大于等于`4` |
| `*`  | 最新版本             | `*`           | 始终安装最新版本                                          |



## 运行环境配置

我们书写的代码一般有三种运行环境：开发环境、生产环境、测试环境
node中有一个全局变量`global`（可以类比浏览器环境的`window`），对象中的所有属性均可以直接使用
`global`有一个属性是`process`，该属性是一个对象，包含了当前运行node程序的计算机的很多信息
其中有一个信息是`env`，是一个对象包含了计算机中所有的系统变量，即`process.env`

通常，我们通过系统变量`NODE_ENV`的值，来判定node程序处于何种环境
有两种方式设置：

1. 永久设置

   ![image-20250701103615222](./assets/image-20250701103615222.png)

   选择添加一个系统变量`NODE_ENV`，值设置为`development`

2. 临时设置

   在`package.json`的`scripts`脚本中设置
   即运行`npm run dev`时，`process.env.NODE_ENV`的值就是`development`

   ```json
   {
     "scripts": {
       "dev": "set NODE_ENV=development node index.js",
       "build": "set NODE_ENV=production node index.js",
     }
   }
   ```

   不同电脑下游不同的写法，在Mac电脑中就要把`set`换成`export`

   ```json
   {
     "scripts": {
       "dev": "export NODE_ENV=development node index.js"
     }
   }
   ```

   **为了避免不同系统的设置方式的差异可以使用第三方库`cross-env`对环境变量进行设置**

   ```shell
   npm install cross-env -D
   ```

   ```json
   {
     "scripts": {
       "dev": "cross-env NODE_ENV=development node index.js"
     }
   }
   ```



## `yarn`

[官网](https://yarnpkg.com/)
相比于npm，yarn的优势有：

- 使用扁平的目录结构
- 并行下载
- 使用本地缓存
- 控制台仅输出关键信息
- 使用`yanr-lock`文件记录确切依赖
- 本地安装的依赖包中的CLI工具可以使用`yarn`直接启动

npm在6版本后，也向yarn学习了优点，解决了以前的一些问题，例如内置了`npx`用于启动本地依赖的CLI工具



## `pnpm`

pnpm是一种新起的包管理器，它具有以下优势：

- 目前，安装效率高于npm和yarn的最新版
- 极其简洁的`node_modules`目录
- 避免了开发时使用间接依赖的问题，只能使用直接安装的依赖
- 能极大的降低磁盘空间的占用

```shell
# 全局安装
npm install pnpm --global
# 查看版本
pnpm -v
# pnpx 使用本地CLI工具
pnpx vue-cli-service serve
```



# 如何发布包？

1. 移除淘宝镜像源，如果有

   ```shell
   npm config delete registry
   ```

2. 到npm官网注册一个账号，并完成邮箱认证

3. 本地使用npm命令进行登录

   ```shell
   # 登录
   npm login
   # 查看当前登录的账号
   npm whoami
   # 注销
   npm logout
   ```

4. 创建工程根目录，并使用`npm int`初始化，确认初始化的配置，确认包名，包名不能有大写字母
   包名即使发布在npm上的名称，也是安装依赖时的名称

   ```json
   {
     "name": "test-nodejs"
   }
   ```

5. 在入口文件中导出提供其他开发者使用的功能

   ```js
   module.exports = {
     test: () => {
       console.log('test')
     }
   }
   ```

6. 开发、确定版本、发布包

   ```shell
   npm publish
   ```

7. 安装依赖

   ```shell
   npm install test-nodejs
   ```



# 版本控制`nvm`

nvm并非包管理器，它是用于管理多个node版本的工具
因为在实际的开发中，可能会出现多个项目分别使用的是不同的node版本，nvm就是用于切换版本的一个工具
[Github地址](https://github.com/coreybutler/nvm-windows)

```shell
# 指定安装 18.19.1 版本的node
nvm install 18.19.1
# 指定卸载某个版本
nvm uninstall 14.21.3
# 显示所有可用的node版本
nvm list available
# 显示已安装的版本
nvm list
# 使用某个版本
nvm use 18.19.1
# 查看nvm的文件位置
nvm root
```



# IP 地址和 端口号



## IP 地址

IP 地址由 4 部分数字组成，每部分数字对应8位二进制数字，各部分之间用小数点分开，例如：`211.152.65.112`
Internet上的每台主机都分配了一个专门的地址，是唯一的；



## 端口号

所有需要联网通信的应用程序都必须要占有一个端口号
端口号用来定位服务器（主机）上具体的应用程序，作用是表示一台计算机中的特定进程所提供的服务
端口号的范围在 0 ～ 65535；

```javascript
// 加载 http 核心模块：
var http = require('http')
// 加载 文件操作 核心模块：
var fs = require('fs')
// 使用createServer（）方法创建一个 web 服务器：
var server = http.createServer()
// 注册 request 请求事件，当有客户端请求过来时，自动触发事件，然后执行第二个参数（回调函数）：
// 回调函数中接受两个参数：request、response

// request（请求对象）：可以获取客户端的一些请求信息，例入请求路径；

// response（响应对象）：可以给客户端发送响应的内容；
// response（响应对象）有一个方法：response.write（），可以给客户端发送响应数据；
// response.write（）可以使用多次，但最后一定要使用 end 来结束响应，否则客户端会一直等待！
server.on('request', function(request, response) {
console.log('收到客户端请求了！')
console.log('请求路径是：' + request.url)

  // response.write('Hello')
// response.write('Node.js')
// // 告诉客户端，响应已结束，可以呈现给用户了
// response.end()

// 更简单的书写，发送响应数据的同时，结束响应；
// 给客户端发送的响应数据只能是二进制数据或者字符串；
// response.end('Hello,Node,js')


// 解决向客户端发送响应数据时出现乱码的情况
// 告诉浏览器按照 utf-8 编码格式去解析响应数据；
response.setHeader('Content-Type', 'text/html;charset=utf-8')
// 在 http 协议中，Content-Type 就是告诉客户端收到的数据是什么类型的


// 根据请求路径的不同，响应所不同的数据：

// 1、获取请求路径
// 请求路径都是以 / 开头的
// request.url 获取的是端口号后面的那一部分路径
var url = request.url
// 2、判断路径，处理请求
if (url === '/') {
   response.end('<h2 style="color:red">这个页面是：Index Page</h2>')
} else if (url === '/login') {
   response.end('<h4>登录页面：Login Page</h4>');
} else if (url === '/casePage') {
   fs.readFile('./resource/index.html',function(error,data){
      if(error){
        response.end('<h3 style="color:red">没有读取到文件数据</h3>')
      }else{
        response.end(data.toString())
      }
   })
} else {
   response.end('Not Found! Is Error Page')
}
})

// 绑定端口号，启动服务器：
server.listen(8000, function() {
  console.log('服务器已启动，可以通过 http://127.0.0.1:8000 来进行访问')
})
```



# 页面渲染

在网页中，根据需求不同，分为`客户端渲染`和`服务端渲染`
一般都是根据场景和需求，两者结合起来使用；



## 客户端渲染

一般请求两次以上，客户端渲染不利于SEO搜索引擎优化，但客户端的异步渲染体验感更好（不刷新页面）

1. 第一次请求拿到的是静态的页面数据；

   此时客户端收到了服务器响应的页面数据；
   
   从上到下依次解析，如果发现`AJAX`请求或带有`src`和`href`属性的标签，则再次发送新的请求

2. 第二次请求拿到的是动态数据

   此时收到了服务器响应的`AJAX`响应数据
   
   模版引擎开始渲染页面上的动态数据



## 服务端渲染

一次响应完整的页面给客户端：直接在服务端用模版引擎把页面渲染好
服务端渲染是可以被爬虫抓取到的；



# 客户端重新定向

一般用于表单提交后，页面自动跳转到指定页面的url地址

1. 设置响应的状态码

   第一步临时重定向，将响应的状态码设置为 `302`
   
   ```javascript
   response.statusCode = 301 // 永久重定向
   response.statusCode = 302 // 临时重定向
   ```

2. 设置响应头的`Location`属性

   在响应头中通过设置Location属性来告诉客户端要重定向的页面地址
   
   ```javascript
   response.setHeader('Location','urlAddress')
   ```



# Socket

网络上的两个程序通过一个**双向**的通信连接实现数据的交换，连接的一端称为一个 Socket 



# Express

> [Express 中文网](https://www.expressjs.com.cn/)
>
> 基于 [Node.js](https://nodejs.org/en/) 平台，快速、开放、极简的 Web 框架
>
> 原生的 http 核心模块在某些方面不足以满足开发需求；
>
> 使用 express 提高效率，代码更统一；



#### 安装

> ```shell
> npm install express --save
> ```



#### 基本语法

> ```javascript
> //加载express模块；
> var express = require('express');
> //创建服务器应用程序；
> var app = express();
> //当请求路径是‘/’，提交方法是‘get’时，触发callback回调函数；
> app.get('/', function(request, response) {
> 	response.send(`Hello World!`)
> })
> 
> //开启监听8000端口服务；
> app.listen(8000, function() {
> 	console.log('server is beging')
> })
> ```



#### 基本路由

> 路由处理，不同的请求对应不同的处理：
>
> 请求方法
>
> 请求路径
>
> 请求的回调函数



###### get

> 当以 ‘GET’的方式请求 ‘/index’路径时，执行对应的处理函数；
>
> ```javascript
> //加载express模块；
> var express = require('express');
> //创建服务器应用程序；
> var app = express();
> 
> app.get('/index',(request,response)=>{
>   response.send('Hello World !');
> })
> ```



###### post

> 当以 ‘POST’的方式请求 ‘/index’路径时，执行对应的处理函数；
>
> ```javascript
> //加载express模块；
> var express = require('express');
> //创建服务器应用程序；
> var app = express();
> 
> app.post('/index',(request,response)=>{
>   response.send('Submit Method Is POST !');
> })
> ```



#### 静态资源服务

> 可以直接访问该指定目录下的所有资源；
>
> ```javascript
> //加载express模块；
> var express = require('express');
> //创建服务器应用程序；
> var app = express();
> //这样可以通过 /public/xxx 的url路径访问  public 文件目录下的所有资源；
> app.use('/public/',express.static('./public/'));
> ```



#### 使用art-template

[使用手册](http://aui.github.io/art-template/express/)



###### 安装

> ```shell
> npm install art-template --save
> npm install express-art-template --save
> ```



###### 配置

> ```javascript
> // 加载express模块；
> var express = require('express');
> // 创建服务器应用程序；
> var app = express();
> 
> // 配置使用 art-template 模版引擎；
> // 第一个参数 ‘art’ 表示渲染 ‘.art’ 格式文件时，才会使用 art-tempalte 模版引擎去渲染页面；
> // 
> app.engine('art', require('express-art-template'));
> ```



###### 使用

> ```javascript
> // 语法
> response.render('template',{data});
> // express 为 art-template 模版引擎提供了一个 render()方法；
> ```

[^template]:要渲染的页面，路径会默认去项目中的 `views` 目录中寻找该页面文件；
[^data]:模版引擎要渲染的动态数据；

> ```javascript
> app.get('/index', function (request, response) {
>     // express 为 art-template 配置了一个 response.render() 方法来渲染页面；
>     // 默认会去 views 目录中查找 index.art 页面文件;
>     response.render('index.art', {
>        user: {
>          name: 'aui',
>          tags: ['art', 'template', 'nodejs']
>        }
>     })
> })
> ```



#### 获取POST请求体数据

> [body-parser](https://www.npmjs.com/package/body-parser)
>
> express提供的 request.query 只能获取`GET`请求体的查询字符串数据；
>
> express中没有获取表单 `POST`请求体的API，需要使用第三方包`body-parser`；



###### 安装

> ```shell
> npm install body-parser --save
> ```



###### 配置

> ```javascript
> var express = require('express')
> //加载 body-parser 模块；
> var bodyParser = require('body-parser')
> var app = express()
>  
> app.use(bodyParser.urlencoded({ extended: false }))
> app.use(bodyParser.json())
> ```



###### 使用

> ```javascript
> app.post('/submit',function(request,response){
>   //与 ‘GET’ 请求时，express自带的request.query一样都能拿到表单数据对象；
>   var formData = request.body;
> })
> ```



#### 重定向

> ```javascript
> response.redirect('/');
> 
> //原生 http 实现方式
> response.statusCode = 302;
> response.setHeader('Location','/');
> ```



#### 包装路由

> 一般开发时，为了让代码看起来美观简洁，每个文件都有专门的功能；
>
> 将客户端的路由请求处理放在单独的文件中；
>
> ```javascript
> // router.js
> // express 提供了专门包装路由的方法：
> var router = express.Router();
> 
> // 请求主页面；
> router.get('/', function(request, response){
>   	response.render('index.html');
> })
> 
> // 导出 router 路由容器；
> module.exports = router;
> ```
>
> ```javascript
> // app.js
> //路由请求处理请查看 ‘router.js’ 文件；
> var router = require('./router.js');
> 
> // 将 路由容器挂载到 app 服务上；
> app.use(router);
> ```





#### express-session

> 主要用于记录登录状态的第三方模块；
>
> 默认`session`数据是内存存储，服务器一旦重启就会丢失；



###### 下载

> ```shell
> npm install express-session --save
> ```



###### 配置

> ```javascript
> // 加载 express-session 模块；记录登录状态；
> var session = require('express-session');
> // 配置模块；
> app.use(session({
> // 配置加密字符串，会在原有的加密基础上加上这个字符串后再次加密；
> 	secret: 'keyboard cat',
> 	resave: false,
> // 是否每次刷新初始化页面都添加发送一个识别钥匙；
> 	saveUninitialized: true
> }));
> ```



###### 使用

> ```javascript
> // 添加
> request.session.user = userdata;
> response.redirect('/');
> 
> // 获取
> console.log(request.session.user);
> ```



# 数据加密

> 为保证用户数据的安全性和隐私性；



#### md5

> 数据加密第三方模块；



###### Install

> ```shell
> npm install md5 --save
> ```



###### Use

> ```javascript
> // 加载数据加密第三方模块;
> var md5 = require('md5');
> 
> var privacyData = md5('要加密的数据');
> ```



#### json web token

> 是实现`token`技术的一种解决方案；
>
> 由三部分组成： `header(头)`、`payload(载体)`、`signature(签名)`；



###### Install

> ```shell
> npm install jsonwebtoken --save
> ```



###### Use

> 加载模块；
>
> ```javascript
> const jwt = require('jsonwebtoken')
> ```
>
> 语法：
>
> ```javascript
> jwt.sign(payload, secretOrPrivateKey, [options, callback])
> ```
>
> [^payload]:加密的载体，可以是`Object`、`buffer`或`String`；
> [^secretOrPrivateKey]:加密私钥；
>
> ```javascript
> // 对称加密；
> const token = jwt.sign(
>   {
>     username: 'Kein',
>     password: '123456'
>   },'secretKeys')
> 
> // 解码；
> const decode = jwt.verify(token,'secretKeys')
> // decode = {username: 'Kein',password: '123456'};
> ```





# nodemon

> 第三方命令行工具`nodemon`，可以帮助解决频繁修改代码重启服务器的问题；
>
> `nodemon`是一个基于 Node.JS 开发的第三方命令行工具，需要 npm 下载安装；
>
> ```shell
> npm install nodemon --global
> ```

> ```shell
> # Mac 电脑上安装出现权限不够的问题：
> # The operation was rejected by your operating system.
> # npm ERR! It is likely you do not have the permissions to access this file as the current user.
> # 在命令前面加上 sudo，则为切换为有权限的用户再执行安装；
> sudo npm install nodemon --global
> ```

> 安装完成后，用 nodemon 代替 node 来启动服务；
>
> 只要`Ctrl + s`保存文件，服务会自动重启；
>
> ```shell
> nodemon app.js
> ```





# url

[Official Website API](http://nodejs.cn/api/url.html)

> Node.JS 中的路径操作核心模块；
>
> ```javascript
> const url = require('url');
> ```



#### url.parse()

> 将路径解析成为一个方便操作的**对象**；
>
> url.parse([^urlString], [^parseQueryString])
>
> ```javascript
> let parseUrl = url.parse(request.url, true);
> ```

[^urlString]:要解析的 URL 字符串；
[^parseQueryString]:布尔值，为 true 时，表示将**查询字符串**转为一个对象；



###### .pathname

> 单独获取不包含**查询字符串**的路径部分，不包含`?`之后的内容；
>
> ```javascript
> let pathName = parseUrl.pathname;
> ```



###### .query

> 单独获取**查询字符串**的数据；
>
> ```javascript
> let formData = parseUrl.query;
> ```





# Path

> Node.JS 中的路径操作核心模块
>
> ```javascript
> // 直接在文件中加载引用；
> const path = require('path');
> ```



#### path.basename()

> 获取一个路径中的文件的名字（默认包含后缀扩展，例如 `index.js` ）；



#### path.dirname()

> 获取一个路径中的目录部分；



#### path.extname()

> 获取一个路径中的文件的后缀扩展名部分；



#### path.parse()

> 把一个路径转换成一个对象；
>
> ```javascript
> var path = require('path');
> var dealPath = path.parse('c:/user/application/project/app.js');
> // 此时 dealPath 是被模块方法转换成了一个对象；
> dealPath:{
>     root: 'c:/',
>     dir: 'c:/user/application/project',
>     base: 'app.js',
>     ext: '.js',
>     name: 'app'
> }
> ```



#### path.join()

> 建议需要进行路径拼接时，使用`path.join()`方法，避免手动拼接出错；



#### path.isAbsolute()

> 判断一个路径是否是绝对路径；



# Node.JS 中的其他成员

> 在每个文件中，除了 `require()` 和 `exports` 等模块相关API之外，还有两个特殊成员；
>
> 在文件操作中，相对路径是不太可靠的；
>
> 因为在 Node 中文件操作的路径是相对于执行 Node 命令所在目录的路径；
>
> 建议在文件操作中统一使用***动态的绝对路径***：`__dirname`或者`__filename`；



#### __dirname

> 动态地获取当前文件模块所属目录的绝对路径；
>
> ```javascript
> // 当一个文件的目录为: /Users/zoukai/Desktop/NodeJs/Practise/blog/app.js
> console.log(__dirname);
> // __dirname = /Users/zoukai/Desktop/NodeJs/Practise/blog
> ```



#### __filename

> 动态地获取当前文件模块的绝对路径；
>
> ```javascript
> // 当一个文件的目录为: /Users/zoukai/Desktop/NodeJs/Practise/blog/app.js
> console.log(__filename);
> // __filename = /Users/zoukai/Desktop/NodeJs/Practise/blog/app.js
> ```

[^补充]:require()加载模块中的路径标识和文件操作的路径标识不一样，不受影响；
