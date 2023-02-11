## TypeScript ？

> 以 JavaScript 为基础构建的语言，是 JavaScript 的超集，扩展了 JavaScript ，并添加了类型校验；
>
> 可以在任何支持 JavaScript 的平台上执行，但 TypeScript 不能直接被 JavaScript 解析器直接执行，需要编译成 JS；
>





## 环境搭建

> 1. 下载 Node.JS；
>
> 2. 安装 TypeScript；
>
> ```shell
> # 使用 `npm` 全局安装
> sudo npm install typescript --global
> ```
>
> 3. 创建一个 `ts` 文件；
> 4. 使用 `tsc` 对 `ts` 文件进行编译；
>
> ```shell
> # 在要编译的 `ts` 文件所在的目录打开终端；执行编译命令；
> 
> tsc filename.ts
> 
> # 监视 TS 文件，发生改动时自动编译成 JS 文件；
> tsc filename.ts -w
> ```





## 类型声明

> 在 TS 中进行变量的数据类型的约束和声明；
>
> ```typescript
> // 在声明变量的时候指定数据类型；
> 
> // 语法 ---> 变量: 数据类型
> let variableName: dataType;
> ```



### 基本类型

> 对原始的基本类型数据进行类型声明和约束；
>
> ```typescript
> export {}; // 第一行增加这个是为了使文件里的变量不污染全局
> ```



#### string

> ```typescript
> // 字符串 类型
> 
> let str: string = "2";
> // str = 2，
> // 此时赋值会报错，以为值的类型只能是 string 类型
> 
> // 如果不书写类型，typescript会根据数据来进行推断
> let str = 'Hello'
> // 此时 str 的数据类型为 string
> ```



#### number

> ```typescript
> // 数字 类型
> 
> // 声明一个变量，同时指定它的数据类型是 number ；
> // 数据类型设置为 number 后，在之后的使用过程中 a 的类型只能是 number ；
> let a: number;
> a = 10;
> // a = 'hello';
> // 此时会报错，不能将 string 类型数据赋值给 number 数据类型；
> ```



#### boolean

> ```typescript
> // 布尔 类型
> 
> let bool: boolean = true;
> ```



#### **symbol**

> ```typescript
> // symbol 类型
> 
> let sy: symbol = Symbol();
> ```



#### null

> ```typescript
> // null 类型，表示对象或者属性是空值
> 
> let nul:null = null;
> ```



#### undefined

> ```typescript
> // undefined 类型
> 
> let undef: undefined = undefined;
> ```



### 非原始类型

> 对非原始类型数据进行类型约束；



#### 对象( object )

> ```typescript
> // object 类型；
> let obj: object = {name: 'Kein'}
> let obj_1: object = [1, 2, 3] // 数组也属于对象类型
> 
> let objectData: { name: string, age: number } // 此书写方式可以约束对象中的属性
> // 表示 objectData 对象中有 name 和 age 两个属性，前者为 string 类型， 后者为 number 类型
> // 少书写其中一个或多个属性，或者多写属性，类型检查时都会报错
> objectData = {
>   name: 'Kein',
>   age: 22
> }
> 
> // 属性后面加 ? 号，表示该属性是可选的，可有可无，不书写也不会报错
> let object_a: { name: string, grade?: string }
> object_a = { name: 'Kein' }
> 
> // 表示该对象至少要有一个 name 属性，且为字符串类型值，其他属性的数据类型和个数都是任意的
> let object_b: { name: string, [propName: string]: any }
> object_b = { name: 'Kein', gender: 'Male', age: 23 }
> 
> // 不确定对象的属性时
> let object_c: { [key: string]: string }
> // 表示对象中属性和值的类型都是 string
> 
> // 错误例子
> let obj1: object = 3
> let obj2: object = "3"
> let obj3: object = true
> let obj4: object = null
> let obj5: object = undefined
> ```

[^Tip]:大Object，代表所有原始类型、非原始类型都 可以赋给 Object，严格模式下不包括null，undefined{}空对象类型和大 Object 一样；



#### 数组( array )

> ```typescript
> // 数组 类型的声明
> 
> let arrayA: string[] // 表示字符串类型元素的数组
> let arrayB: number[] // 表示数字类型元素的数组
> let arrayC: {}[] // 表示对象类型元素的数组
> ```



#### 函数( function )

> ```typescript
> // 函数 类型
> 
> // 此时表示，函数的两个参数和返回值的类型都是 number 类型
> const add = (a: number, b: number): number => {
>   return a + b
> }
> 
> // 使用 类型别名 定义类型
> type addFnType = (a: number, b:number) => number
> let addFn: addFnType = (num1, num2) => {
>   return num1 + num2
> }
> 
> // 可缺省参数
> function log(msg?: string): void {
>   console.log(msg)
> }
> 
> // 参数默认值
> function addFn1(num1: number = 1, num2: number = 2): number {
>   return num1 + num2
> }
> 
> // 剩余参数
> function sum(...nums: number[]) {
>   return nums.reduce((a, b) => a + b, 0)
> }
> sum(1, 2) // => 3
> sum(1, 2, 3) // => 6
> ```

[^Tip]:可缺省和 string | undefined 不等价，string | undefined 的意思是这两个类型中的一种，而可缺省是这个属性或参数可有可无的意思；



#### 元组

> ```typescript
> // 元组 类型，即是固定长度的数组
> 
> let tupleA: [string, string]
> // 此时只允许该元组拥有 2 个元素，不能多也不能少，且类型都为 string
> tupleA = ['Kein', 'ZouKai']
> ```



#### 枚举( enum )

> ```typescript
> // 枚举 类型
> // 作用在于定义被命名的常量集合
> // 一个默认从 0 开始递增的数字集合，称之为数字枚举
> // 也可以 指定值，这里可以指定的值可以是数字或者字符串
> 
> enum Gender {
>   male = 0,
>   female = 1,
>   unknown // 默认为 2
> }
> let enumA = {
>   name: 'Kein',
>   // gender: 0, // 此时 gender = male
>   gender: Gender.male // 此时 gender = 0
> }
> ```



#### 字面量

> ```typescript
> // 字面量不仅可以表示值，还可以表示类型，限制变量的值就是该字面量
> // typeScript 支持 number string boolean 三种字面量类型
> 
> let x3: 1 | 3 = 1
> x3 = 3
> x3 = 2 // 报错
> 
> let x4:'hello' = 'hello'
> x4 = 'hi' // 报错
> ```

[^Tip]:以字面量表示类型，即表示除了字面量之外的其他值都会报错；



#### void

> ```typescript
> // 用在函数没有返回值的情形下
> 
> function fn(): void {
>   // ...
>   return undefined
> }
> 
> let vd: void = undefined
> // 可以把 undefined 赋值给 void 类型，但是反过来不行
> 
> // 设置函数的参数类型和返回值类型
> let fn: (x: number, y: number) => number;
> fn = (x, y) => {
>   return x + y
> }
> ```



#### any

> 任意类型，是官方提供的一个选择性**绕过静态类型检测**的作弊方式；
>
> 非常不建议使用，**Any is Hell**(**Any** 是地狱)；
>
> ```typescript
> // any会绕过类型检测，所以下面不会有问题提示
> let an: any
> an.toFixed(2)
> an[0] = 'any'
> ```



#### unknown

> 是 TypeScript 3.0 中添加的一个类型，主要用来描述类型并不确定的变量；
>
> 和 any 的区别就是**会进行类型检测**；
>
> ```typescript
> let unk: unknown
> let x = 1
> let y = "2"
> if (x) {
>   unk = x
> } else {
>   unk = y
> }
> 
> // 通过缩小类型可以通过类型检测
> if (typeof unk === 'number') {
>   unk.toFixed(2)
> }
> ```

[^Tip]:可以把任何类型的值赋值给**unknown**，但是**unknown**类型的值只能赋值给**any**或者**unknown**；



#### never

> ```typescript
> // never 类型，表示永远不会有值的类型
> function throwErrFn():never {
>   throw new Error('出错了')
> }
> 
> // 如果函数里是死循环，返回值类型也是never
> // never 是所有类型的子类型
> ```



#### 联合类型

> ```typescript
> // 可以把 | 类比为 JavaScript 中的逻辑或 ||，只不过前者表示的是类型
> 
> let age: number | string = 20
> age = '23'
> ```



#### 交叉类型

> ```typescript
> // & 类似于 JavaScript 中的逻辑且 &&
> 
> let zs: { name: string; age: number } & { height: number }
> zs = {
>   name: "张三",
>   age: 20,
>   height: 180,
> }
> ```





## 接口( interface )

> 通过 interface 接口来声明数据类型；
>
> 可以将内联类型抽离出来，从而实现类型可复用；



### 声明类型

> 使用接口定义变量和函数参数的类型；
>
> ```typescript
> // 定义对象的类型
> interface PersonInfo {
>   name: string; // 不是类似对象般用 , 隔开，而是 ;
>   age: number;
> }
> let zhangsan: PersonInfo = {
>   name: "张三",
>   age: 20
> }
> 
> // 定义数组的类型
> interface ArrayNumber {
>   [idx: number]: number;
> }
> let arr1: ArrayNumber = [1, 2, 3]
> 
> // 定义函数的类型
> interface PersonFn {
>   (p: PersonInfo): void;
> }
> let Person1: PersonFn = (obj: PersonInfo): void => {
>   console.log(obj.name, obj.age);
> }
> 
> // 定义 类Class 的成员及类型
> interface myInterface {
>   // 接口中所有属性都不能有实际的值；
>   // 接口中的方法都是抽象方法；
>   name: string;
>   age: number;
>   sayHello(): void;
> };
> 
> // 用接口去限制类的属性和方法
> // 用来定义一个类的结构，指定一个类中应该包含哪些属性和方法
> class myClass implements myInterface {
>   name: string; // 声明 this.name 的数据类型
>   age: number;
>   constructor(name: string, age: number) {
>     this.name = name;
>     this.age = age;
>   }
>   sayHello = () => {
>     console.log('Hello !');
>   }
> }
> 
> // 缺省和只读特性
> interface PersonInfo {
>   name?: string; // 缺省
>   readonly height: number; // 只读
> }
> ```

[^Tip]:很少使用接口类型来定义函数的类型，更多使用内联类型或类型别名配合箭头函数语法来定义函数类型；



### 继承接口

> 多个不同接口之间是可以实现继承的，会组合成一个新的接口；
>
> 但是如果继承的接口被继承的接口有相同的属性，并且类型不兼容，那么就会报错；
>
> ```typescript
> interface NameInfo {
>   name: string;
> }
> interface AgeInfo {
>   age: number;
> }
> interface PersonInfo extends NameInfo, AgeInfo {
>   height: number;
> }
> let zs: PersonInfo = {
>   name: "张三",
>   age: 20,
>   height: 177
> }
> ```



### 接口合并

> 多个相同名字的接口，会进行合并，得到一个新的接口；
>
> **即：重复定义的接口类型，它的属性会叠加**；
>
> 这个接口的特性一般用在扩展第三方库的接口类型；
>
> ```typescript
> interface PersonInfo {
>   name: string;
>   age: number;
> }
> interface PersonInfo {
>   name: string;
>   height: number;
> }
> let zs: PersonInfo = {
>   name: "张三",
>   age: 20,
>   height: 177
> }
> ```





## 类型别名( type )

> 使用类型别名接收抽离出来的内联类型实现复用，
>
> 格式: `type 别名名称 = 类型定义`；



### 定义别名

> ```typescript
> type PersonInfo = { name: string; age: number }
> 
> let zs: PersonInfo = {
>     name: "张三",
>     age: 20
> }
> ```

[^Focus]:与接口不同的是，类型别名重复定义，不会叠加属性而是会报错；



### 使用场景

> 某些情况下，接口 interface 接口和 type 类型别名没有太大区别；
>
> 类型别名可以针对接口没法覆盖的场景，例如组合类型、交叉类型等；
>
> ```typescript
> // 1. 联合类型
> type NumAndString = number | string
> let age: NumAndString = 22
> age = '23'
> 
> // 2. 交叉类型
> type SectionType = { name: string; age: number } & {
>     height: number;
>     name: string;
> }
> let zs: SectionType = {
>     name: "张三",
>     age: 20,
>     height: 180
> }
> 
> // 3. 提取接口中属性的类型
> interface PersonInfo {
>     name: string;
>     height: number;
> }
> type PersonHeight = PersonInfo["height"]
> let altitude: PersonHeight = 8848
> ```





## 泛型( <  > )

> 指的是**类型参数化**，即将原来某种具体的类型进行参数化；
>
> 设计泛型的目的在于有效约束类型成员之间的关系，比如函数参数和返回值、类或者接口成员和方法之间的关系；



### 泛型参数

> 在函数执行传参时才确定参数数据的类型；
>
> ```typescript
> // 使用 泛型 对函数参数的类型进行声明
> function getValue_1<T>(val: T): T {
>   return val
> }
> let g4: number = getValue_1<number>(3)
> let g5: string = getValue_1<string>('4')
> 
> // 多个泛型
> function add<T, G>(x: T, y: G): T {
>   return x + y
> }
> const result: string = add<string, number>('Kine', 23)
> ```



### 泛型类型

> 在 TypeScript 中，类型本身就可以被定义为拥有不明确的类型参数的泛型；
>
> 并且可以接收明确类型作为入参，从而衍生出更具体的类型；
>
> ```typescript
> // 定义数组类型
> let arr: Array<number> = [1]
> let arr1: Array<string> = [""];
> 
> // 类型别名
> type typeFn<P> = (params: P) => P;
> let fntype: typeFn<number> = (n: number) => {
>   return n
> }
> 
> // 定义接口
> interface TypeItf<P> {
>   name: P;
>   getName: (p: P) => P;
> }
> let t1: TypeItf<number> = {
>   name: 123,
>   getName: (n: number) => {
>     return n
>   }
> };
> let t2: TypeItf<string> = {
>   name: "123",
>   getName: (n: string) => {
>     return n
>   }
> }
> ```



### 泛型约束

> 把泛型入参限定在一个相对更明确的集合内，以便对入参进行约束；
>
> ```typescript
> interface TypeItf<P extends string | number> {
>   name: P;
>   getName: (p: P) => P;
> }
> let t1: TypeItf<number> = {
>   name: 123,
>   getName: (n: number) => {
>     return n
>   }
> }
> let t2: TypeItf<string> = {
>   name: "123",
>   getName: (n: string) => {
>     return n
>   }
> }
> ```





## 类( class )

> ```typescript
> class Person {
>      // 定义实例属性,实例属性需要通过实例化的对象去访问；
>      name: string = 'Kein'
> 
>      // 在属性前加 static 关键字，定义类属性（静态属性），只能通过 class 类去访问；
>      static age: number = 22
> 
>      // 在属性前加 readonly 关键字，表示这个属性只能读不能改；
>      readonly gender: string = 'Male'
> 
>      // 定义方法；
>      sayHello() {
>        console.log('Hello!My Name Is' + this.name);
>      }
> };
> 
> const people = new Person();
> people.sayHello()
> ```



### 修饰符

> 通过修饰符做到控制属性和方法的访问；
>
> [^public]:基类、子类、类外部都可以访问；
> [^protected]:基类、子类可以访问，类外部不可以访问；
> [^private]:基类可以访问，子类、类外部不可以访问；
> [^readonly]:只读修饰符；
> [^static]:静态属性，可以避免数据冗余，提升运行性能；
>
> ```typescript
> class Person {
>   public readonly name: string = '张三'; // 公共属性，且只读
>   protected age: number = 20;
>   private height: string = '180';
>   protected getPersonInfo():void {
>     console.log(this.name, this.age, this.height); // 基类里面三个修饰符都可以访问
>   }
>   static title: string = "个人信息";
> }
> 
> class Male extends Person {
>   public getInfo():void {
>     console.log(this.name, this.age); // 子类只能访问 public、protected 修饰符的属性
>   }
> }
> 
> let m = new Male()
> console.log(m.name) // 类外部只能访问 public 修饰的属性
> m.name = '李四' // name 属性使用只读修饰符，所以不能对name进行赋值修改操作
> ```



### constructor()

> 类 class 里面的构造函数，把公共属性写在里面；
>
> ```typescript
> class Car {
>      brand: string;
>      color: string;
>      maxSpeed: number;
>      isSale: boolean;
>      constructor(brand: string, color: string, maxSpeed: number, isSale: boolean) {
>        this.brand = brand;
>        this.color = color;
>        this.maxSpeed = maxSpeed;
>        this.isSale = isSale
>      }
> };
> 
> const bmw = new Car('BMW', 'White', 300, true);
> console.log(bmw);
> ```



### 类的继承

> 子类使用继承后，会拥有父类的所有属性和方法；
>
> 通过继承可以将多个类共有的属性和方法写在一个父类中；
>
> 子类中的属性和方法与父类同名，使用子类的；
>
> 子类的 constructor 构造函数必须通过 super 关键字调用；
>
> ```typescript
> // 类的继承；
> class Animal {
>      species: string
>      age: number
>      gender: string
>      constructor(species: string, age: number, gender: string) {
>        this.species = species;
>        this.age = age;
>        this.gender = gender;
>      }
>      call() {
>        console.log(this.species + '的性别是：' + this.gender);
>      }
>   };
> 
> class Dog extends Animal {
>     // 子类的 constructor 构造函数必须通过 super 关键字调用；
>      // 否则就会对父类的 constructor 构造函数进行重写，而不是继承；
>      hair: string
>      voice: string
>      constructor(species: string, age: number, gender: string, hair: string, voice: string) {
>        // 调用父类的 constructor 构造函数；
>        super(species, age, gender)
>        this.hair = hair;
>        this.voice = voice
>      }
>      hairColor() {
>        console.log(this.species + '的毛发的颜色是' + this.hair)
>      }
>   };
> 
> var dog = new Dog('狗', 3, 'Male', '金色', '汪汪汪 ！');
> dog.hairColor()
> ```



### 抽象类

> 在类的前面加 `abstrsct` 关键词；
>
> 抽象类不能用来直接实例化对象；
>
> 抽象类的作用就是专门当作父类去被继承，是一种不能被实例化仅能被子类继承的特殊类；
>
> ```typescript
> abstract class Father {
>      name: string
>      age: number
>      constructor(name: string, age: number) {
>        this.name = name;
>        this.age = age;
>      }
>      // 定义一个抽象方法，在方法的前面加 abstract 关键字；
>      // 抽象方法只能定义在抽象类中，且子类必须对抽象类方法进行重写；
>      abstract sayHello(): void;
> };
> 
> class Son extends Father {
>      // 重写父类的抽象方法；
>      sayHello() {
>        console.log('My Name Is ' + this.name);
>      }
> };
> let person = new Son('Kein', 22);
> console.log(person);// Son { name: 'Kein', age: 22 };
> ```



### 属性封装

> 对象属性的值可以被任意的修改将会导致对象中的数据变得非常不安全；
>
> 类的 `get` 和 `set` 方法；
>
> ```typescript
> class Pro {
>      // 增加 peivate 关键字前缀，表示该属性私有化，外部无法访问；
>      // 默认的前缀是 public ，即公开性属性；
>      private _name: string;
>      private _age: number;
>      constructor(name: string, age: number) {
>        this._name = name;
>        this._age = age;
>      }
>      // 设置属性的读取和更改方法，让外界能访问到 private 私有属性；
>      // getAge(){
>      //   return this.age;
>      // }
>      // setAge(newValue: number){
>      //   this.age = newValue;
>      // }
> 
>      // TS 中提供的get、set 属性的的方法；
>      get age() {
>        return this._age;
>      }
>      set age(newValue: number) {
>        if (newValue >= 0) {
>          this._age = newValue;
>        };
>      }
>   };
> 
> const one = new Pro('Kein', 22);
> // 访问 name 属性；
> console.log(one.age);
> // 设置 name 属性；
> one.age = 23;
> ```

[^注意]:属性的名称不能和 `get` 和 `set` 方法的名称一样，否则会报错；





## 类型工具( Tools )

> 使用一些特定的限定词，诗定义类型时更加灵活；



### declare

> 类型增强；
>
> ```html
> <script>
>   var globalVar = 'globalVar变量'
>   var globalObj = { name: '', age: 20 }
>   function fn(str) {
>     console.log('fn函数' + str)
>   }
> </script>
> ```
>
> 如果上面几个变量和函数没有在全局做声明，会报类型错误，在我们在 types 文件夹中创建 common.d.ts 文件；
>
> ```typescript
> declare var globalVar: string
> type ObjType = { name: string; age: number }
> declare var globalObj: ObjType
> // 声明函数fn类型
> declare function fn(s?: string): void
> ```



### extends

> 类、接口、类型继承；
>
> ```typescript
> type TypeFn<P> = P extends string | number ? P[] : P
> let m: TypeFn<number> = [1, 2, 3]
> let m1: TypeFn<string> = ['1', '2', '3']
> let m2: TypeFn<boolean> = true
> ```



### infer

> 类型推断；
>
> ```typescript
> type ObjType<T> = T extends { name: infer N; age: infer A } ? [N, A] : [T]
> let p: ObjType<{ name: string; age: number }> = ["张三", 1]
> let p1: ObjType<{name: string}> = [{name: '张三'}]
> ```



### keyof

> 提取对象属性名、索引名、索引签名的类型；
>
> ```typescript
> interface NumAndStr {
>   name: string;
>   age: number;
>   [key: number]: string | number;
> }
> type TypeKey = keyof NumAndStr // number | 'name' | 'age'
> let t:TypeKey = 'name'
> ```



### in

> 映射类型；
>
> ```typescript
> type NumAndStr = number | string
> type TargetType = {
>   [key in NumAndStr]: string | number;
> }
> let obj: TargetType = {
>   1: '123',
>   "name": 123
> }
> ```

[^Focus]:`in`和`keyof`只能在**类型别名( type )**定义中使用；	



### typeof

> 在类型上下文中获取变量或者属性的类型；
>
> ```typescript
> // 推断变量的类型
> let strA = "2"
> type KeyOfType = typeof strA // string
> // 反推出对象的类型作为新的类型
> let person = {
>   name: '张三',
>   getName(name: string):void {
>     console.log(name)
>   }
> }
> type Person = typeof person
> ```





## 第三方库类型声明

> 在项目中使用第三方库，使用 TS 对其进行类型声明；



### jquery

> ```typescript
> console.log($("#app"))
> $.ajax()
> // 此时没有在全局声明，会报错误
> ```
>
> 新建全局类型声明文件夹`types`，在文件夹中新建`jquery.d.ts`文件对 jquery 进行类型声明；
>
> ```typescript
> declare function $(n: string):any
> /* declare let $: object; 重复声明会报红 */
> declare namespace $ {
>   function ajax():void;
> }
> ```
>
> namespace 的扩展；
>
> ```typescript
> // 全局变量的声明文件主要有以下几种语法: declare var 声明全局变量
> declare function // 声明全局方法
> declare class // 声明全局类
> declare enum // 声明全局枚举类型
> declare namespace // 声明(含有某方法的)全局对象
> // interface 和 type 声明全局类型
> ```





## 编译( Compile )

> 配置自定义编译 TS 文件；
>
> 项目根目录下创建 `tsconfig.json`  ts 编译器文件，将配置写在里面；



#### include

> 用来指定哪些 TS 文件在执行 `tsc` 命令时需要被编译；
>
> ```json
> {
>   "include":[
>     "./code/**/*.ts"
>   ]
> }
> ```

[^/**]: 表示当前目录下的任意文件夹；
[^/*]: 表示当前文件夹下的任意文件；



#### exclude

> 用来指定哪些 TS 文件不需要被编译；
>
> ```json
> {
>   "exclude": [
>     "./code/**/*"
>   ]
> }
> ```

[^/**]: 表示当前目录下的任意文件夹；
[^/*]: 表示当前文件夹下的任意文件；



#### compilerOptions

> 编译器选项；
>
> ```json
> {
>     "compilerOptions": {
>        // target 属性，用来指定 TS 被编译后的 JS 版本，默认是编译成 ES3 ；
>        "target": "ES5",
>        // module 属性，指定按照哪种模块化标准规范；
>        "module": "es2015",
>        // lib 属性，指定项目中要使用的库，一般不写；
>        "lib": [],
>        // outDir 属性，指定编译后的 JS 文件所在的目录；
>        "outDir": "./code/js",
>        // outFile 属性，将所有全局作用域的 JS 文件合并到一个文件中，一般不写；
>        "outFile": "./code/js/app.js",
>        // allowJs 属性，是否编译目录里的 JS 文件，默认是 false ；
>        "allowJs": false,
>        // checkJs 属性，是否检查 JS 代码语法是否符合规范，默认是 false ；
>        "checkJs": false,
>        // removeComments 属性，是否移除掉代码注释；
>        "removeComments": false,
>        // noEmit 属性，是否生成编译后的 JS 文件；
>        "noEmit": false,
>        // noEmitOnError 属性，当有错误时，不生成编译后的 JS 文件；
>        "noEmitOnError": true,
>        // strict 属性，所有严格检查的总开关；
>        "strict": true,
>        // alwaysStrict 属性，是否在编译后的 JS 文件里使用严格模式；
>        "alwaysStrict": false,
>        // noImplicitAny 属性，指定不允许隐式的 : any 数据类型；
>        "noImplicitAny": true,
>        // noImplicitThis 属性，指定不允许不明确指向的 this ；
>        "noImplicitThis": true,
>        // strictNullChecks 属性，指定是否严格的检查空值；
>        "strictNullChecks": true
>     }
> }
> ```





## 打包( Webpack )

> ```javascript
> module.export = {
>   module: {
>     rules: [
>       {
>         test: /\.ts$/,
>         exclude: /node_modules/,
>         loader: 'ts-loader'
>       }
>     ]
>   }
> }
> ```

