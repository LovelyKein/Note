## Node.js ？

>Node.js 不是一门语言，不是库，不是框架：
>
>一个基于Chrome V8 引擎的 JavaScript 运行环境（平台）；
>
>简单来说就是 Node.js 可以解析和执行 JavaScript 代码，可以代替浏览器；
>
>使用`C++`语言开发；





## 特性

>non-blocking I/O model（非阻塞 IO 模型，异步）；
>
>lightweight and efficient（轻量和高效）；





## 能做什么？



#### 搭建 Web 服务器后台

> ```javascript
> //加载 http 核心模块：
> var http = require（‘http’）；
> //使用createServer（）方法创建一个 web 服务器：
> var server = http.createServer（）；
> //注册 request 请求事件，当有客户端请求过来时，自动触发事件，然后执行第二个参数（回调函数）：
> server.on（‘request’，function（request,response）{
>      console.log('收到客户端的请求了');
>      response.end('这是响应的数据内容！');
>   
>      // 解决向客户端发送响应数据时出现乱码的情况：
>     // 告诉浏览器按照 utf-8 编码格式去解析响应数据
>      response.setHeader('Content-Type', 'text/html;charset=utf-8');
>     // 在 http 协议中，Content-Type 就是告诉客户端收到的数据是什么类型的；
> }）
> //绑定端口号，启动服务器：
> server.listen（8000，function（）{
>      console.log('服务器已启动，可以通过 http://127.0.0.1:8000/ 来进行访问');
> }）
> ```



#### 命令行工具

* npm（node）
* git（C 语言）
* hexo（node）



#### 运行 JavaScript 代码

> 在文件位置打开终端，输入：`node FileName.js`；



#### REPL

> 在终端中以Node.JS来运行JavaScript代码；
>
> ```javascript
> node + enter(回车键)；
> ```





## Node 中的 JavaScript



#### ECMA Script

> Node 中的 JavaScript 没有 DOM、BOM；



#### 核心模块

> 核心模块本质也是文件；
>
> 核心模块已经被编译到二进制文件中了，只需要直接按模块名来进行加载；
>
> Node 为 JavaScript 提供了很多服务器级别的 API ；
>
> 这些 API 绝大多数都被包装到了一个具名的核心模块中；



###### 使用

> 使用 require（）方法加载需要的核心模块：
>
> ```javascript
> var fs = require('fs');/*文件操作核心模块*/
> var http = require('http');/*http服务核心模块*/
> var url = require('url');/*路径处理核心模块*/
> ```



#### 第三方模块

> 第三方提供的功能模块；
>
> 要先 npm 下载模块，再通过require()方法引入；



###### 模块查找原则

> 默认先找项目根目录下的`node_modules`文件；
>
> 再找到里面的第三方模块；
>
> ```javascript
> //文件路径；
> node_modules/art-template/package.json.main
> ```

> 按照原型链的方式依次往上找，直到磁盘根目录还找不到，就报错；

> ```javascript
> // 模版引擎第三方模块；
> var template = require('art-template');
> // POST请求体表单数据处理第三方模块;
> var bodyParser = require('body-parser');
> ```



#### 自定义模块

> 用户自己写的带有特定功能的文件；
>
> ```javascript
> var target = require(‘JavaScript 文件的相对路径’)；
> ```
>
> 相对路径前缀要加`./`
>
> 上一级的文件要加`../`;
>
> ```javascript
> var target = require('./target.js');
> target.add(22,23);
> console.log(target.name);
> console.log('我是 source 文件，我引入了 target 文件的数据并使用');
> ```

> ```javascript
> //./target.js文件；
> exports.add = function(x,y){
> console.log(x+y);
> }
> exports.name = '邹凯';
> ```



#### 作用域

> Node 中没有全局作用域，只有模块作用域（文件作用域;
>
> 两个模块的变量和方法不会冲突；





## IP 地址和 端口号



#### IP 地址

>IP 地址由 4 部分数字组成，每部分数字对应8位二进制数字，各部分之间用小数点分开，例如：`211.152.65.112`
>
>Internet上的每台主机都分配了一个专门的地址，是唯一的；



#### 端口号

>所有需要联网通信的应用程序都必须要占有一个端口号；
>
>端口号用来定位服务器（主机）上具体的应用程序，作用是表示一台计算机中的特定进程所提供的服务；
>
>端口号的范围在 0 ～ 65535；
>
>```javascript
>// 加载 http 核心模块：
>var http = require('http');
>// 加载 文件操作 核心模块：
>var fs = require('fs');
>// 使用createServer（）方法创建一个 web 服务器：
>var server = http.createServer();
>// 注册 request 请求事件，当有客户端请求过来时，自动触发事件，然后执行第二个参数（回调函数）：
>// 回调函数中接受两个参数：request、response
>
>// request（请求对象）：可以获取客户端的一些请求信息，例入请求路径；
>
>// response（响应对象）：可以给客户端发送响应的内容；
>// response（响应对象）有一个方法：response.write（），可以给客户端发送响应数据；
>// response.write（）可以使用多次，但最后一定要使用 end 来结束响应，否则客户端会一直等待！
>server.on('request', function(request, response) {
>    console.log('收到客户端请求了！');
>    console.log('请求路径是：' + request.url);
>    
>       // response.write('Hello');
>    // response.write('Node.js');
>    // // 告诉客户端，响应已结束，可以呈现给用户了；
>    // response.end();
>
>    // 更简单的书写，发送响应数据的同时，结束响应；
>    // 给客户端发送的响应数据只能是二进制数据或者字符串；
>    // response.end('Hello,Node,js');
>
>
>    // 解决向客户端发送响应数据时出现乱码的情况：
>    // 告诉浏览器按照 utf-8 编码格式去解析响应数据；
>    response.setHeader('Content-Type', 'text/html;charset=utf-8');
>    // 在 http 协议中，Content-Type 就是告诉客户端收到的数据是什么类型的；
>
>
>    // 根据请求路径的不同，响应所不同的数据：
>
>    // 1、获取请求路径；
>    // 请求路径都是以 / 开头的；
>    // request.url 获取的是端口号后面的那一部分路径；
>    var url = request.url;
>    // 2、判断路径，处理请求
>    if (url === '/') {
>        response.end('<h2 style="color:red">这个页面是：Index Page</h2>');
>    } else if (url === '/login') {
>        response.end('<h4>登录页面：Login Page</h4>');
>    } else if (url === '/casePage') {
>        fs.readFile('./resource/index.html',function(error,data){
>           if(error){
>             response.end('<h3 style="color:red">没有读取到文件数据</h3>');
>           }else{
>             response.end(data.toString());
>           }
>        })
>    } else {
>        response.end('Not Found! Is Error Page');
>    }
>})
>
>// 绑定端口号，启动服务器：
>server.listen(8000, function() {
>    console.log('服务器已启动，可以通过 http://127.0.0.1:8000 来进行访问');
>})
>```





## 页面渲染

> 在网页中，根据需求不同，分为`客户端渲染`和`服务端渲染`；
>
> 一般都是根据场景和需求，两者结合起来使用；



#### 客户端渲染

> 一般请求两次以上；
>
> 客服端渲染不利于SEO搜索引擎优化；
>
> 但客户端的异步渲染体验感更好（不刷新页面）；

1. 第一次请求拿到的是静态的页面数据；

   > 此时客户端收到了服务器响应的页面数据；
   >
   > 从上到下依次解析，如果发现`AJAX`请求或带有`src`和`href`属性的标签，则再次发送新的请求；



2. 第二次请求拿到的是动态数据；

   > 此时收到了服务器响应的`AJAX`响应数据；
   >
   > 模版引擎开始渲染页面上的动态数据；



#### 服务端渲染

> 一次响应完整的页面给客户端：直接在服务端用模版引擎把页面渲染好；
>
> 服务端渲染是可以被爬虫抓取到的；





## 客户端重新定向

> 一般用于表单提交后，页面自动跳转到指定页面的url地址

1. 设置响应的状态码

   > 第一步临时重定向，将响应的状态码设置为 `302`；
   >
   > ```javascript
   > response.statusCode = 302;//临时重定向；
   > response.statusCode = 301;//永久重定向；
   > ```



2. 设置响应头的`Location`属性

   > 在响应头中通过设置Location属性来告诉客户端要重定向的页面地址；
   >
   > ```javascript
   > response.setHeader('Location','urlAddress');
   > ```





## Socket



#### Socket ?

> 网络上的两个程序通过一个**双向**的通信连接实现数据的交换；
>
> 连接的一端称为一个 Socket ；





## 模块系统



#### 模块化?

> 文件（局部）作用域；
>
> 文件通信规则；



#### CommonJs

> 在Node中的JavaScript还有一个很重要的概念：模块系统；

* 模块作用域；
* 使用`require()`方法来加载模块；
* 使用`exports`接口对象来导出模块中的成员；



#### require()

> require（）方法既可以加载 Node 中的核心模块；
>
> 也可以加载引入 JavaScript 文件；
>
> ```javascript
> //语法
> var moduleName = require('module');
> ```



###### 作用

> 执行被加载模块中的代码；
>
> 得到被加载模块中的exports导出的接口对象；



###### 加载规则

> **优先从缓存加载**；
>
> 如果一个模块之前被加载过，再次执行`reauire()`方法不会再加载；
>
> 此时缓存中已经有了此模块；
>
> 再次执行`reauire()`方法会拿到这个模块导出的`exports`接口对象；



#### exports

> 接口对象；
>
> Node中是模块作用域，默认文件中所有的成员只在当前文件模块有效；
>
> 如果希望可以被其他模块访问，就需要把要公开的元素挂载到exports接口对象上；
>
> exports 是一个空对象，将要导出的数据写在 exports 对象身上；



###### 导出多个成员

> ```javascript
> //将要导出的成员写在expoers接口对象上；
> exports.a = 'Kein';
> exports.b = ['22','19','52','13','14'];
> exports.c = {
>      name : 'Kein',
>      agr : 22,
>      sex : 'male'
> };
> exports.print = function(){
>      console.log(this.c.name);
> };
> ```



###### 导出单个成员

> 此时将对`module.exports`接口对象重新赋值；
>
> ```javascript
> module.exports = 'Kein';
> 
> //module.exports也可以导出多个成员：
> module.exports = {
>     name : 'Kein',
>     agr : 22,
>     sex : 'male',
>     print : function(){
>        console.log(this.name + this.age);
>     }
> };
> ```



###### 原理解析

> 每个模块都有一个`module`对象；`module`对象中有一个`exports`对象；
>
> `exports`是`module.exports`的一个引用；
>
> 模块真正导出的是`module.exports`,而不是`exports`；
>
> 模块源码里有这样一行代码，为了方便书写：
>
> ```javascript
> var exports = module.exports;
> ```
>
> ```javascript
> console.log(exports === module.exports);//true
> 
> exports.name = 'Kein';
> //等价于
> module.exports.name = 'Kein';
> ```





## npm

> `npm`node package manager
>
> [npm Official Website](https://www.npmjs.com)



#### 查看版本

> ```shell
> npm --version
> ```



#### 升级版本

> ```shell
> npm install --global npm
> ```



#### 常用指令



###### 删除模块

> ```shell
> npm uninstall art-tempalte --save
> ```



###### 查看npm使用帮助

> ```shell
> npm --help
> ```



###### 清除缓存

> ```shell
> npm cache clear --force
> ```



#### --save

> 建议安装第三方模块的时候加上`--save`；
>
> 此时第三方模块的信息就会被`package.json`文件的`dependencies`选项所记录；
>
> ```shell
> #安装 art-template 模版引擎第三方模块；
> npm install art-template --save
> ```



#### cnpm

> npm存储模块（包）的服务器在国外，有时候安装模块的时候会很慢，出现问题；
>
> 淘宝的开发团队把npm在国内做了一个备份；
>
> ```shell
> #任意目录下；
> # --global 表示安装到全局，而非当前文件目录；
> # --global 不能省略，否则不管用；
> npm install --global cnpm
> 
> #接下来安装模块（包）的时候把 npm 换成 cnpm 就行了；
> cnpm install jquery --save
> ```



#### npm install

> 如果项目中的`node_modules`文件丢失，通过` npm install `方式重新加载；
>
> 根据`package.json`文件里的`dependencies`选项所记录的第三方模块信息；
>
> ```shell
> npm install
> ```





## package.json

> 建议每个项目的根目录下都要有一个`package.json`文件；



#### package.json文件

> 项目（包）的描述文件，就像产品的说明书一样，存有项目的各类信息；
>
> ```shell
> #通过 npm 自动创建package.json文件；
> npm init
> #或者
> npm init --yes
> ```



#### dependencies选项

> 记录了项目所加载依赖的第三方模块的信息；
>
> ```json
>  "dependencies": {
>     "art-template": "^4.13.2"
>   }
> ```





## Express

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





## 数据加密

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





## nodemon

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





## url

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





## Path

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





## Node.JS 中的其他成员

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
