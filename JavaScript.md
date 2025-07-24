# 类`class`

本质是一个函数，简单来说，类`class`就是构造函数的另一种写法（语法糖）

- 类的构造器必须使用`new`关键字调用
- 类在定义和初始化之前不可被读取使用，和`let`和`const`一样，声明不会被提升，存在暂时性死区
- 类的所有方法（非箭头函数）都是不可枚举的，内部处理会将方法放到原型对象上去
- 类的所有代码均在严格模式下执行
- 类里面的所有方法都不需要加`function`前缀，多个函数方法之间不需要添加逗号分割

```js
// 创建一个类
class Animal {
  // 字段初始化器（ES7），添加一个拥有默认值的实例属性
  legs = 4
  
  // 类的构造函数，用于传递参数，返回实例对象，通过`new`命令生成对象实例时，自动调用该方法
  // 如果没有定义，类的内部会自动帮我们创建一个`constructor`来执行
  constructor(type, name, age) {
    this.type = type
    this.name = name
    this.age = age
  }
  
  // 类方法，内部处理会将方法放置在 prototype 上
  sayHello() {
    console.log(`${this.name}（${this.type}）向你打招呼`)
  }
  
  // 用箭头函数书写的方法，则 this 固定为当前实例
  // 且该方法不会挂载到原型上，而是直接在实例中，可以被枚举
  eat = () => {
    console.log(this) // 固定指向 new Animal() 的实例
  }
}

// 创建一个实例
const dog = new Animal('犬类', '小白', 3)
dog.sayHello() // 小白（犬类）向你打招呼


// 类的表达式书写方式
const Animal = class {}
const dog = new Animal()
```



## 静态属性`static`

**类的静态属性不能通过实例或者原型来访问，只能通过类本身访问**

```js
class Animal {
  constructor() {}
  static species = '哺乳动物'
}
// 或者
Animal.species = '哺乳动物'
```



## 继承`extends`

子类可以继承父类的实例属性和方法

```js
class Animal {
  constructor(name, age, legs) {
    // 限制 Animal 类不能直接只用 new 调用，需要被子类继承
    if (new.target === Animal) throw new Error('抽象类，不能直接实例化')
    
    this.name = name
    this.age = age
    this.legs = legs
  }
  sayHello() {
    console.log(`${this.name}向你打招呼`)
  }
}

class Cat extends Animal {
 constructor(name, age, legs) {
   // 手动调用父类的 constructor 构造函数
   super(name, age, legs)
   // 子类 Cat 自己的属性
   this.sound = '汪汪'
   this.type = '小猫'
  }
  
  sayHello() {
    super.sayHello() // 调用父类上的同名的方法，重用父类逻辑
    // Cat类自己的代码
    console.log(`我的种类是${this.type}`)
  }
}

const cat = new Cat('小黑', 2, 4)
cat.sayHello()
```

如果在子类中定义了`constructor`，则必须在`constructor`函数的第一行借助`super()`手动调用父类的构造函数，否则会报错
如果子类没有`constructor`，则会有默认的构造器，该构造器的参数和父类一致，并会自动调用父类构造器

![image-20250703185535212](./assets/image-20250703185535212.png)



## `get/set`

`class`类中的`get`和`set`是两个特殊的方法，**其作用是对类属性的读取和赋值操作进行自定义**

注意点：

- **`getter/setter`的名称不能和实际存储属性的名称相同**，否则会溢出最大调用栈报错
- `setter`必须接受且只能接受一个参数（即要设置的值），而`getter`不能有参数
- 可以只实现`getter`，这样该属性就会变成只读属性，但只实现`setter`是不允许的，否则在读取属性时会得到`undefined`
- ES6的`class`中的`getter/setter`其实是`Object.defineProperty()`的语法糖，二者功能相同

基本概念：

- `getter`：获取对象属性值的方法，使用`get`关键字定义对象的某个属性时，对应的`getter`方法就会被自动调用
- `setter`：设置对象属性值的方法，通过`set`关键字定义对象的某个属性赋值时，相应的`setter`方法会被触发

```js
/** 基本语法 **/
class MyClass {
  constructor() {
    this._property = 0
  }

  // 定义getter方法
  get property() {
    console.log('获取属性值')
    return this._property
  }
  // 定义setter方法
  set property(value) {
    console.log(`设置属性值为: ${value}`)
    if (value < 0) {
      this._property = 0 // 可以在setter里添加数据验证逻辑
    } else {
      this._property = value
    }
  }
}

// 使用示例
const obj = new MyClass()
console.log(obj.property) // 调用getter，输出: 获取属性值 0
obj.property = 10         // 调用setter，输出: 设置属性值为: 10
console.log(obj.property) // 调用getter，输出: 获取属性值 10
obj.property = -5         // 调用setter，输出: 设置属性值为: -5
console.log(obj.property) // 调用getter，输出: 获取属性值 0（经过了setter里的验证处理）
```

主要作用：

- **数据验证**：借助`setter`，能够在给属性赋值之前对数据进行检查，保证数据的有效性

- **计算属性**：可以根据其他属性动态地计算出属性值，无需将其存储为实体数据

  ```js
  class Rectangle {
    constructor(width, height) {
      this.width = width
      this.height = height
    }
    // 可以只实现 getter，这样该属性就会变成只读属性
    // 但只实现 setter 是不允许的，否则在读取属性时会得到 undefined
    // 面积属性是通过计算得到的，不需要单独存储
    get area() {
      return this.width * this.height
    }
  }
  const rect = new Rectangle(5, 10)
  console.log(rect.area) // 输出: 50
  ```

- **封装实现细节**：可以隐藏对象内部的实际属性，对外提供统一的访问接口

- **监听属性变化**：在`setter`中添加额外的逻辑，当属性值发生变化时执行相应操作

  ```js
  class Temperature {
    constructor() {
      this._celsius = 0
    }
  
    get celsius() {
      return this._celsius
    }
    set celsius(value) {
      this._celsius = value
      console.log(`温度已更新为 ${value}°C`)
    }
  
    get fahrenheit() {
      return (this._celsius * 9/5) + 32
    }
    set fahrenheit(value) {
      this._celsius = (value - 32) * 5/9
      console.log(`温度已更新为 ${this._celsius}°C`)
    }
  }
  const temp = new Temperature()
  temp.celsius = 25     // 输出: 温度已更新为 25°C
  console.log(temp.fahrenheit) // 输出: 77°F
  temp.fahrenheit = 32  // 输出: 温度已更新为 0°C
  console.log(temp.celsius)    // 输出: 0°C
  ```

 

# 构造函数

> 在ES6之前，对象不是基于类（class）创建的；
>
> 而是用叫**构造函数**的特殊函数来定义；

 

### 成员

> 构造函数中的属性和方法可以称为成员，可以添加；

​	

#### 实例成员

> 构造函数内部通过`this`添加的成员；
>
> 只能通过实例化的对象来访问，不可以通过构造函数访问；



#### 静态成员

> 直接在构造函数本身上添加的成员，只能通过构造函数来访问；

   

### prototype

> 构造函数通过原型（prototype）分配的函数是所有对象所共享的；
>
> 每一个**构造函数**都有一个`prototype`属性，指向另一个对象；
>
> `prototype`是一个对象，这个对象的所有属性和方法，都会被**构造函数**所拥有；

[^Rules]:一般情况下，公共属性定义到构造函数里面，公共的方法就放在原型对象（prototype）上；

 

### proto

> 实例对象都有一个`_proto_`属性，指向**构造函数**的`prototype`原型对象；
>
> 实例对象可以使用构造函数和 prototype 原型对象身上的属性和方法，就是因为`_proto_`属性的存在；

[^Focus]:实例对象身上的`_proto_`对象原型和构造函数身上的`prototype`是相等的；
[^Function]:在于为对象成员的查找规则提供一个方向和路线，叫做`原型链`；

 

### 查找原则

> 首先看实例对象本身是否有需要的方法和属性，如果有，就使用实例对象本身的方法和属性；
>
> 如果实例对象本身没有找到，就顺着`_proto_`属性指向的构造函数的原型对象`prototype`身上去查找需要的方法和属性；

 

### constructor

> 实例对象中的`_proto_`属性和构造函数的原型对象`prototype`属性里面都有一个`constructor`属性；
>
> 指向构造函数本身；
>
> 主要用于记录该对象引用于哪个构造函数；
>
> 一般手动添加`contructor`属性指向构造函数本身；



### 原型链

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

 

### 获取异步操作的结果

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

 

 

## 闭包( Closure )

> 可以访问另一个函数作用域中变量的函数，即**定义在一个函数内部的函数**；



### 形成条件

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



### 创建闭包

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



### 优缺点

> 根据场景需要，是否使用闭包；



#### 优点

> 可以延展变量的作用域，让外部访问函数内部变量成为可能；
>
> 隐藏变量以及防止变量被篡改和作用域的污染，从而实现封装；



#### 缺点

> 会造成内存泄漏[^Reason]；

[^Reason]:函数执行完后，函数内的局部变量没有释放，占用内存时间变长，容易造成内存泄漏；
[^Resolve]:让内部函数变成垃圾对象，赋值为`null`，及时释放，让浏览器回收闭包；



### 结论

> **闭包找到的是同一地址中父级函数中对应变量最终的值**；

​		

## 递归

[^定义]:如果一个函数在内部自己调用本身，那这个函数就是**递归函数**；	
[^Focus]:递归函数容易发生“栈溢出”错误( stack overflow )，**必须要有退出条件；** 

```javascript
// 利用递归函数求1～n的阶乘：
function number(n) {
  if (n === 1) { // 添加递归函数的退出条件，否则就会发生错误，死循环；
    return 1;
  }
  return n * number(n - 1); //自己在函数内部调用本身
};
console.log(number(6));/*720*/


// 利用递归函数求斐波那契数列（兔子序列）：1 1 2 3 5 8 13 21 34 55 ...，输入n就可以知道与之对应的序列值；
// 理解：知道（n-1）和（n-2）的序列值就可以知道n的序列值 = （n-1）+（n-2）；
function fbnq(n) {
  // 添加递归函数的退出条件，否则就会发生错误，死循环
  if (n === 1 || n === 2) {
    return 1;
  };
  //自己在函数内部调用本身
  return fbnq(n - 1) + fbnq(n - 2);
};
console.log(fbnq(10));/*55*/


// 利用递归函数实现数据的 深度拷贝
var apple = [
  {
    id: 1,
    brand: 'Iphone',
    model: ['iphone8 Plus', 'iphone11', 'iphone12 Pro', 'iphone13ProMax']
  },
  {
    id: 2,
    brand: 'iMac',
    model: ['iMac Pro', 'iMac 4K', 'iMac 6K', 'iMac Display']
  },
  {
    id: 3,
    brand: 'Watch',
    model: ['Watch Series 3', 'Watch SE', 'Watch Series 6', 'Watch Series 7']
  }
]

var copy = []
function deepCopy(creat, target) {
  for (var index in target) {
    // 获取属性值；
    var item = target[index];
    // 判断属性值是哪一种数据类型；
    if (item instanceof Array) {
      // 判断属性值是否是数组；
      creat[index] = [];
      deepCopy(creat[index], item);
    } else if (item instanceof Object) {
      // 判断属性值是否是对象；
      creat[index] = {};
      deepCopy(creat[index], item);
    } else {
      creat[index] = item;
    }
  }
}
deepCopy(copy,apple)
```

 

## 节流 / 防抖

[^Question]:事件因为用户的频繁操作而被触发，每次都会去执行操作，可能会造成**卡顿现象**；



### 节流

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



### 防抖

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



# ES6+



## 对象简写

直接在内部写变量，当成一个属性；从而省略属性值；

```javascript
const name = 'Kein'
const age = 26
const gender = 'Female'

const variablefield = 'hobby'

const objet = {
  // 当属姓名和属性值单词一样时，可以省略键值
  name,
  age,
  gender,
  // 对象方案简写，相当于 export: function() {}
  export() {
    console.log(this.name)
  },
  // 计算属性名
  [variablefield]: '骑行'
}
```



## 解构赋值

按照一定模式从数组和对象中提取值，再对变量进行赋值

### 数组

```javascript
const F4 = ['小沈阳','刘能','赵四','宋小宝']
let [a, b, c, d] = F4

// 这样结构也行
const {
  0: one,
  1: two
} = F4
```

### 对象

```javascript
const person = {
  name: '赵本山',
  power: function() {
    console.log('我是小品演员！')
  }
}
let { name, age = 40 } = person
// age 有一个默认值 40，假如解构 age 的结果是 undefined ，则使用默认值
```

### 连续解构

```javascript
const list = {
  keys: {
    left: 'A',
    right: 'D'
  },
  mouse: {
    clickLeft: 'mouse-left',
    middle: 'mouse-middle',
    clickRight: 'mouse-right'
  }
}

// 解构赋值
let { keys } = list
// 等同于 -->
let keys
keys = list.keys
// 连续解构赋值
const { keys:{ left } } = list // 'A'
// 连续解构并且更改变量名 为 keyLeft
const { keys:{ left: keyLeft } } = list // 'A'
```



# `Set()`

集合，成员的值都是唯一的

- 自带元素去重功能，集合里的值都是唯一的
- 可以接收一个可迭代对象参数得到一个初始集合数据
- `Set`本身就是一个可迭代对象，所以可以使用扩展运算符`...`和`for-of`循环遍历

```js
console.log(new Set()) // Set(0) {size: 0}
console.log(new Set('aabbccdd')) // Set(4) {'a', 'b', 'c', 'd'}
console.log(new Set([0, 2, 0, 4, 3, 1, 2])) // Set(5) {0, 2, 4, 3, 1}

// 利用`...`，数据去重，并得到一个新数组
const arr = [...new Set([1, 3, 3, 1, 2, 5, 2])] // [1, 3, 2, 5]
// 字符串去重
const str = [...new Set('cdhjdcbhdwqeqcd')].join('') // 'cdhjbwqe'
```



## `.size`

返回集合里的元素个数，只读属性，不可复制

```javascript
const array = ['Iphone', 'Huawei', 'Oppo', 'Vivo']
new Set(array).size // 4
```



## `.add()`

给当前的集合在元素的最后面添加一个新元素，可以进行链式调用
当添加的是已经存在的元素，操作无效，内部使用的是`Object.is()`来判断两个数据是否相同，但`Set`认为`+0`和`-0`相同

```javascript
const array = ['Iphone', 'Huawei', 'Oppo', 'Vivo']
new Set(array).add('Xiaomi').add('Xiaomi') // 只会添加一次
```



## `.delete()`

删除当前集合里的元素，返回的是布尔`boolean`值，是否删除成功，内部判断和`.add`一样

```javascript
const array = ['Iphone', 'Huawei', 'Oppo', 'Vivo']
new Set(array).delete('Xiaomi')
```



## `.has()`

检测集合里是否包含某个元素，返回的是布尔`boolean`值

```javascript
const array = ['Iphone', 'Huawei', 'Oppo', 'Vivo']
new Set(array).has('Iphone') // true
```



## `.clear()`

清空当前集合里的所有元素

```javascript
const array = ['Iphone', 'Huawei', 'Oppo', 'Vivo']
new Set(array).clear()
// array: []
```



## `.forEach()`

集合中不存在下标，因此`forEach`中的第二个参数和第一个参数是一致的，均表示集合中的每一项

```js
const list = new Set(['a', 'b', 'c', 'd'])
list.forEach((item, index, arr) => {
  console.log(item === index) // true
})
```



# `WeakSet()`

**弱集合**，和`Set`类似，都是**不重复的值的集合**
**成员只能是对象，且都是弱引用的对象**，适合临时存放一组对象和跟对象绑定的信息
没有`size`属性，无法遍历（不是可迭代对象)，只有`add、delete、has、clear`方法

```js
// 实例
const weakSet = new WeakSet()
let obj = { name: 'Kyle' }
weakSet.add(obj) // add

// 但当`obj`赋值为`null`时，此时若除了集合中没有其他变量保存了对象的引用
// 则集合中保存的数据也会被丢弃删除，弱集合储存的对象引用不会影响js的垃圾回收机制
obj = null
console.log(weakSet) // 无属性-空

weakSet.add(window)
weakSet.has(window) // true
weakSet.delete(window)
```



# `Map()`

是**键值对的有序列表**，键和值都可以是任意类型，键不可重复
可以传递一个可迭代对象参数来初始化，该参数要求每次迭代的都是一个长度为2的数组（可迭代对象），形如`[键， 值]`
产生的实例也是一个可迭代对象，同样可以使用`...`扩展运算符和`for-of`遍历

```js
const map = new Map([['name', 'Kyle'], ['gender', 'Male']]) // Map(2) {'name' => 'Kyle', 'gender' => 'Male'}

const arr = [...map]

for (const [key, value] of map) {
  console.log(key, value)
}
```



## `.size`

`size`属性返回map的成员总数

```javascript
const map = new Map()
map.set('foo', true)
map.set('bar', false)
map.size // 2
```



## `.set()`

设置**键名`key`**对应的**键值为`value`**，然后返回整个map结构，即可以使用链式写法
如果`key`已经有值，则键值会被更新，否则就新生成该键并映射值
比较键的方法和`Set`一样，都是使用`Object.is()`判断

```javascript
const m = new Map()

m.set('edition', 6)        // 键是字符串
m.set(262, 'standard')     // 键是数值
m.set(undefined, 'nah')    // 键是 undefined
m.set(1, 'a').set(2, 'b').set(3, 'c') // 链式操作
```



## `.get()`

读取`key`对应的键值，如果找不到`key`，返回`undefined`

```javascript
const m = new Map()
m.set('hello', 'Hello ES6!')
m.get('hello')  // Hello ES6!
```



## `.has()`

返回一个**布尔值**，表示某个键是否在当前map结构中

```javascript
const m = new Map()
m.set('edition', 6)
m.set(262, 'standard')
m.set(undefined, 'nah')

m.has('edition') // true
m.has('years') // false
m.has(262) // true
m.has(undefined) // true
```



## `.delete()`

方法删除某个键和对应的值，返回`true`，如果删除失败，返回`false`

```javascript
const m = new Map()
m.set(undefined, 'nah')
m.has(undefined)     // true

m.delete(undefined)
m.has(undefined)       // false
```



## `.clear()`

清除所有成员，没有返回值

```javascript
const map = new Map()
map.set('foo', true)
map.set('bar', false)

map.size // 2
map.clear()
map.size // 0
```



## `.keys()`

返回map键名的迭代器

```javascript
const map = new Map([['F', 'no'],['T',  'yes']])
const mapKeys = map.keys() // ['F', 'T']
```



## `.values()`

返回键值的遍历器

```javascript
const map = new Map([['F', 'no'],['T',  'yes']])
const mapValues = map.values() // ['no', 'yes']
```



## `.entries()`

返回所有成员的遍历器

```javascript
const map = new Map([['F', 'no'],['T',  'yes']])
const mapEntries = map.entries() // [['F', 'no'],['T',  'yes']]
```



## `.forEach()`

遍历映射中的所有成员，`forEach((值, 键, map) => {})`

```javascript
const map = new Map([['F', 'no'],['T',  'yes']])
map.forEach(value, key, map) {
  console.log(value) // 'no'、'yes'
  console.log(key) // 'F'、'T'
  console.log(map) // [['F', 'no'],['T',  'yes']]
}
```



### WeakMap()

是**弱映射**，是不可迭代对象
**弱映射中的键只能是Object或者继承自Object的类型，值的类型没有限制**
**弱键**，意思是不属于正式的引用，不会阻止垃圾回收
弱映射中的**值**不是，只要键存在，键/值对就会存在于映射中

```js
const wm = new WeakMap()
wm.set({}, "val")

const container = {
key: {}
}
wm.set(container.key, "val")
// 调用 removeReference()，会摧毁键对象的最后一个引用，垃圾回收程序就可以把这个键/值对清理掉
function removeReference() {
container.key = null;
}
```



### async / await

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

> `await`必须写在 async 函数中；
>
> `await`右侧的表达式一般为 promise 对象；
>
> `await`返回的是 promise 成功的值；
>
> `await`的 promise 失败了，就会抛出异常，需要通过`try...catch`捕获处理；



### 数组方法



#### .includes(element)

> 方法用来检测数组`array`中是否包含某个元素`element`；
>
> 返回布尔类型值；
>
> ```javascript
> array.includes(item);
> // 返回布尔类型值（true/false）
> ```



### 对象方法



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



# All in Js

> JavaScript 中的技巧和小知识；



## base64 上传接口

base64 图片转 file，并上传到接口；

```js
const img = document.getElementById('img')

// base64 --> file
const base64ToFile = async (imgSrc, imgName) => {
const bInfo = await fetch(imgSrc)
const imgBlob = await bInfo.blob()
return new File([imgBlob], imgName)
}

// upload
const upload = async () => {
const file = await base64ToFile(img.src, 'test.png')
const form = new FormData()
form.append('file', file)
fetch('/api/upload', {
 method: 'POST',
 body: form
}).then(async (res) => {
 const json = await res.text()
 console.log(json)
})
}
```



# ESLint

> 一个可组装的`JavaScript`和`JSX`代码检查工具；
>
> ```js
> // 在整个文件中取消代码语法检查
> /* eslint-disable */
> 
> // 对 当前行 取消代码语法检查
> // eslint-disable-line
> 
> // 对 下一行 取消代码语法检查
> // eslint-disable-next-line
> ```



