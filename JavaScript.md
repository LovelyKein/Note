## 编程思想  



#### 面向过程（POP）

> 分析出解决问题所需要的步骤;
>
> 然后用函数把这些步骤一步一步实现，使用的时候再一个一个的依次调用；



#### 面向对象（OOP）

> 把事物分解成一个个对象，然后由对象之间分工与合作；
>
> 每一个对象都是功能中心；具有灵活、代码可复用、容易维护和开发的优点;
>
> 更适合多人合作的大型项目；

[^特性]:封装性、继承性、多态性





## 类( Class )

> 定义一个类`class`；
>
> 是一个函数，简单来说，类（class）就是构造函数的另一种写法；
>
> ```java
> // 创建一个类的语法：
> class name {
>   // class body
> }
> // 创建一个实例
> var xx = new name();
> ```



#### 类和对象

###### class

> `类`抽象了对象的公共部分，它泛指某一个大类（class）；



###### object

> `对象`特指某一个，通过类的实例化形成的一个具体的对象；



#### contructor()

> 是类的构造函数（默认方法），用于传递参数，返回实例对象；
>
> 通过`new`命令生成对象实例时，自动调用该方法；
>
> 如果没有显示定义，类的内部会自动帮我们创建一个constructor（）;
>
> ```javascript
> class Stars {
>     constructor(name, like) {
>        this.name = name
>        this.like = like
>     }
>     sing() {
>        console.log('我是' + this.name + '，我喜欢' + this.like)
>     }
> };
>    
>    var xzq = new Stars('薛之谦', '唱歌');
>    ```
>    

 

#### 赋值写法

> 类中可以直接写**赋值语句**；
>
> ```javascript
> class Car {
>   constructor(brand, price) {
>     this.brand = brand
>     this.price = price
>   }
>   // 写在 constructor 外外面的实例属性；
>   // 给实例对象赋予一个固定的、不可传值的属性 wheel，值为 4；
>   wheel = 4
>   // 加上 static 前缀，则为类独有，实例对象没有；
>   static classId = 52
>   print = () => {
>     // 写成箭头函数则，该方法的 this 永远指向 Car 的实例对象；
>     console.log(this.brand + '是' + this.price)
>     // 奔驰是38W
>   }
> }
> 
> // 创建实例对象；
> const car = new Car('奔驰', '38W')
> // Car {wheel: 4, brand: '奔驰', price: '38W'}
> ```



#### 语法( Grammar )

> 类里面的所有函数都不需要加function前缀；
>
> 多个函数方法之间不需要添加逗号分割；

 

#### 继承( extends )

> 子类可以继承父类的一些属性和方法；
>
> ```javascript
> // 类的继承：子类可以继承父类的一些属性和方法；
> class Father {
>      // 这是父类
>      constructor(name, age) {
>        this.name = name
>        this.age = age
>      }
>      personal() {
>        console.log(this.name + this.age);
>      }
>    };
>    
>    class Son extends Father {
>     // 子类继承父类的一些属性和方法
>     constructor(name, age, gender) {
>        // 调用父类的 constructor 构造函数；
>        super(name, age)
>        this.gender = gender
>      }
>    };
>    
> var son = new Son('Kein', 22, 'Male');
> son.personal();
> ```

[^Focus]:在`constructor()`中使用**父类**的属性，需借助`super()`，否则会报错；



#### super

> 用于访问父类上的属性；



#### get/set 

> ```javascript
> get dataName(){
>     // 实例化对象获取 dataName 的数据时，触发此方法；
>     return this.dataName;
> };
> 
> set dataName(newValue){
>     // 实例化对象设置或赋值 dataName 时，触发此方法；
>     this.dataName = newValue;
> }
> ```

 

#### 注意( Focus )

> 在ES6中类没有变量提升，所以必须先定义类，才能通过类实例化对象；
>
> 类里面共有的属性和方法一定要加`this`使用；
>
> constructor 里面的`this`指向实例化的对象，方法里面的 this 指向这个方法的调用者；

 

#### 原型( prototype )

> 在原型对象（prototype）里面添加方法；
>
> 原型对象（prototype）里面的 constructor 属性指向类（class）本身；

 

 

## 构造函数

> 在ES6之前，对象不是基于类（class）创建的；
>
> 而是用叫**构造函数**的特殊函数来定义；

 

#### 成员

> 构造函数中的属性和方法可以称为成员，可以添加；

​	

###### 实例成员

> 构造函数内部通过`this`添加的成员；
>
> 只能通过实例化的对象来访问，不可以通过构造函数访问；



###### 静态成员

> 直接在构造函数本身上添加的成员，只能通过构造函数来访问；

   

#### prototype

> 构造函数通过原型（prototype）分配的函数是所有对象所共享的；
>
> 每一个**构造函数**都有一个`prototype`属性，指向另一个对象；
>
> `prototype`是一个对象，这个对象的所有属性和方法，都会被**构造函数**所拥有；

[^Rules]:一般情况下，公共属性定义到构造函数里面，公共的方法就放在原型对象（prototype）上；

 

#### proto

> 实例对象都有一个`_proto_`属性，指向**构造函数**的`prototype`原型对象；
>
> 实例对象可以使用构造函数和 prototype 原型对象身上的属性和方法，就是因为`_proto_`属性的存在；

[^Focus]:实例对象身上的`_proto_`对象原型和构造函数身上的`prototype`是相等的；
[^Function]:在于为对象成员的查找规则提供一个方向和路线，叫做`原型链`；

 

#### 查找原则

> 首先看实例对象本身是否有需要的方法和属性，如果有，就使用实例对象本身的方法和属性；
>
> 如果实例对象本身没有找到，就顺着`_proto_`属性指向的构造函数的原型对象`prototype`身上去查找需要的方法和属性；

 

#### constructor

> 实例对象中的`_proto_`属性和构造函数的原型对象`prototype`属性里面都有一个`constructor`属性，指向构造函数本身；



###### 作用

> 主要用于记录该对象引用于哪个构造函数；
>
> 一般手动添加`contructor`属性指向构造函数本身；

  

#### 原型链

> 只要是对象，就会有`_proto_`属性；
>
> 指向其构造函数的原型对象`prototype`；

 



 ## call()

> 改变this的指向；也可以调用函数；
>
> ```javascript
> function Father(name, age, money) {
>      this.name = name;
>      this.age = age;
>      this.money = money;
> };
> 
> Father.prototype.salary = function() {
>      console.log(this.money);
> };
> 
> // 创建一个子构造函数
> function Son(name, age, money, score) {
>      // 改变子父构造函数的this指向，变成子构造函数
>      Father.call(this, name, age, money);
>      this.score = score;
> };
> 
> // 子构造函数继承父构造函数的方法
> // Son.prototype = Father.prototype;不能这样写，如果修改了子原型对象，父原型对象也会跟着一起变化；
> Son.prototype = new Father();//此时Son的原型对象指向的是Father的原型对象
> 			
> // 此时利用对象的形式修改了构造函数Son的原型对象，要利用constructor属性指向回本身的构造函数
> Son.prototype.constructor = Son;
> 
> // 创建一个实例对象
> var person = new Son('邹凯', 22, '¥5201314', 'A+');
> ```

 

 

## 数组迭代

> forEach（）、map（）、filter（）、some（）、every（）

 

#### forEach（）

> 遍历数组，对数组里面的每个元素都进行操作；
>
> ```javascript
> var array = [12, 5, 8, 9, 11];
> var arraySum = 0;
> // forEach（）：遍历数组，对数组里面的每个元素都进行操作
> array.forEach(function(value, index, array) {
>      arraySum += value;
> })
> console.log(arraySum);/*arraySum = 45;*/
> ```

 

#### filter（）

> 根据条件筛选数组，返回一个符合条件的**新数组**；
>
> ```javascript
> var array = [12, 5, 8, 9, 11];
> // filter（）：筛选数组，返回一个新的数组
> var newAr = array.filter(function(value, index, array) {
>      return value > 8;
> })
> console.log(newAr);/*newAr = [12,9,11];*/
> ```

 

#### some()

> 检测数组中的元素是否满足制定条件；
>
> 如果找到第一个满足条件的元素，则终止循环不再继续查找，返回的是**布尔值**；
>
> ```javascript
> var array = [12, 5, 8, 9, 11];
> 
> var result = array.some(function(value, index, array) {
>      return value > 10;
> })
> console.log(result);/* result = true */
> ```



#### every()

> 用于检测数组中**所有元素**是否都符合指定条件；
>
> 如果有一个元素不满足条件，返回`false` ，终止循环不再继续检测，
>
> 所有元素都符合条件，返回`true`；
>
> ```javascript
> const array = [12, 5, 8, 9, 11]
> 
> const result = array.every((value, index, array) => {
>     // 检测元素元素是否都大于 4
>     return value > 4
> })
> console.log(result);/* result = true */



#### map()

> `map()` 方法会创建一个**新数组**；
>
> 其结果是数组中的每个元素进行操作后的**返回值**；
>
> [^Focus]:`return`的是什么，新数组的成员就是什么；
>
> ```javascript
> const target = [
>      {name: 'Kein',age: 23},
>      {name: 'MuYin',age: 1},
>      {name: 'ZouKai',age: 22},
> ]
> 
> const newArray = target.map((item,index,array) => {
>      return item.name
> })
> 
> // newArray = ['Kein','MuYin','ZouKai']
> ```



#### find（）

> 返回通过测试（函数内判断）的数组中的**第一个元素的值**，之后的值不会再调用执行函数；
>
> 如果没有符合条件的元素返回 undefined ；
>
> ```javascript
> var array = [12, 5, 8, 9, 11];
> // find（）：返回通过测试（函数内判断）的数组的第一个元素的值;
> var result = array.find(function(value){
>   return value === 9;
> })
> console.log(result);/*result = 9;*/
> ```

[^value]:数组里面每个元素的值；
[^index]:数组每个元素的索引值；
[^array]:数组本身；

  

#### reduce()

> `reduce()` 方法对数组中的**每个元素**执行一个自定义的 **reducer** 函数；
>
> 将其结果汇总为值并返回；
>
> ```javascript
> // 语法：
> arr.reduce(callback,initialValue);
> // callback 是一个自定义的 reducer 函数；
> 
> function callback(accumulator,currentValue,index,array){
>      return accumulator + currentValue;
> }
> ```

[^callback]:用户自定义的 reducer 函数，作为`reduce()`方法的第一个参数；

> [^accumulator]:累计器累计回调的返回值，它是上一次调用回调时返回的累积值；
> [^currentValue]:数组中正在处理的元素的值；
> [^index]:可选，数组中正在处理的当前元素的索引值；
> [^array]:可选，调用`reduce()`方法的数组；

[^initialValue]:可选，作为第一次调用 `callback`函数时的第一个参数的值；





## Object.defineProperty()

> 给对象增加新的属性或修改原有的属性；
>
> 语法：
>
> ```javascript
> Object.defineProperty(obj,prop,descriptor);
> ```

[^obj]:要进行操作的目标对象；
[^prop]:要定义或修改的属性名；
[^descriptor]:要定义或修改的属性的描述；以对象形式`{ }`书写；

​		 

#### descriptor

> 以对象形式`{ }`书写；
>
> ```javascript
> var phone = {
>     model: 'iphone 12 ProMax',
>     brand: 'Apple',
>     color: 'black',
>     price: 5999
> };
> 
> // 通过Object.defineProperty（）方法修改color属性
> Object.defineProperty(phone, 'color', {
>     value: 'white'
> })
> console.log(phone);/*phone.color = 'white';*/
> 
> // 通过Object.defineProperty（）方法增加一个amount属性
> Object.defineProperty(phone, 'amount', {
>     value: 1000,
>     writable:true,
>     enumerable:true,
>     configurable:true,
>     // 当读取 phone 中的 amount 属性时，就会调用此方法，返回一个值；
>     get: function(){
>        return this.amount;
>     },
>     // 当修改 phone 中的 amount 属性时，就会调用此方法；
>     set: function(newValue){
>        this.amount = newValue;
>     }
> });
> ```

[^value]:设置属性的值，默认为 undefined；
[^writable]:属性值是否可以重写，布尔值（true或false），默认是 false；
[^enumerable]:属性是否可以被枚举，布尔值（true或false），默认是 false；
[^configurable]:属性是否可以被删除或者再次修改，布尔值（true或false），默认是 false；

 

  

## 函数进阶

 

#### 定义方式



###### 命名函数

> ```javascript
> // 自定义函数
> function funName（）{};
> ```



###### 匿名函数

> ```javascript
> // 函数表达式；
> var funName = function（）{ };
> ```



###### Function 构造函数

> ```javascript
> var funName = new Function（）；
> ```

​		

#### 函数的本质

> 所有的函数都是`Function`构造函数的实例对象；
>
> 函数本质是对象，是一种数据类型；

​	

#### this

> 一般`this`指向函数的调用者，谁调用了函数，`this`就指向谁；
>
> 定时器函数里面的`this`指向的是`window`；



#### 获取异步操作的结果

> 如果要获取一个函数中异步操作的结果，则必须通过回调函数来获取；
>
> ```javascript
> function fn(callback){
>      setTimeout(function(){
>        var data = 'Hello World !';
>        callback(data);
>      },1000);
> }
> 
> // 如果要获取一个函数中异步操作的结果，则必须通过回调函数来获取；
> // 回调函数
> function receive(data){
>      console.log(data)
> }
> fn(receive)
> ```

 

#### 函数柯里化

> 通过函数调用继续返回函数的方式，实现多次接受参数最后统一处理的函数编码形式；

 

## strict mode

> 严格模式；
>
> 指在严格的语法条件下运行JavaScript代码；	



#### 兼容性

> 在 IE10 以上版本会被支持，旧版本会被忽略；

​	

#### 开启 strict mode	



###### 全局开启

> 在脚本里面的代码的最上方写入 ` ‘use strict’`；

​		

###### 局部开启

> 在函数体内写入 ` ‘use strict’`；

​	

#### 规则

> 变量必须先声明再使用；
>
> 不能随意删除已经声明好的变量；
>
> 全局作用域的函数里面的 this 指向 undefined ；
>
> 函数不能传递重名的形参；
>
> 函数必须声明在顶层：逻辑运算（ if、for、while、switch ...）里面不能声明函数；

 

 

## 闭包( Closure )

> 可以访问另一个函数作用域中变量的函数，即**定义在一个函数内部的函数**；



#### 形成条件

> 形成一个闭包环境，需要两个条件：**函数嵌套**，子函数引用父函数**局部变量**；
>
> ```javascript
> // 没有形成闭包；
> // 只有函数嵌套，没有子函数引用父函数局部变量；
> var name = "the window"
> var obj = {                    
>   name : "myObject",          // 属性
>   getNameFunc:function(){     // 方法
>     return function(){
>       return this.name
>     }
>   }
> }
> alert(obj.getNameFunc()())
> 
> // 形成闭包；
> // 子函数使用了父函数的变量引用；
> var name2 = "the window"
> var obj2 = {                            
>   name2:"my name is a obj",
>   getNameFunc2 :function fn1(){
>     var that = this
>     return function(){
>       return that.name2
>     }
>   }
> }
> alert(obj2.getNameFunc2()())
> ```



#### 创建闭包

> 闭包就是可以创建一个独立的环境；
>
> 每个闭包里面的环境都是独立的，互不干扰；
>
> ```javascript
> /* case-1 */
> function funA(){
>   var a = 10  // funA的活动对象之中;
>   return function(){   //匿名函数的活动对象;
>     alert(a)
>   }
> }
> var b = funA()
> b()  //10
> 
> /* case-2 */
> function outerFn(){
>   var i = 0
>   function innerFn(){
>     i++
>     console.log(i)
>   }
>   return innerFn
> }
> var inner = outerFn()
> inner()
> inner()
> inner()
> var inner2 = outerFn()
> inner2()
> inner2()
> inner2()
> // 输出结果： 1 2 3 1 2 3
> // Reason: 每次外部函数执行的时候，外部函数的引用地址不同，都会重新创建一个新的地址；
> 
> /* case-3 */
> var i = 0
> function outerFn(){
>   function innnerFn(){
>     i++
>     console.log(i)
>   }
>   return innnerFn
> }
> var inner1 = outerFn()
> var inner2 = outerFn()
> inner1()
> inner2()
> inner1()
> inner2()
> // 输出结果： 1 2 3 4
> ```



#### 优缺点

> 根据场景需要，是否使用闭包；



###### 优点

> 可以延展变量的作用域，让外部访问函数内部变量成为可能；
>
> 隐藏变量以及防止变量被篡改和作用域的污染，从而实现封装；



###### 缺点

> 会造成内存泄漏[^Reason]；

[^Reason]:函数执行完后，函数内的局部变量没有释放，占用内存时间变长，容易造成内存泄漏；
[^Resolve]:让内部函数变成垃圾对象，赋值为`null`，及时释放，让浏览器回收闭包；



#### 结论

> **闭包找到的是同一地址中父级函数中对应变量最终的值**；

​	



## 循环( Loop )



#### for-in

> 获取的是**对象的键值**或者**数组的索引**；
>
> 遍历顺序有可能不是按实际数组的内部顺序，更适合遍历对象；
>
> ```javascript
> const obj = { x: 123, y: 234, z: 456 };
> for (let i in obj) {
>      console.log(i);
> }
> // x y z
> ```



#### for-of

> 遍历的是**数组元素值**，返回的是数组内的元素；
>
> ```javascript
> const array = [123, 234, 456]
> for(let i of array) {
>     console.log(i)
> }
> // 123 234 456
> ```

​	

​	

## 递归

[^定义]:如果一个函数在内部自己调用本身，那这个函数就是**递归函数**；	
[^Focus]:递归函数容易发生“栈溢出”错误( stack overflow )，**必须要有退出条件；** 

> ```javascript
> // 利用递归函数求1～n的阶乘：
> function number(n) {
>      if (n === 1) { // 添加递归函数的退出条件，否则就会发生错误，死循环；
>        return 1;
>      }
>      return n * number(n - 1); //自己在函数内部调用本身
> };
> console.log(number(6));/*720*/
> 
> 
> // 利用递归函数求斐波那契数列（兔子序列）：1 1 2 3 5 8 13 21 34 55 ...，输入n就可以知道与之对应的序列值；
> // 理解：知道（n-1）和（n-2）的序列值就可以知道n的序列值 = （n-1）+（n-2）；
> function fbnq(n) {
>      // 添加递归函数的退出条件，否则就会发生错误，死循环
>      if (n === 1 || n === 2) {
>        return 1;
>      };
>      //自己在函数内部调用本身
>      return fbnq(n - 1) + fbnq(n - 2);
> };
> console.log(fbnq(10));/*55*/
> 
> 
> // 利用递归函数实现数据的 深度拷贝
> var apple = [
>      {
>        id: 1,
>        brand: 'Iphone',
>        model: ['iphone8 Plus', 'iphone11', 'iphone12 Pro', 'iphone13ProMax']
>      },
>      {
>        id: 2,
>        brand: 'iMac',
>        model: ['iMac Pro', 'iMac 4K', 'iMac 6K', 'iMac Display']
>      },
>      {
>        id: 3,
>        brand: 'Watch',
>        model: ['Watch Series 3', 'Watch SE', 'Watch Series 6', 'Watch Series 7']
>      }
> ]
> 
> var copy = []
> function deepCopy(creat, target) {
>      for (var index in target) {
>        // 获取属性值；
>        var item = target[index];
>        // 判断属性值是哪一种数据类型；
>        if (item instanceof Array) {
>          // 判断属性值是否是数组；
>          creat[index] = [];
>          deepCopy(creat[index], item);
>        } else if (item instanceof Object) {
>          // 判断属性值是否是对象；
>          creat[index] = {};
>          deepCopy(creat[index], item);
>        } else {
>          creat[index] = item;
>        }
>      }
> }
> deepCopy(copy,apple)
> ```

 

## 节流 / 防抖

[^Question]:事件因为用户的频繁操作而被触发，每次都会去执行操作，可能会造成**卡顿现象**；



#### 节流

> 规定一个**时间间隔**；
>
> 在这个时间间隔之内，不会重复的触发事件函数；大于这个时间间隔才会被触发；
>
> 将每次触发变为间隔触发；
>
> ```javascript
> // 获取元素
> const number = document.getElementById('number')
> // 获取点击按钮
> const btn = document.getElementById('btn')
> 
> // 点击按钮 count +1 
> let count = 0
> 
> function start() {
>      let canNext = true
>      btn.onclick = function () {
>        // 判断 canNext 的值，为 false 则直接 return 出去
>        if (!canNext) {
>          return
>        }
>        canNext = false
>        // 1000ms 后将 canNext 的值设置为 true
>        const timer = setTimeout(() => {
>          count++
>          number.innerHTML = count
>          canNext = true
>        }, 1000)
>      }
> }
> start()
> ```



#### 防抖

> 规定一个**延迟触发时间**；
>
> 之前的所有触发都会被取消，**最后一次**的触发会在**延迟时间**之后被执行；
>
> 连续快速的触发，也只会执行最后一次；
>
> ```javascript
> // 获取表单输入元素
> const input = document.getElementById('input')
> // 函数防抖
> function start() {
>      let timer = null
>      input.oninput = function () {
>        if (timer) {
>          // 判断 timer 的值，为真说明触发函数，则清除掉延时器
>          clearTimeout(timer)
>        }
>        timer = setTimeout(() => {
>          console.log('正在输入...')
>        }, 1000)
>      }
> }
> start()
> ```



 

## 正则表达式

> **正则表达式**：`Regular Expression`；
>
> 用于匹配字符串中字符组合的模式；
>
> 在 JavaScript 中，正则表达式也是对象；

​	

#### 创建



###### RegExp 构造函数

> ```javascript
> var 变量名 = new RegExp（/表达式/）；
> ```

​		

###### 字面量

> var 变量名 = /表达式/；
>
> ```javascript
> var reg = /^[a-zA-Z0-9_-]{6,16}$/;
> //6位到16位的任意数字、字母、下划线、横线；
> ```





## ECMAScript ?

> ECMAScript 是由Ecma国际通过 ECMA-262 标准化的脚本程序设计语言；
>
> Ecma 国际制定了许多标准，而 ECMA-262 只是其中一个；





## let

> 变量不能不能重复声明；
>
> `let`变量具有块级作用域，在块的外部不能访问；
>
> 不存在变量提升；
>
> 不影响作用域链效果，即内部依然能访问外部的let变量；





## const

> `const`用来声明常量；
>
> 声明时一定要赋予初始值；
>
> 一般常量使用大写（潜规则）；
>
> 常量的值不能修改；
>
> `const`变量具有块级作用域；
>
> 当声明的常量是一个对象或数组时，可以更改里面的元素，不会报错；





## 模板字符串

> 用**反引号**包裹内容；
>
> 内容中可以直接出现换行；
>
> ```javascript
> const text =	`
> 								这个是 ES6 的新语法，
> 								很方便使用
> 							`
> ```



#### 变量拼接

> 语法：`${dataName}`；
>
> ```javascript
> let star = ‘薛之谦’
> let out = `${star}是最搞笑的歌手`
> console.log(out);/*薛之谦是最搞笑的歌手*/
> ```





## 对象简写

> ES6 提供了简化对象属性和方法的语法糖；



#### 属性

> 可以直接在内部写变量，当成一个属性；从而省略属性值；
>
> ```javascript
> var objet = {
> name,
> age,
> sex
> }
> ```



#### 方法

> 方法可以省略`fuction`；
>
> ```javascript
> var object = {
> export(){
>  console.log(this.name);
> }
> };
> ```



#### 箭头函数

> 箭头函数中的`this`是静态的；
>
> **始终指向函数声明时所在作用域下的`this`指向，无法改变**；
>
> 声明构造函数不能使用箭头函数；
>
> 当形参只有一个的时候，可以省略`()`小括号；
>
> ```javascript
> var method = (x,y)=>{
>  return x*y;
> }
> console.log(method(4,5));/*20*/
> ```



#### 形参初始值

> ```javascript
> function add(a,b,c=10){
>  return a+b+c;
> }
> let result = add(2,5);/*此时第三个参数未传数据*/
> console.log(result);/*17*/
> ```





## 解构赋值

> 按照一定模式从数组和对象中提取值，再对变量进行赋值；



#### 数组的解构

> ```javascript
> const F4 = ['小沈阳','刘能','赵四','宋小宝'];
> let [a, b, c, d] = F4;
> ```



#### 对象的解构

> ```javascript
> const person = {
>     name: '赵本山',
>     age:65,
>     power: function(){
>        console.log('我是小品演员！');
>     }
> };
> let {name, age, power} = person;
> ```



#### 连续解构

> ```javascript
> const list = {
>   keys: {
>     left: 'A',
>     right: 'D'
>   },
>   mouse: {
>     clickLeft: 'mouse-left',
>     middle: 'mouse-middle',
>     clickRight: 'mouse-right'
>   }
> }
> 
> // 解构赋值
> const { keys } = list
> // 连续解构赋值
> const { keys:{ left } } = list // 'A'
> // 连续解构并且更改变量名 为 keyLeft
> const { keys:{ left:keyLeft } } = list // 'A'
> ```





## ...args

> 用于获取函数的实参，用来代替 arguments ；
>
> 必须写在形参的最后面；
>
> ```javascript
> function fn(a,b,...args){
>   console.log(a);   /*a=1*/
>   console.log(b);   /*b=2*/
>   console.log(args);   /*args=[3,4,5,6]*/
> };
> fn(1,2,3,4,5,6);
> ```





## 扩展运算符

> `...`是 ES6 中增加的扩展运算符;
>
> 能将`数组`或`对象`转换为`逗号`分隔的参数序列；



#### 合并数组

> ```javascript
> const kuaizi = ['王太利','肖央'];
> const fh = ['曾毅','玲花'];
> 
> //const star = kuaizi.concat(fh);数组拼接concat()方法；
> 
> const star = [...kuaizi,...fh];/*star = ['王太利','肖央','曾毅','玲花']*/
> ```



#### 数组浅拷贝

> ```javascript
> const zm = ['A','B','C'];
> const clone = [...zm];/*clone = ['A','B','C']*/
> ```



#### 转化成数组

> ```javascript
> const zm = {
>     0: A,
>     1: B,
>     2: C
> };
> const clone = [...zm];   /*clone = ['A','B','C']*/
> ```



#### 克隆对象

> 利用`...`扩展运算符，可以深度克隆对象；
>
> ```javascript
> const person = {
>      name: 'LovelyKein',
>      gender: 'Male',
>      age: '22'
> }
> const clonePerson = {...person}
> const clonePerson_1 = {...person, name: 'MuYi', gender: 'Female'} // 克隆的同时，修改对象的属性值
> person.name = 'Kein' // 改变 person.name 的值
> console.log(clonePerson) // clonePerson 不会受影响
> ```





## Symbol

> 是 ES6 中新增的一种**原始数据类型**，表示**独一无二的值**；
>
> 类似于字符串`String`数据类型；



#### Create

> 创建一个`Symbol`类型数据；
>
> ```javascript
> var dataName = Symbol()
> ```



#### Feature

> `Symbol`的值是唯一的，用来解决命名冲突的问题；
>
> `Symbol`的值不能与其他数据进行运算；
>
> `Symbol`定义的对象不能用 for…in 循环遍历；
>
> `Symbol`可以使用 Reflect.ownKeys 来获取对象的所有键名；





## 迭代器( Iterator )

>迭代器`Iterator`是一种接口，为各种不同的数据结构提供统一的访问机制；
>
>任何数据结构只要部署`Iterator`接口，就可以完成遍历操作；





## 生成器( Generator )

>`Generator` 生成器函数；
>
>解决回调再回调（回调地狱）`callbacl hell`的问题;
>
>```javascript
>// 模拟先获取用户的数据，再获取用户的订单数据，最后获得订单的商品数据
>
>// 先创建要用到的回调函数；
>function user() {
>    setTimeout(() => {
>         let data = '用户数据';
>         // 第二次调next（），将data里面的数据传出，作为第一段yield代码的返回值
>         creatFuntion.next(data);
>    })
>};
>
>function order() {
>    setTimeout(() => {
>         let data = '订单数据';
>         // 第三次调next（），将data里面的数据传出，作为第二段yield代码的返回值
>         creatFuntion.next(data);
>    })
>};
>
>function goods() {
>    setTimeout(() => {
>         let data = '订单的商品数据';
>         // 第四次调next（），将data里面的数据传出，作为第三段yield代码的返回值
>         creatFuntion.next(data);
>    })
>};
>
>// 创建生成器函数；
>function* getData() {
>    // 此时获取了creatFuntion.next(data)传过来的‘用户数据’；
>    let one = yield user();
>    console.log(one);
>    // 此时获取了creatFuntion.next(data)传过来的'订单数据'；
>    let two = yield order();
>    console.log(two);
>    // 此时获取了creatFuntion.next(data)传过来的订单的'商品数据'；
>    let three = yield goods(); 
>    console.log(three);
>};
>
>let creatFuntion = getData();
>// 生成器函数要用.next()调用才会生效，这是第一次调next（）
>creatFuntion.next(); 
>```





## Promise

>[ES6入门教程](https://es6.ruanyifeng.com)
>
>ES6引入的异步编程的新的解决方案，语法上`Promise`是一个构造函数；
>
>用来封装异步编程，并且可以获取其成功或失败的结果；
>
>`Promise`不是异步函数，一般里面封装的才是异步；
>
>```javascript
>var fs = require('fs');
>// 创建一个Promise构造函数实例化的对象；
>const p_1 = new Promise((resolve, reject) => {
>      // 函数体内进行异步操作；
>      fs.readFile('./data.json',function(error,data){
>         if (error){
>           reject(error);
>         } else {
>           resolve(data);
>         }
>      })
>})
>// resolve（‘数据’）：异步操作成功时，使用的方法；
>// reject（‘数据’）：异步操作失败时，使用的方法；
>
>const p_2 = new Promise((resolve, reject) => {
>      // 函数体内进行异步操作；
>      fs.readFile('./data_1.json',function(error,data){
>         if (error){
>           reject(error);
>         } else {
>           resolve(data);
>         }
>      })
>})
>
>// 调用then方法：
>
>// then方法的返回结果是Promise的实例化对象，对象的状态由构造函数的执行结果决定；
>
>// 构造函数返回的是非 Promise 对象类型的数据，状态为成功，返回值为对象return的成功（resolve（））的值；
>p_1.then(function(value){
>      console.log(value);
>      // return 'successful!';
>
>      // 解决回调地狱的问题，链式调用；
>      return p_2; // p_2 是一个 Promise 实例对象；
>      // 当 return 一个 Promise 实例对象时，后续的 then 方法中的两个参数会对应 P_2 的 resolve() 和 reject()；
>},function(reason){
>      console.error(reason);
>      return 'error!';
>}).then(function(value){
>      return value;
>},function(reason){
>      return reason;
>})
>
>
>// then（function（value）{}，function(reason){}）
>// then方法可以接受两个函数参数：
>
>// value函数：形参value的值是resolve（）抛出的值；
>// 函数体内写的是异步操作成功后的操作；
>
>// reason函数：形参reason的值是reject（）抛出的值；
>// 函数体内写的是异步操作失败后的操作；
>
>
>// Promise 封装文件读取方法，代替自己写 callback 回调函数；
>var fs = require('fs');
>var promiseReadFile = function(filePath){
>      return new Promise(function(resolve,reject){
>         fs.readFile(filePath,'utf8',function(error,data){
>           if(error){
>             reject(error);
>           } else{
>             resolve(data);
>           }
>         })
>      })
>}
>// then 方法获取异步操作结果；
>promiseReadFile(./data.json).then(function(value){
>      return value;
>},function(reason){
>      return reason;
>})
>```





## Set()

> 集合；
>
> 成员的值都是唯一的，没有重复的值；



#### Feature

> 自带数组去重，但返回的结果是**对象**，和扩展运算符（...）一起使用转换成数组；
>
> ```javascript
> // 1.数组去重
> let arr = [1, 2, 6, 2, 5, 3, 1, 6, 8, 9, 3];
> let result = [...new Set(arr)];
> console.log(result);
> 
> 
> // 2.求交集
> let arr_1 = [1, 2, 3, 4, 5, 6, 7, 8, 9];
> let arr_2 = [12, 25, 3, 49, 51, 6, 7, 8, 9];
> let result_1 = arr_2.filter((item) => {
>    if (new Set(arr_1).has(item)) {
>        return item;
>    } else {
>        return false;
>    }
> })
> console.log(result_1);
> 
> 
> // 3.求并集
> let union = [...new Set([...arr_1, ...arr_2])];
> console.log(union);
> 
> 
> // 4.求差集，相当于交集的取反
> let diff = arr_2.filter((item) => {
>    if (!new Set(arr_1).has(item)) {
>        return item;
>    }
> })
> console.log(diff);
> ```



#### Methods

> 集合的方法；



###### .size

> 返回集合里的元素个数；
>
> ```javascript
> const array = ['Iphone', 'Huawei', 'Oppo', 'Vivo']
> new Set(array).size // 4
> ```
>



###### .add(item)

> 给当前的集合在元素的最后面添加一个新元素；
>
> 当添加实例中已经存在的元素，`Set`不会进行处理添加；
>
> 可以进行**链式**调用；
>
> ```javascript
> const array = ['Iphone', 'Huawei', 'Oppo', 'Vivo']
> new Set(array).add('Xiaomi').add('Xiaomi') // 只会添加一次
> ```



###### .delete(item)

> 删除当前集合里的元素，返回的是布尔（boolean）值；
>
> ```javascript
> const array = ['Iphone', 'Huawei', 'Oppo', 'Vivo']
> new Set(array).delete('Xiaomi')
> ```



###### .has(item)

> 检测集合里是否包含某个元素，返回的是布尔（boolean）值；
>
> ```javascript
> const array = ['Iphone', 'Huawei', 'Oppo', 'Vivo']
> new Set(array).has('Iphone') // true
> ```



###### .clear()

> 清空当前集合里的所有元素；
>
> ```javascript
> const array = ['Iphone', 'Huawei', 'Oppo', 'Vivo']
> new Set(array).clear()
> // array: []
> ```





## Map()

> Map 类型是**键值对的有序列表**，而`键和值都可以是任意类型`；



#### Map/Set

> [^Map]:是一种叫做[^字典]的数据结构，以**[键，值]**的形式存储；
> [^Set]:是一种叫做[^集合]的数据结构，以**[值，值]**的形式存储元素；
> [^字典]:是一些元素的集合，每个元素有一个称作 key 的域，不同元素的 key 各不相同；
> [^集合]:是由一堆无序的、相关联的，且不重复**内存**的结构**数学中称为元素**组成的组合；
>
> 都可以存储不重复的值；



#### Methods

> Map 实例的方法；



###### size

> `size`属性返回 Map 结构的成员总数；
>
> ```javascript
> const map = new Map()
> map.set('foo', true)
> map.set('bar', false)
> 
> map.size // 2
> ```



###### set()

> 设置**键名`key`**对应的**键值为`value`**，然后返回整个 Map 结构；
>
> 如果`key`已经有值，则键值会被更新，否则就新生成该键；
>
> 因为方法返回的是当前`Map`对象，所以可以采用链式写法；
>
> ```javascript
> const m = new Map();
> 
> m.set('edition', 6)        // 键是字符串
> m.set(262, 'standard')     // 键是数值
> m.set(undefined, 'nah')    // 键是 undefined
> m.set(1, 'a').set(2, 'b').set(3, 'c') // 链式操作
> ```



###### get()

> `get()`方法读取`key`对应的键值，如果找不到`key`，返回`undefined`；
>
> ```javascript
> const m = new Map();
> 
> m.set('hello', 'Hello ES6!')
> 
> m.get('hello')  // Hello ES6!
> ```



###### has()

> `has()`方法返回一个**布尔值**，表示某个键是否在当前 Map 对象之中；
>
> ```javascript
> const m = new Map();
> 
> m.set('edition', 6);
> m.set(262, 'standard');
> m.set(undefined, 'nah');
> 
> m.has('edition')     // true
> m.has('years')       // false
> m.has(262)           // true
> m.has(undefined)     // true
> ```



###### delete()

> `delete()`方法删除某个键，返回`true`，如果删除失败，返回`false`；
>
> ```javascript
> const m = new Map();
> m.set(undefined, 'nah');
> m.has(undefined)     // true
> 
> m.delete(undefined)
> m.has(undefined)       // false
> ```



###### clear()

> `clear()`方法清除所有成员，没有返回值；
>
> ```javascript
> let map = new Map();
> map.set('foo', true);
> map.set('bar', false);
> 
> map.size // 2
> map.clear()
> map.size // 0
> ```



###### keys()

> 返回 Map 键名的遍历器；
>
> ```javascript
> const map = new Map([['F', 'no'],['T',  'yes']])
> const mapKeys = map.keys() // ['F', 'T']
> ```



###### values()

> 返回 Map 键值的遍历器；
>
> ```javascript
> const map = new Map([['F', 'no'],['T',  'yes']])
> const mapValues = map.values() // ['no', 'yes']
> ```



###### entries()

> 返回 Map 所有成员的遍历器；
>
> ```javascript
> const map = new Map([['F', 'no'],['T',  'yes']])
> const mapEntries = map.entries() // [['F', 'no'],['T',  'yes']]
> ```



###### forEach()

> 遍历 Map 的所有成员；
>
> ```javascript
> const map = new Map([['F', 'no'],['T',  'yes']])
> map.forEach(value, key, map) {
>      console.log(value) // 'no'、'yes'
>      console.log(key) // 'F'、'T'
>      console.log(map) // [['F', 'no'],['T',  'yes']]
> }
> ```

[^value]:键值；
[^key]:键名；
[^map]:Map 对象；





## 模块化

> 最初的 JavaScript 是不支持模块化语法的；
>
> 指将一个大的程序文件，拆分成许多个小的文件，然后再将小文件组合起来；



#### 好处

> 防止命名冲突；
>
> 代码可复用；
>
> 较高的维护性；



#### 语法

>`import` ：用于输入其他模块提供的功能；
>
>`export `：用于规定模块的对外接口，向其他模块提供什么功能；
>
>```javascript
>// 以下都是静态加载：import命令会被 JavaScript 引擎静态分析，先于模块内的其他语句执行；
>// 有利于编译器提高效率，但是无法在运行时按需加载模块；
>
>// 整体加载：
>import * as star from './src/module_1.js';
>star.power();
>
>// 解构赋值形式写法：逐一指定要加载的方法;
>import {name,who} from './src/module_2.js'
>console.log(name+'\n'+who);
>
>// 以上使用 import 命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载;
>
>// export default 命令：为模块指定默认输出;
>import feature from './src/module_3.js';
>console.log(feature.height);
>
>
>// 动态加载模块：运行时执行，也就是说，什么时候运行到这一句代码，就会加载指定的模块；
>// import(muduleName)函数;返回一个 Primise 对象；
>
>import('./src/module_4.js');
>```





## 异步

> 获取**异步操作**结果的语法；



#### async

> `async`函数的返回值为 promise 对象；
>
> 它就是 Generator 生成器函数的语法糖；
>
> promise 对象的结果由`async`函数执行的返回值决定；
>
> ```javascript
> // 利用 async 和 await 发送 AJAX 请求
> function sendAJAX(url) {
> return new Promise((resolve,reject)=>{
>  const x = new XMLHttpRequest();
>  x.open('GET', url);
>  x.send();
>  x.onreadystatechange = function() {
>    if (x.readyState === 4) {
>        if (x.status >= 200 && x.status <= 300) {
>           resolve(x.response);
>        } else {
>           reject(x.status);
>        }
>    }
>  }
> })
> }
> 
> // pomise then 方法测试 
> sendAJAX('https://api.apiopen.top/getloke').then((value)=>{
> console.log(value);
> },(reason)=>{
> console.error(reason);
> });
> 
> // async 和 await 方法测试 
> async function main(){
>  let result = await sendAJAX('https://api.apiopen.top/getloke');
>  console.log(result);
> };
> main();
> ```



#### await

> `await`必须写在 async 函数中；
>
> `await`右侧的表达式一般为 promise 对象；
>
> `await`返回的是 promise 成功的值；
>
> `await`的 promise 失败了，就会抛出异常，需要通过`try...catch`捕获处理；





## 数组方法





#### .includes(element)

> 方法用来检测数组`array`中是否包含某个元素`element`；
>
> 返回布尔类型值；
>
> ```javascript
> array.includes(item);
> // 返回布尔类型值（true/false）
> ```





## 对象方法



#### Object.keys()

> 方法返回一个**数组**；
>
> 成员是参数对象自身的（不含继承的）所有可遍历`enumerable`属性的键名；
>
> ```javascript
> var obj = { foo: 'bar', baz: 42 };
> var nameArr = Object.keys(obj);
> // nameArr = ["foo", "baz"];
> ```



#### Object.values()

> 方法返回一个**数组**；
>
> 成员是参数对象自身的（不含继承的）所有可遍历`enumerable`属性的键值；
>
> ```javascript
> var obj = { foo: 'bar', baz: 42 };
> var valueArr = Object.keys(obj);
> // valueArr = ["bar", 42];
> ```



#### Object.entries()

> 方法返回一个**二维数组**；
>
> 成员是参数对象自身的（不含继承的）所有可遍历`enumerable`属性的键值对数组；
>
> ```javascript
> const obj = { foo: 'bar', baz: 42 };
> let newArr = Object.entries(obj);
> // newArr = [["foo", "bar"], ["baz", 42]];
> ```



#### Object.fromEntries()

> 是`Object.entries()`的逆操作，用于将一个键值对数组转为对象；
>
> ```javascript
> var obj = Object.fromEntries([
>   ['foo', 'bar'],
>   ['baz', 42]
> ]);
> // obj = { foo: "bar", baz: 42 };
> ```



#### Object.assign()

> **合并对象属性**；
>
> 用于将所有可枚举属性的值从一个或多个**源对象**分配到**目标对象**；
>
> 返回**目标对象**；
>
> 语法：`Object.assign(target, ...sources)`
>
> ```javascript
> const target = { a: 1, b: 2 }
> const source = { b: 4, c: 5 }
> // 将 source 对象合并到 target 对象
> Object.assign(target, source)
> 
> console.log(target)
> // target: { a: 1, b: 4, c: 5 }
> ```





## ESLint

> 一个可组装的`JavaScript`和`JSX`代码检查工具；



#### 使用( Use )

> ```javascript
> // 在整个文件中取消代码语法检查
> /* eslint-disable */
> 
> // 对 当前行 取消代码语法检查
> // eslint-disable-line
> 
> // 对 下一行 取消代码语法检查
> // eslint-disable-next-line
> ```

