[art-template官网](http://aui.github.io/art-template/zh-cn/)



## 什么是art-template?

>art-template 是一个简约、超快的模板引擎；
>
>它采用作用域预声明的技术来优化模板渲染速度，并且同时支持 NodeJS 和浏览器；





## art-tempalte的特性

* 拥有接近 JavaScript 渲染极限的的性能；

* 调试友好

  > 语法、运行时错误日志精确到模板所在行；
  >
  > 支持在模板文件上打断点（Webpack Loader）；

* 支持 Express、Koa、Webpack；

* 支持模板继承与子模板；

* 浏览器版本仅 6KB 大小；





## 安装



#### Node中安装

> npm安装；
>
> ```shell
> npm install art-template --save
> ```
>
> Node中`require()`引入再使用；
>
> ```javascript
> const template = require('art-template')
> ```



#### 浏览器中使用

> 需要下载art-template的源码文件，在需要的项目中用<script>标签引入
>
> ```html
> <script src="template-web.js"></script>
> ```





## 语法

> art-template 模版引擎同时支持两种模版语法：`标准语法`和`原始语法`；

* 标准语法

  > 模版更容易读写
  >
  > ```javascript
  > {{ JavaScript表达式 }}
  > ```



* 原始语法

  > 具有更强大的逻辑处理能力
  >
  > ```javascript
  > <%= JavaScript表达式 %>
  > ```



#### 原文输出

> 如果数据中携带`HTML`标签，默认模版引擎不会解析标签，会将其转义后输出；
>
> ```javascript
> //标准语法
> {{ @data }}
> ```



#### 条件判断

> 在模版中可以根据条件来决定显示哪块HTML代码；
>
> ```html
> {{if JudgeConditions}} ...content {{/if}}
> {{if JudgeConditions-1}} ... {{else if JudgeConditions-2}} ... {{/if}}
> 
> //例子
> {{if age>18}}
> 年龄大于18
> {{else if age<15}}
> 年龄小于15
> {{else}}
> 年龄不符合要求
> {{/if}}
> ```



#### 循环

> ```html
> <!--标准语法-->
> {{each target}}
> 	{{$index}} {{$value}}
> {{/each}}
> 
> <!--例子-->
> <ul>
>   {{each user}}
>   <li>
>     {{$value.name}}
>     {{$value.age}}
>     {{$value.sex}}
>   </li>
>   {{/each}}
> </ul>
> ```

[^target]:执行循环渲染所依赖的数据；
[^$index]:当前循环元素的索引值，固定写法；
[^$value]:当前循环元素的数据，固定写法；



#### 子模版

> 使用子模版可以将网站公共区域（头部、底部）抽离到单独的文件中；
>
> ```html
> <!--标准语法-->
> {{include 'sonTemplate'}}
> 
> <!--例子-->
> {{include './header.art'}}
> ```



#### 模版继承

> 模板继承允许你构建一个包含你站点共同元素的基本模板“骨架”;
>
> ```html
> <!--标准语法-->
> {{extend './layout.art'}}
> {{block 'head'}} ... {{/block}}
> ```

> 范例
>
> ```html
> <!--layout.art-->
> <!doctype html>
> <html>
> <head>
>     <meta charset="utf-8">
>     <title>{{block 'title'}}My Site{{/block}}</title>
> 
>     {{block 'head'}}
>     <link rel="stylesheet" href="main.css">
>     {{/block}}
> </head>
> <body>
>     {{block 'content'}}{{/block}}
> </body>
> </html>
> ```

> ```html
> <!--index.art-->
> {{extend './layout.art'}}
> 
> {{block 'title'}}{{title}}{{/block}}
> 
> {{block 'head'}}
>     <link rel="stylesheet" href="custom.css">
> {{/block}}
> 
> {{block 'content'}}
> <p>This is just an awesome page.</p>
> {{/block}}
> ```



#### 模版配置



###### 向模版中导入变量

>  template.defaults.imports.`dataName` = `dataValue`;



###### 设置模版的根目录

>  template.defaults.root = '`模版目录`';



###### 设置模版文件的默认后缀

> template.defaults.extname = '.art';

