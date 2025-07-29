## 数据库类型



#### 关系型

> 表就是关系，或者说表与表之间存在关系；

* 所有的关系型数据库都需要通过[^SQL]结构化查询语言来操作；

  [^SQL]:用于访问和处理数据库的标准的计算机语言

* 所有的关系型数据库在操作之前都需要设计`表结构`；
* 数据表具有约束性
  * 唯一性
  * 主键
  * 默认值
  * 非空



#### 非关系型

> 非关系型数据库非常灵活；
>
> 有的非关系型数据库就是 `key-value` 键值对；





## MongoDB



#### 简介

> MongoDB 是一个基于分布式文件存储的数据库；
>
> 由 C++ 语言编写。旨在为 Web 应用提供可扩展的高性能数据存储解决方案；
>
> 介于关系数据库和非关系数据库之间；



#### 安装

1. 官网下载安装包并且解压，将解压后的文件命名为`mongodb`；

2. 打开`Finder`访达，输入快捷键`commang + shift + g `快去前往文件夹

   > `/usr/local`
   >
   > 将`mongodb`文件复制到该目录下；

3. 添加环境变量

   > 终端输入`sudo vi ./.bash_profile`
   >
   > 输入用户密码；
   >
   > 目前处于查看模式，不能编辑。输入`i`，进入insert模式；
   >
   > `export PATH=/usr/local/mongodb/bin:$PATH`
   >
   > 按`ESC`键退出编辑模式；
   >
   > 输入`:wq!`保存并退出环境变量；
   >
   > 终端输入`source ./.bash_profile`，让环境变量生效；
   >
   > 终端输入`echo $PATH`，查看环境变量；

4. 创建日志及数据存放的目录

   * 在`/usr/local/mongodb`目录下新建一个`data`目录；然后在`data`目录下新建一个`db`目录;

     ```shell
     sudo mongod --dbpath=/usr/local/mongodb/data
     ```

5. 启动和关闭

   > ```shell
   > # 启动：输入 mongod + enter(回车键)
   > mongod
   > 
   > # 关闭：输入 Control + C
   > control + c
   > ```

1. 连接和断开

   > ```shell
   > # 启动 mongodb 数据库后，打开一个新的终端窗口；
   > # 输入 mongo
   > mongo
   > 
   > # 断开：输入 exit + enter(回车键)
   > exit
   > ```





## 基本命令



#### 查看数据库

> ```shell
> # 查看显示所有的数据库；
> show dbs
> 
> # 查看当前操作的数据库；
> db
> ```



#### 切换数据库

> ```shell
> # 切换到指定的数据库；
> # 如果没有该名称的数据库，则会新建一个；
> use databaseName
> ```





## 基本概念

> MongoDB 中可以有多个数据库；
>
> 一个数据库中可以有多个集合（表）；
>
> 一个集合中可以有多个文档（表记录）
>
> 文档结构很灵活，没有限制；
>
> 当要写入数据时，只需要指定往哪个数据库中的哪个集合进行操作；
>
> MongoDB 会自动完成建库建表；
>
> ```json
> {
>     "QQ":{
>        "user":[
>          {
>            "name":"Kein","age":"23","gender":"female"
>          },
>          {
>            "name":"Zoukai","age":"22","gender":"male"
>          },
>          {
>            "name":"MuYin","age":"1","gender":"female"
>          }
>        ],
>        "product":[
>          {
>            "phone":"Apple","model":"12","color":"black"
>          },
>          {
>            "phone":"Honor","model":"4s","color":"white"
>          },
>          {
>            "phone":"Vivo","model":"8","color":"pink"
>          }
>        ]
>     },
>     "Wechat":{
>        // QQ、Wechat、TIM 都是数据库；
>        // QQ 数据库中，存放着 user、product 两个集合（表）；
>        // user、product 两个集合中都存放则着若干个文档信息（表记录）；
>     },
>     "TIM":{
>     
>     }
> }
> ```





## 在Node中操作mongodb

> [mongodb模块官方指南](https://www.npmjs.com/package/mongodb)
>
> [mongoose第三方模块指南](https://mongoosejs.com)



#### 官方模块包

> ```shell
> npm install mongodb --save
> ```



#### mongoose

> 基于 mongodb 官方模块包开发的第三方模块；



###### 安装

> ```shell
> npm install mongoose --save
> ```



###### 设计集合结构

> ```javascript
> // 加载 mongoose 第三方模块；
> const mongoose = require('mongoose');
> var Schema = mongoose.Schema;
> // 连接本地 MongoDB 数据库；
> // test 数据库不需要存在，当写入第一条数据后就会被自动创建出来；
> mongoose.connect('mongodb://localhost:27017/test');
> 
> // 设计集合结构（表结构），结构是通过Schema接口定义的;
> // 字段名称就是表结构中的属性名称；
> // 约束数据：为了保证数据的统一性和完整性；
> var dataSchema = new Schema({
>   username: {
>     type: String,
>     required: true // 该属性为必须项；
>   },
>   password: {
>     type: String,
>     required: true
>   },
>   email: {
>     type: String,
>     required: false // 该属性为非必须项；
>   }
> })
> 
> // 将集合结构（表结构）发布为模型；
> // mongoose.model() 方法用来将一个表结构发布为 model ；
> // 方法的第一个参数：传入一个首字母大写的单数名词；
> // mogoose 会自动生成 小写复数 形式的 集合（表）名称；
> // 这里的 Data 会变成 datas 集合名称；
> 
> // 方法的第二个参数：Schema 集合（表）结构；
> 
> // mongoose.model() 方法的返回值：模型构造函数；
> var Data = mongoose.model('Data', dataSchema);
> 
> // 有了模型构造函数后，就可以进行增删改查操作；
> ```



###### 增加数据

> ```javascript
> // 在 datas 集合里面增加一条文档信息（表记录）；
> var own = new Data({
>   username: 'Kein',
>   password: '1314',
>   email: '2312197273@qq.com'
> })
> 
> // 数据持久化（保存）；
> own.save(function(error, data) {
>   if (error) {
>     console.log('data saving failure');
>   } else {
>     console.log(data);
>   }
> });
> ```



###### 查询数据

> ```javascript
> // 查询集合里的所有文档信息（表记录）；
> Data.find(function(error, result) {
> 	if (error) {
> 		console.log('finding failure');
> 	} else {
> 		console.log(result);
> 	}
> })
> 
> //按条件查询；
> Data.find({
> // 条件为一个对象；
> // 此处查找 username 为 ZouKai 的文档信息（表记录）
> 	username: "ZouKai"
> }, function(error, result) {
> 	if (error) {
> 		console.log('finding failure');
> 	} else {
> 		console.log(result);
> 	}
> })
> 
> // 以上方法返回的都是一个数组；
> 
> // 按 _id 查找数据；
> Data.findById(objectID,function(error,result){
> if (error) {
> 		console.log('finding failure');
> 	} else {
> 		console.log(result);
> 	}
> })
> 
> // 该方法返回的是一个对象；
> //按条件查询符合条件的第一条记录；
> Data.findOne({
> 	username: "ZouKai"
> }, function(error, result) {
> 	if (error) {
> 		console.log('finding failure');
> 	} else {
> 		console.log(result);
> 	}
> })
> ```



###### 删除数据

> ```javascript
> // 按条件删除集合里的文档信息（表记录）；
> Data.remove({
> 	username: "ZouKai"
> }, function(error, result) {
> 	if (error) {
> 		console.log('delete failure');
> 	} else {
> 		console.log('delete success');
> 	}
> })
> ```



###### 更新数据

> ```javascript
> // 根据 id 更新某条文档信息（表记录）的数据；
> Data.findByIdAndUpdate('61a0d7f4dc575ee64b3e31c4', {
> 	password: '0929',
> 	username: 'MuYi'
> }, function(error, result) {
> 	if (error) {
> 		console.log('update failure');
> 	} else {
> 		console.log('update success');
> 	}
> })
> ```