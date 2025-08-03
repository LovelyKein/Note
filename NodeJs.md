# NodeJS?

Node不是一门语言，不是框架，而是一个基于`Chrome V8`引擎的JavaScript运行环境（平台）
简单来说就是 Node.js 可以解析和执行JavaScript代码，使用`C++`语言开发
特性：

- non-blocking I/O(Input/Output) model（非阻塞 IO 模型，异步）
- lightweight and efficient（轻量和高效）

Node中的全局对象是`global`

```js
console.log(global)
```

安装`@types/node`第三方库，方便开发时的语法提示

```shell
npm i @types/node -D
```



# `ES Module`支持

node14版本后开始正式支持`ESM`模块规范，16版本后完全稳定的`ESM`支持，与`CommonJS`并存

如何开启使用`ESM`模块化？

- 全局开启：在`package.json`中添加`"type": "module"`，所有`.js`后缀文件默认视为`ESM`规范
- 局部开启：使用`.mjs`扩展名，无需设置 `"type": "module"`，则该文件使用`ESM`规范

```js
// 导入模块
import fs from 'fs'
import { debounce } from './utils.js'
import * as tools from './tools.js'

// 导出
export const data = 'hello'
export default () => console.log('default export')
```

使用`CommonJS`规范

- 全局开启：若未设置 `"type": "module"`，默认支持`.js`后缀文件，也可设置`"type": "commonjs"`
- 局部开启：使用`.cjs`文件后缀，则强制该文件为`CommonJS`规范

```js
// 导入模块
const fs = require('fs')
const { customFunction } = require('./utils.js')

// 导出模块
exports.data = 'hello'
module.exports = function() { console.log('export') }
```

可以混合使用两种规范吗？Yes！

```js
// ESM 中混入 CJS 模块
import { createRequire } from 'module'
const require = createRequire(import.meta.url) // 需借助 createRequire 兼容
const cjsModule = require('./cjs-module.cjs')
```

```js
// CJS 中混入 ESM 模块（需异步）
async function loadESM() {
  const esmModule = await import('./esm-module.mjs')
  esmModule.default()
}
```

**请注意！在`ES Module`规范中，无法直接使用`__dirname`和`__filename`**

需借助`import.meta.url`，它返回当前模块文件的url，格式为`file:///path/to/module.js`

![image-20250727180452044](./assets/image-20250727180452044.png)

```js
import { dirname } from 'path'
import { fileURLToPath } from 'url'
const filename = fileURLToPath(import.meta.url) // 文件url转为系统路径
console.log(filename, 'filename')
const dir = dirname(filename) // 得到目录路径
console.log(dir, 'dirname')
```



# `CommonJS`规范

**在node中，有且仅有一个入口文件（启动文件）**
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
     (function(module, exports, __dirname, __pathname) {
       module.exports = {}
       var exports = module.exports
       /** 模块中的代码 **/
       
       // 真正导出的是 module.exports 对象
       return module.exports
     })()
     ```

- 为了避免反复加载同一个模块，node开启了模块缓存，如果模块已经被加载过了，则会自动使用之前的导出结果
  通过查询`require.cache`中有没有该模块判断是否有缓存

> [!NOTE]
>
> `CommonJS`是同步执行的，必须要等到加载完文件并执行完代码后才能继续向后执行



## 导入`require()`

`require`既可以加载node中的核心模块，也可以加载引入js文件
其作用是：执行被加载模块中的代码，并得到被加载模块中的`exports`导出

`require()`中可以填写：

- 绝对路径
  ```js
  require('/Users/kein/Documents/Kyle/study/Node/index.js')
  ```

- 相对路径
  ```js
  require('./modules/test.js')
  ```

  如果省略后缀名不写，会自动补全去查找，按`.js`、`.json`、`.node`、`.mjs`补全
  举个例子，当执行 `require('./utils')` 时，查找顺序为：

  ```
  ./utils.js
  ./utils.json
  ./utils.node
  ./utils.mjs
  ```

  若指定的路径是个目录，会按下面的步骤处理：

  1. 查看该目录下是否有`package.json`文件，并且其中指定了入口文件`main`字段，例如：

     ```json
     {
       "name": "my-package",
       "main": "lib/index.js"
     }
     ```

     这种情况下，Node会加载`./utils/lib/index.js`文件

  2. 没有`package.json`和`main`字段，尝试加载目录下的`index.js`、`index.json`或者`index.node`文件

- 模块名称

  1. 查找是否有内置模块，如`path`、`fs`等
  2. 查找有没有`node_modules/path.js`文件
  3. 查找有没有`node_modules/path/入口文件`
  4. 依次查找上级目录中的`node_modules`目录，直到根目录
  5. 转换成绝对路径，导入模块

  ```js
  // 导入核心模块
  const path = require('path')
  // 导入第三方模块
  const axios = require('axios')
  ```

> [!NOTE]
>
> 导入模块的路径最终都会转换成绝对路径



## 导出`module.exports`

node中是模块作用域，默认文件中所有的成员只在当前文件模块有效
如果希望可以被其他模块访问，就需要把要公开的元素挂载到`exports`接口对象上
`exports`初始化时是一个空对象，将要导出的数据写在`exports`对象身上

```javascript
/** 使用 exports 导出 **/
exports.a = 'Kein'
exports.c = {
  name: 'Kein'
}
```

```javascript
/** 同样也可以使用 module.exports 来导出 **/
module.exports = 'Kein'
module.exports = {
  name: 'Kein',
  agr: 22,
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

// 模块文件中的 this 等于 exports
console.log(this === exports) // true
console.log(this === module.exports) // true

// 但是当我将 module.exports 或者 exports 直接复制修改后
module.exports = { count: 0 }
console.log(this === module.exports) // false
console.log(this === exports) // true

console.log(module.exports) // { count: 0 }
console.log(exports) // { name: 'Kein' }
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
  }
}
```

运行`package.json`文件中`scripts`中的脚本

```shell
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



## 语义版本

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
       "dev": "export NODE_ENV=development && node index.js"
     }
   }
   ```

   为了避免不同系统的设置方式的差异可以使用第三方库`cross-env`对环境变量进行设置

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



# 内置模块

## `os`

提供了与操作系统进行交互的实用工具，可以获取有关计算机系统的信息

```js
import os from 'os'
// 返回操作系统换行符，在不同操作系统下的换行符可能不一样, \n 或 \r\n
console.log(os.EOL)
// 返回操作系统的 CPU 架构，如 x64、arm64 等
console.log(os.arch())
// 返回操作系统的平台，如 darwin、win32、linux 等
console.log(os.platform())
// 返回操作系统的 CPU 信息，包括型号、速度、核心数等，格式为数组
console.log(os.cpus().length)
// 返回操作系统的可用内存大小，单位为字节(byte)
console.log(os.freemem() / 1024 / 1024 + ' MB')
// 返回操作系统的总内存大小，单位为字节(byte)
console.log(os.totalmem() / 1024 / 1024 + ' MB')
// 返回操作系统的网络接口信息，格式为对象
console.log(os.networkInterfaces())
// 返回操作系统的主机名
console.log(os.hostname())
// 返回当前用户的目录路径
console.log(os.homedir())
// 返回操作系统的临时目录路径
console.log(os.tmpdir())
```



## `path`

提供了处理和转换文件路径的实用工具，可跨平台统一路径格式

```js
// 导入
import path from 'path' // EMS
// const path = require('path') // CMJ

/** 路径分隔符常量 **/
// path.sep: 返回当前系统的路径分隔符，输出: \ 或 /
console.log('分隔符:', path.sep)

// path.delimiter: 返回环境变量路径分隔符,如 Windows 为`;`，POSIX 为`:`
process.env.PATH.split(path.delimiter)

/** 路径处理方法 **/
// path.join([...paths]): 拼接路径片段，自动处理多余分隔符和相对路径，可以传递多个参数
path.join('/dir', 'subdir', 'file.txt')
// 输出: /dir/subdir/file.txt (POSIX) 或 \dir\subdir\file.txt (Windows)，分隔符不同

// path.resolve([...paths]): 将一系列路径或路径段解析为绝对路径，直到构造出绝对路径
path.resolve('src', 'index.js') // /current/working/dir/src/index.js (基于当前工作目录)
path.resolve('/foo', 'bar', '../baz') // 输出: /foo/baz

// path.normalize(path): 规范化路径，处理`.`、`..`和多余分隔符
path.normalize('/foo/bar//baz/../qux') // 输出: /foo/bar/qux

/** 路径解析方法 **/
// path.parse(path): 将路径解析为对象，包含根目录、目录、文件名、扩展名等
const parsed = path.parse('/home/user/dir/file.txt')
// 输出: { root: '/', dir: '/home/user/dir', base: 'file.txt', ext: '.txt', name: 'file' }

// path.format(pathObj): 从对象反序列化为路径字符串，与`path.parse`相反
const pathStr = path.format({
  dir: '/home/user',
  base: 'file.txt'
}) // 输出: /home/user/file.txt

/** 相对路径与绝对路径 **/
// path.isAbsolute(path): 判断路径是否为绝对路径
path.isAbsolute('/dir/file.txt') // 输出: true
path.isAbsolute('file.txt') // 输出: false

// path.relative(from, to): 计算从 from 到 to 的相对路径
path.relative('/home/user/dir', '/home/user/other') // 输出: ../other

// path.basename(path[, ext]): 返回路径的最后一部分（文件名或目录名），可指定扩展名以去除
path.basename('/dir/subdir/file.txt') // file.txt
path.basename('/dir/subdir/file.txt', '.txt') // file

// 返回路径的目录部分
path.dirname('/dir/subdir/file.txt') // 输出: /dir/subdir

// 返回路径的扩展名（包括 .），若无扩展名则返回空字符串
path.extname('file.txt') // '.txt'
path.extname('file') // ''
```



## `url`

提供了处理和解析`URL`的工具，Node支持现代浏览器的`URL`构造函数，`WHATWG API (new URL())`

```js
const myUrl = new URL('https://user:pass@example.com:8080/path?query=value#hash') // WHATWG API
myUrl.host // 输出: example.com:8080
myUrl.hostname // 输出: example.com
myUrl.port // 输出: 8080
// ...
```

```js
const url = require('url') // 引入模块
const url = 'https://nodejs.org/docs/latest/api/url.html'

// 解析url字符串为对象
const urlObj = new url.URL(url) // 也可以直接 new URL()
url.parse(url) // 效果和上面一样

// 从url对象生成url字符串
url.format(myURL) // 'https://nodejs.org/docs/latest/api/url.html'

// 根据基础url解析相对路径
url.resolve('https://example.com/a/b', '../c/d') // https://example.com/a/c/d

// 解析查询字符串为对象（通过第二个参数true启用）
const parsedQuery = url.parse('https://example.com?name=John&age=30', true)
parsedQuery.query // { name: 'John', age: '30' }
// 更现代的查询参数处理方式，使用 URL 构造函数：
const myUrl = new URL('https://example.com?name=John&age=30')
myUrl.searchParams.get('name') // 'John'
myUrl.searchParams.append('city', 'New York')
myUrl.searchParams.toString() // 'name=John&age=30&city=New%20York'
```



## `util`

提供了一系列实用工具函数，用于简化常见的编程任务，如对象序列化、回调函数转换、调试输出等

```js
import util from 'util'
import fs from 'fs'

// 将CallBack回调风格的函数转换为返回Promise的函数
const readFile = util.promisify(fs.readFile)
readFile('./example.txt', 'utf8')
  .then(data => console.log(data))
  .catch(err => console.error(err))

// 将Promise函数转换为CallBack回调风格
async function asyncFunction() { return '结果' }
const callbackFunction = util.callbackify(asyncFunction)
callbackFunction((err, result) => {
  if (err) throw err
  console.log(result) // 输出: 结果
})

// 如果value_1和value_2存在深度严格相等，则返回true，否则返回false，判断两值是否严格相等
util.isDeepStrictEqual('value_1', 'value_2')

// 为不同类型的内置对象提供类型检查
util.types.isAnyArrayBuffer()
// 判断是否为arguments对象
function foo() {
  util.types.isArgumentsObject(arguments) // true
}
```



## `stream`

`stream`是Node中处理**大量数据或连续数据**的抽象接口，数据分块处理，无需一次性加载全部内容
而`fs.createReadStream`和`fs.createWriteStream`是继承自`stream`模块实现的文件流操作

**什么是文件流？**
内存数据和磁盘文件数据之间的流动，就是文件流

**流的分类**

- **可读流`Readable`**：如`fs.createReadStream`、`HTTP`请求、`process.stdin`
- **可写流`Writable`**：如`fs.createWriteStream`、`HTTP`响应、`process.stdout`
- **双向流`Duplex`**：同时支持读写，如网络套接字
- **转换流`Transform`**：在读写过程中转换数据，如`zlib`压缩、`crypto`加密

**核心特性**

- **事件驱动**：通过`on('data')`、`on('end')`等监听数据流动
- **背压`Backpressure`**：自动控制数据传输速率，避免内存溢出
- **管道`Pipe`**：通过`stream.pipe()`连接多个流，简化数据处理流程



## `fs`

提供了与文件系统进行交互的能力，支持同步、异步、流式等多种操作方式

> [!NOTE]
>
> 在Node中文件操作的路径是相对于执行Node命令所在目录的路径，默认即项目的根目录

注意事项：

- **异步优先**：避免使用同步方法，如 `readFileSync`，以免阻塞事件循环，除非有需求要同步
- **错误处理**：异步操作都应包含错误处理，`.catch()`或`try/catch`
- **大文件处理**：对于大文件，使用流`createReadStream`和`createWriteStream`，而非一次性读取
- **路径问题**：使用`path`模块处理路径，确保跨平台兼容性

```js
const fs = require('fs')
const path = require('path')
const os = require('os')
```

### 读取文件

```js
const filename = path.resolve(__dirname, './files/word.txt') // 获取文件路径
// 读取文件 CallBack 模式
fs.readFile(filename, (err, data) => {
  if (err) {
    console.log(err)
  } else {
    // console.log(data) // 默认读取的是 Buffer 数据
    console.log(data.toString('utf-8')) // 用 utf-8 编码格式转为字符串
  }
})
// promise 写法
fs.promises.readFile(filename).then(data => {
  console.log(data.toString('utf-8'))
}).catch(err => {
  console.log(err)
})
```

### 写入文件

**注意**：写入文件需要正确路径，目录不存在或错误会导致失败

```js
const filename = path.resolve(__dirname, './files/word.txt') 
const content = 'hello world, hello NodeJs'
fs.writeFile(filename, content, (err) => {
  if (err) {
    console.log(err)
  } else {
    console.log('写入成功')
  }
})
// promise 写法
fs.promises.writeFile(filename, os.EOL + content, {
  encoding: 'utf-8', // 编码格式
  flag: 'a', // 写入模式：w 写入，a 追加
}).then(() => {
  console.log('写入成功')
}).catch(err => {
  console.log(err)
})

// fs.appendFile() 追加文件内容
fs.appendFile(filename, content, (err) => {
  if (err) {
    console.log(err)
  } else {
    console.log('追加成功')
  }
})

// 手写复制文件
const imageFilename = path.resolve(__dirname, './image/portrait.jpg')
const targetFilename = path.resolve(__dirname, './image/portrait_copy.jpg')
fs.promises.readFile(imageFilename).then(data => {
  const buffer = Buffer.from(data)
  fs.promises.writeFile(targetFilename, buffer).then(() => {
    console.log('复制成功')
  }, (err) => {
    console.log(err)
  })
}).catch(err => {
  console.log(err)
})
```

### 删除文件

```js
// 当指定路径没有目标目录或文件时，会执行错误回调
const filename = path.resolve(__dirname, './files/test.js')
fs.promises.unlink(filename).then(() => {
  console.log('删除成功')
}).catch(err => {
  console.log(err)
})
```

### 获取文件信息

```js
// 获取文件信息，可以用来判断文件是否存在
const filename = path.resolve(__dirname, './files/word.txt')
fs.promises.stat(filename).then(stat => {
  console.log(stat.size)
  console.log(stat.isFile()) // true
  console.log(stat.isDirectory()) // falsexx
  console.log(new Date(stat.birthtime).toLocaleString())
}).catch(err => {
  console.log(err)
})

// fs.readdir() 获取目录中的文件和子目录，子目录中文件无法读取到
const dirname = path.resolve(__dirname, './files')
fs.promises.readdir(dirname).then(files => {
  console.log(files)
}).catch(err => {
  console.log(err)
})
```

### 创建/删除目录

```js
// 创建目录，如果目录已存在则不会再创建，执行失败的回调
const dirname = path.resolve(__dirname, './media')
fs.promises.mkdir(dirname).then(() => {
  console.log('创建成功')
}).catch(err => {
  console.log(err.message)
})
// 删除目录，不存在则执行错误回调
fs.promises.rmdir(dirname).then(() => {
  console.log('删除成功')
}).catch(err => {
  console.log(err.message)
})
```

### 流式读取

`fs.createReadStream()`函数返回的是`stream`中`Readable`的子类`ReadStream`的实例

可监听事件：
文件打开`open`、文件读取`data`、暂停读取`pause`、恢复读取`resume`、读取完成`end`、读取错误`error`、文件关闭`close`

```js
const filename = path.resolve(__dirname, './files/largeTxt.txt')
const stream = fs.createReadStream(filename, {
  encoding: null, // 编码格式，默认是 null，返回 Buffer 类型的数据
  highWaterMark: 256, // 一次读取的字节数 byte
  start: 0, // 开始读取的位置
  // end: 1024 * 1024 * 5, // 结束读取的位置
  autoClose: true, // 读取完成后自动关闭文件流，默认 true
})
// 读取事件监听：
```

监听文件读取`data`事件，每次读取`highWaterMark`字节数的数据，`chunk`为读取到的数据
**注意：注册`data`事件后，才会开始读取文件内容**

```js
stream.on('data', chunk => {
  console.log(chunk.toString('utf-8'))
})

// 手动关闭流读取文件，则之后不会再触发`data`事件，会出发`close`事件
stream.close()
stream.on('close', () => {
  console.log('文件关闭')
})
```

### 流式写入

`fs.createWriteStream()`函数返回的是`stream`中`Writable`的子类`WriteStream`的实例

可监听事件：文件打开`open`、写入队列清空`drain`、写入完成`finish`、写入错误`error`...

```js
const writeStream = fs.createWriteStream(filename, {
  encoding: 'utf-8', // 写入文件的编码格式
  flag: 'w', // 写入模式：w 写入，a 追加
  highWaterMark: 128, // 一次最多写入的字节数 byte，默认是 16 * 1024 byte
  start: 0, // 开始写入的位置
  // end: 1024 * 1024 * 5, // 结束写入的位置
  autoClose: true, // 写入完成后自动关闭文件流，默认 true
})
const flag = writeStream.write(os.EOL + 'hello world')
// flag 为 true 表示当前写入的字节数小于 highWaterMark，后续写入的数据不用排队，可以直接写入
// flag 为 false 表示当前写入的字节数大于等于 highWaterMark，后续数据需要排队
console.log(flag) // true
// 所以要特别注意背压问题，因为写入队列是内存中的数据，是有限的

// 告诉写入流后续没有数据了，会触发写入流的 finish 事件，参数为最后的写入数据
writeStream.end(os.EOL + 'hello node')

// 文件写入队列清空事件，表明后续数据不用排队，可以直接写入
writeStream.on('drain', () => {
  // 写入队列清空后再写入数据，可以缓解背压问题
  writeStream.write(os.EOL + 'hello node')
  console.log('写入队列清空')
})
```

缓解背压问题的流式文件复制

```js
const filename = path.resolve(__dirname, './image/portrait.jpg')
const copyFilename = path.resolve(__dirname, './image/portraitCopy.jpg')

const rs = fs.createReadStream(filename, {
  encoding: null,
  highWaterMark: 256,
  start: 0,
  autoClose: true
})
const ws = fs.createWriteStream(copyFilename, {
  encoding: null,
  highWaterMark: 256
})
rs.on('data', (chunk) => {
  // 将读取的内容写入文件，如果 highWaterMark 已满，则暂停读取
  const flag = ws.write(chunk)
  if (!flag) rs.pause()
})
ws.on('drain', () => {
  // 文件写入对列清空，恢复文件读取
  rs.resume()
})
rs.on('end', () => {
  ws.end()
  console.log('copy done')
})
```

使用`pipe`管道高级用法，自动处理背压问题

```js
rs.pipe(ws) // 数据从 rs 流向 ws
rs.on('end', () => {
  console.log('copy done')
})
```

### 转换流

```js
// pipe 管道高级用法
const { Transform } = require('stream')
const fs = require('fs')
const path = require('path')

const filename = path.resolve(__dirname, './files/largeTxt.txt')
const copyFilename = path.resolve(__dirname, './files/largeTxtUppercase.txt')
// 创建一个将文本转为大写的转换流
const upperCaseTransform = new Transform({
  transform(chunk, encoding, callback) {
    this.push(chunk.toString('utf-8').toUpperCase())
    callback()
  },
  flush(callback) {
    console.log('转换完成')
    callback()
  }
})
// 使用管道组合流：读取文件 → 转换为大写 → 写入新文件
fs.createReadStream(filename)
  .pipe(upperCaseTransform)
  .pipe(fs.createWriteStream(copyFilename), {
    end: true
  })
```



## `net`

提供了基于`TCP`协议的网络通信能力，可以实现进程间的通信`IPC`和网络通信`TCP/IP`

```js
import net from 'net'
// 创建一个服务
const server = net.createServer((socket) => {
	console.log('客户端已连接')
	socket.on('data', (data) => {
		const dataStr = data.toString()
		console.log(dataStr)
		const byteLength = Buffer.from(data).byteLength
		console.log(byteLength)
	})
	socket.write('hello client') // 服务端向客户端发送数据
})
// 监听 5000 端口
server.listen(5000, () => {
	console.log('server is running on port 5000')
})
// 创建一个客户端连接
const client = net.createConnection(
	{
		port: 5000,
		host: 'localhost',
	},
	() => {
		console.log('服务器已连接')
    // 向服务端发送消息
		client.write('hello server')
	}
)
```

`net.Socket`是`TCP`套接字或流式进程间通信端点的抽象
`net.createConnection()`会返回一个`net.Socket`，用户可以使用它与服务器进行通信



## `http`

是构建Web服务器和客户端的核心工具，基于`HTTP/1.1`协议实现，建立在`net`模块之上
核心功能：

- **创建`HTTP`服务器**：监听端口，处理客户端请求
- **发起`HTTP`客户端请求**：向其他服务器发送请求
- **流处理**：基于`stream`模块，高效处理大文件和实时数据

```js
const http = require('http')
const path = require('path')
const fs = require('fs')

// 发起客户端请求
const request = http.request(options, callback)

// 创建http服务器
const server = http.createServer(async (req, res) => {
	console.log('请求地址：', req.url)
	// 此处可以用 new URL() 来解析请求的信息，req.method 是请求的方法
	// 根据得到的信息，可以根据请求的路径来响应不同的内容，例如搭建静态资源服务器
	const reqUrl = new URL(req.url, 'http://localhost:5000')
	const pathname = reqUrl.pathname.substring(1) // 去除掉'/'
	const filename = path.resolve(__dirname, 'public', pathname) // 拼接到public目录下的资源
	try {
		const file = await handleFile(filename)
		res.statusCode = 200
		res.end(file)
	} catch (error) {
		res.statusCode = 404
		res.end(error)
	}
})
// 监听端口服务
server.listen(5000, () => {
	console.log('server is running at http://localhost:5000')
})

async function handleFile(filename) {
	try {
		await fs.promises.access(filename)
		const file = await fs.promises.readFile(filename)
		return file
	} catch (error) {
		return '404 not found'
	}
}
```



# `Socket`

在计算机网络中，`Socket`是一个抽象概念，用于实现不同设备或进程间的通信

它是网络编程的基础接口，让应用程序能够通过网络发送和接收数据

`Socket`通信基于{客户端-服务器}的`C/S`模型，主要基于两种传输协议：

- `TCP(Transmission Control Protocol)`：面向连接、可靠、有序的字节流协议
- `UDP(User Datagram Protocol)`：无连接、不可靠、快速的数据报协议

核心特性：`Socket`是**全双工（Full-duplex）**的，即双方可同时发送和接收数据



# `NodeJS`的生命周期

![image-20250729210057145](./assets/image-20250729210057145.png)

- `timers`：当定时器的延迟时间到达后，回调会被放入此阶段的`timers`队列
- `poll`：轮询队列，绝大部分回调都会进入该队列，例如文件的读取、监听用户的请求，运作方式为：
  如果`poll`中有回调，依次执行回调、直到清空队列
  如果`poll`中没有，等待其他队列出现回调，然后进入该队列执行回调
  如果其他队列也没有，则一直等待到出现为止

- `check`：检查阶段，`setImmediate`的回调函数会立即进入这个队列等待运行
- `Promise & nextTick`：在结束该阶段事件循环之前，都要先清空这两个队列中的回调
  在`EMS`规范下，`Promise`优先；在`CMJ`规范下，`nextTick`优先执行

**异步回调的优先级：`ESM`规范下，优先执行清空`Promise`微队列，`CMJ`规范下，优先执行清空`nextTick`**

```js
setTimeout(() => {
  console.log(1)
}, 0)

setImmediate(() => {
  console.log(2)
})

process.nextTick(() => {
  console.log(3)
  process.nextTick(() => {
    console.log(4)
  })
})

console.log(5)

Promise.resolve().then(() => {
  console.log(6)
  process.nextTick(() => {
    console.log(7)
  })
})
// ES Module 规范下输出顺序：5 6 3 7 4 2 1
// CommonJS 规范下输出顺序：5 3 4 6 7 1 2
```



# 操作数据库`Control Database`

在`NodeJS`环境下，使用JS语言对数据库中的数据执行增删改查的`DML`操作



## `Use MySQL`

```shell
npm install mysql2
```

```js
const MySQL = require('mysql2')

// 创建与数据库连接
const connection = MySQL.createConnection({
  host: 'localhost',
  port: 3306,
  user: 'root',
  password: '123456',
  database: 'test',
  multipleStatements: true, // 允许执行多个 SQL 语句
})

// 执行CRUD操作
connection.query(
  'SELECT `name`, `age` FROM `staff`;', // sql语句
  (err, res) => {
  if (err) {
    console.log(err.message)
  }
  console.log('查询结果', res)
})

// 为了防止连接泄漏，应该在查询完成后关闭连接
connection.end()

// 使用SQL预编译模版，避免直接将用户输入拼接到 SQL 语句中，存在 SQL 注入风险
// SQL注入：注入额外的SQL语句到最终查询中，导致结果与预期不符
// 实际开发中，尽量使用MySQL提供的变量预编译模版，而不是直接拼接 SQL 语句
const SQL = `
SELECT * FROM staff
WHERE id = ? AND name = ?;
`
connection.execute(
  SQL,
  [1, 'Kyle'], // 预编译参数，对应模版中的 ? 占位符
  (err, res) => {
    if (err) {
      console.log(err.message)
    }
    console.log('查询结果', res)
  }
)
```

使用`Promise`风格的`API`

```js
const MySQLPromise = require('mysql2/promise')
// 异步执行CRUD操作
async function queryAsync() {
  const connection = await MySQLPromise.createConnection({
    host: 'localhost',
    user: 'root',
    password: '123456',
    database: 'test'
  })
  const [res] = await connection.query('SELECT `name`, `age` FROM `staff`;')
  console.log('查询结果', res)
  connection.end() // 关闭连接
}
queryAsync()
```

使用连接池管理与数据后的连接

```js
// 使用连接池
const pool = MySQL.createPool({
  host: 'localhost',
  port: 3306,
  user: 'root',
  password: '123456',
  database: 'test',
  waitForConnections: true, // 当连接池中的连接数已用尽时，是否等待其他查询完成
  connectionLimit: 10, // 连接池中的最大连接数
  queueLimit: 0, // 连接池中的连接请求队列最大长度，0 表示不限制
  multipleStatements: true, // 允许执行多个 SQL 语句
  // 连接池中的连接在空闲时间超过 idleTimeout 毫秒后，将被自动关闭
  idleTimeout: 1000 * 60 * 60, // 1 小时
  // 连接池中的连接在被创建后，距离上次使用时间超过 maxIdle 毫秒后，将被自动关闭
  maxIdle: 1000 * 60 * 60, // 1 小时
})
// 执行查询
pool.execute(
  SQL,
  [1, 'Kyle'],
  (err, res) => {
    if (err) {
      console.log(err.message)
    }
    console.log('查询结果', res)
  }
)
```



## `ORM`

`Object Relational Mapping`对象关系映射，通过`ORM`框架，可以自动的把程序中的对象和数据库关联
`ORM`框架会隐藏具体的数据库底层细节，让开发者使用同样的数据操作接口，完成对不同数据库的操作，其优势为：

- 开发者不用关心数据库，仅需关心对象
- 可轻易的完成数据库的移植
- 无须拼接复杂的`SQL`语句即可完成精确查询

![image-20250803150735091](./assets/image-20250803150735091.png)

`NodeJS`中的`ORM`框架第三方库有：`sequelize`、`typeorm`、`mongoose`、`Prisma`



# `MD5`加密

对不能明文显示或传输的数据使用哈希算法进行单向加密，其特点为：

- `hash`加密算法的其中一种
- 可以将任何一个字符串，加密成一个固定长度的字符串
- 同样的源字符串加密得到的结果固定一致
- 单向加密，只能加密不能解密

```shell
npm install md5
```

```js
const md5 = require('md5')
const privacyData = md5('要加密的数据')
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



# IP地址和 端口号

## IP 地址

IP 地址由4部分数字组成，每部分数字对应8位二进制数字，各部分之间用小数点分开，例如：`211.152.65.112`
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



# nodemon

第三方命令行工具`nodemon`，可以帮助解决频繁修改代码重启服务器的问题；

`nodemon`是一个基于 Node.JS 开发的第三方命令行工具，需要 npm 下载安装；

```shell
npm install nodemon --global
```

```shell
# Mac 电脑上安装出现权限不够的问题：
# The operation was rejected by your operating system.
# npm ERR! It is likely you do not have the permissions to access this file as the current user.
# 在命令前面加上 sudo，则为切换为有权限的用户再执行安装；
sudo npm install nodemon --global
```

安装完成后，用 nodemon 代替 node 来启动服务；

只要`Ctrl + s`保存文件，服务会自动重启；

```shell
nodemon app.js
```



# __dirname

> 动态地获取当前文件模块所属目录的绝对路径；
>
> ```javascript
> // 当一个文件的目录为: /Users/zoukai/Desktop/NodeJs/Practise/blog/app.js
> console.log(__dirname);
> // __dirname = /Users/zoukai/Desktop/NodeJs/Practise/blog
> ```



# __filename

> 动态地获取当前文件模块的绝对路径；
>
> ```javascript
> // 当一个文件的目录为: /Users/zoukai/Desktop/NodeJs/Practise/blog/app.js
> console.log(__filename);
> // __filename = /Users/zoukai/Desktop/NodeJs/Practise/blog/app.js
> ```
