# TypeScript

### JS开发中的问题

- 使用了不存在的变量、函数或成员（对象、数组）
- 把一个不确定的类型当做一个确定类型处理
- 在使用null或者undefined的成员

> js语言本身的特性，决定该语言无法适应大型的复杂的项目
>
> 弱类型：某个变量可以随时更换类型
>
> 解释性： 错误发生时间，是在运行时

### TS语言的特点

> TypeScript是JS的超级，是一个可选的静态的类型系统

#### 类型系统

- 对代码中所有的标识符（变量、函数、参数、返回值）进行类型检查

#### 可选的 

#### 静态的

- 无论是浏览器环境，还是node环境，都无法直接识别TS代码
- tsc -> js
- 静态： 类型检查发生的时间，在编译的时候，而非运行时
- TS不参与任何运行时的类型检查

### node中搭建TS开发环境

#### 安装TypeScript

```shell
npm install -g typescript
```

>默认情况下，TS会做出下面几种假设：
>
>1. 假设当前的执行环境是dom
>2. 如果代码中没有使用模块化语句（import、export）,便认为该代码是全局执行
>3. 编译目标代码是ES3
>
>有两种方式更改以上假设：
>
>1. 使用tsc命令行加上选项参数
>2. 使用配置文件（tsconfig.json）,更改编译选选项

#### TS配置文件

```bash
tsc --init # 生成配置文件tsconfig.json

# 添加node执行环境
npm install @types/node --save
```

```json
{
    "compilerOptions": { // 编译选项
        "target": "es2016", // 配置编译目标代码的标准
        "module": "commonjs", // 配置编译目标使用的模块化标准
        "lib": ["es2016", "dom"], // 执行环境（库）
        "outDir": "./dist", // 编译结果的目录
        "strictNullChecks": true, // 严格的空类型检查 
      	"removeComments": true, // 移除注释
      	"esModuleInterop": true, // 启用ES模块化交互非模块导出
    },
    "include": ["./src"], // 需要编译的文件目录
    // "files": ["./src/index.ts"] // 需要编译的文件
    
}
```

> 使用了配置文件后，使用tsc进行编译时，不能跟上文件名，跟上文件名忽略配置文件。

#### @types

> @types是一个TS官方的类型库，其中包含对js代码的类型描述

### 使用第三方库简化流程

- ts-node：将TS代码在内存中完成编译，同时完成运行

  ```bash
  npm install -g ts-node
  
  # ts-node src/index.ts
  ```

- nodemon：用于监测文件的变化

  ```bash
  npm install -g nodemon
  # --exec 执行
  # nodemon --exec ts-node src/index.ts
  # -e 扩展名
  # nodemon -e ts --exec ts-node src/index.ts
  # --watch 监控目录
  # nodemon --watch src -e ts --exec ts-node src/index.ts
  ```

### 基本的类型约束

- ts是一个可选的、静态的类型系统

#### 如何进行类型约束

- 仅需要变量、函数参数、函数返回值位置加上`:类型`

  ```ts
  let name: string;
  name = 'll'
  
  function sun(a: number, b:string):number {
      return a + b
  }
  const num:number = sum(1, 2)
  ```

  > ts在很多场景中可以完成类型推断
  >
  > any：表示任何类型，ts不对其进行检查

#### 原代码和编译结果的差异

- 编译结果中没有类型约束信息

### 基本类型

- number：数字
- string：字符串
- boolean：布尔值
- 数组：string[] 或者 Array<string>
- object: 对象（不精准约束）
- null和undefined：
  - 是任何其他类型的的子类型，可以赋值给其他类型
  - 设置`strictNullChecks:true`,null和undefined只能赋值给自身

### 其他常用类型

#### 联合类型

- 多种类型任选其一
- 配合类型保护： 当对某个变量进行类型判断后，在该语句块内使用判断后的类型

```ts
let name: string | undefined;
if (typeof name === 'string') {
	// 类型保护
}
```

> 联合类型只能使用共有的属性，一般配合类型保护

#### viod类型

- 通常用于约束函数的返回值，表示该函数没有任何返回值

```ts
function print (): void {
    console.log('con')
}
```

#### never类型

- 通常用于约束函数的返回值，表示该函数永远不可能结束

```ts
function throwError (msg: string): never {
    throw new Error(msg)
    // ...
    console.log('a')
}
function alwaydo (): never {
    while(ture) {
        // ...
    }
}
```

#### 字面量类型

- 使用一个值进行约束

```ts
let gender: 'male' | 'female';
let user: {
    name: string
    age: number
}
```

#### 元祖类型（Tuple）

- 一个固定长度的数组，并且数组中每一项的类型确定。

```ts
let tu: [number, string] 
```

#### any类型

- any类型可以绕过类型检查
- any类型的变量可以赋值给任何类型

```ts
let data: any = 'dd'

let num: number = any
```

### 类型别名

- 对已知的一些类型定义名称：` type 名称 = 已知类型`

```ts
type GenderType = 'male' | 'female'
type UserType = {
    name: string
    age: number
    gender: GenderType
}
let u: User

u = {
    name: 'll',
    age: 22,
    gender: 'male'
}
```

### 函数的相关约束

- 函数重载：在函数实现之前，对函数调用的多种情况进行声明
- 可选参数：可以在某些参数名后加上问号，表示该参数可以不传递
  - 可以选参数后面不能有必选参数
  - 默认参数一定是可选参数

```ts
funtion combine (a: number, b: number): number
funtion combine (a: string, b: string): string
funtion combine(a: number | string, b: number | string): number |string {
    if (typeof a === 'number' && typeof b === 'number') {
        return a * b
    }
    else if (typeof a === 'string' && typeof b === 'string') {
        return a + b
    }
    throw new Error('error')
}
```

### 扩展类型

#### 枚举

- 通常用于约束某个变量的取值范围
  - 字面量和联合类型配合使用，也可以达到同样的目标

> 字面量的问题
>
> 1. 在类型约束位置，会产生重复代码。（可以使用类型别名解决该问题）
>
> 2. 逻辑值和真实值产生混淆，会导致修改真实值，产生大量修改
> 3. 字面量类型不会进编译结果

- 定义一个枚举：

  ```
  enum 枚举名 {
  	枚举字段1 = 值1,
  	枚举字段2 = 值2,
  }
  ```

  ```ts
  enum Gender {
      male = "男",
      female = "女"
  }
  let gender: Gender = Gender.male
  
  function print () {
      Object.values(Gender).forEach(val => consoel.log(val))
  }
  ```

> 枚举会出现子编译结果中，编译结果中表现为对象

#### 枚举的规则

- 枚举的字段值可以是字符串或数字
- 数字枚举的值会自动自增
- 被数字枚举约束的变量，可以直接赋值为数字
- 数字枚举的编译结果和字符串枚举有差异

##### 最佳实践

- 不要再一个枚举即出现字符串字段，又出现数字字段
- 使用枚举，尽量使用枚举字段的名称，不要使用枚举值

##### 位枚举

- 针对数据枚举
- 位运算： 两个数字换算成二进制后进行运算

```ts
enum Permussion {
    Read = 1,   // 0001
    Write = 2,	// 0010
    Create = 4, // 0100
    Delete = 8  // 1000
}
// 组合权限
// 位运算: 或运算
// 0001 或 0010 > 0011
let p:Permussion = Permussion.Read | Permussion.Write

// 判断权限
// 0011 且 0001 > 0001
function hasPermussion (target: Permussion, per:Permussion ) {
    return (target & per) === per
}
// 判断变量p是拥有可读权限
hasPermussion(p, Permussion.Read)

// 删除权限
// 异或： 相同取0 不同取1
// 0011 ^ 0001 0010
p = p ^ Permussion.Read
```

### TS中的模块化

#### TS中如何书写模块化语句

TS中，导入和导出模块，统一使用ES6的模块化标准。

> 使用导入时不要加扩展名，如 ： import { xx } from './xx.ts'
>
> 因为ts需要编译成js,会报错。

#### 编译结果中的模块化标准

可配置

- 如果编译结果的模块化是ES6，则没有区别

- 如果编译结果的模块化标准是commonjs:
  - 导出的声明会变成exports的属性
  - 默认的导出会变成exports的default属性

#### 解决默认导入的错误

- Tsconfig.json

```json
{
  "compilerOptions": {
    "esModuleInterop": true // 启用ES模块化交互非模块导出
  }
}
```



> 使用module.exports = {},使用import导入没有没有默认导入.
>
> 可以使用{}按需导入
>
> 或者import * as xx from 'xx'

#### 在TS中写commonjs

- 导出： export = {}
- 导入： import xx = require('./xx.ts')

```ts
// a.ts
export = {
  name: 'LL',
  say (str: string) {
		console.log(str)
  }
}
// b.js

import a = require('a.js')
```

#### 模块解析

从什么位置寻找模块，

TS中有两种模块解析策略。

- Classic：经典（过时）
- node解析策略（唯一变化，将js变为ts）

### 接口和类型兼容性

#### TS中的接口

- 用于约束类、对象、函数的契约（标准）
- 接口不出现在编译结果中

##### 接口约束对象

```ts
interface User {
  name: string
  age: number
  // sayHello: () => void
  sayHi(): void
}
const u: User = {
  name: 'LL',
  age: 22
}
```

##### 接口约束函数

```ts
// type Condition = (n: number) => boolean
//type Condition = {
//  (n: number): boolean
//}
interface Condition {
  (n: number): boolean
}
function sum (numbers: number[], callback: Condition) {
	let res = 0
  numbers.forEach(item => {
		if (callback(item)) {
			res += item
    }
  })
  return res
}
const list = [1, 2, 3, 4, 5, 6]
const r = sum(list, (n) => n > 3)
console.log(r)
```

##### 接口可以继承

- 子接口不能覆盖父接口的成员
- 交叉类型会把相同成员的类型进行交叉

```ts
interface A {
  a: string
}
interface B extends A {
  b: number
}

const o: B ={
	a: 'hello',
  b: 1
}
/**
* 使用类型可以实现类似接口继承效果
* 通过`&`:交叉类型
*/
type C = {
  c: string
}
type D = {
  d: number
} & C
```

##### readonly修饰符

- 修饰目标属性只读

- 有readonly修饰符的属性，只能在最开始的赋值一次。后不能修改。
- 不在编译结果中
- 在类型别名相同使用

```ts
interface User = {
	readonly id: string
  name: string
  age: number
}
const  u: User = {
  id: '1',
  name: 'LL',
  age: 22
}
// u.id = 2 // 不 可以修改

// 只读数组 写法
const arr: redaonly number[] = [1, 2, 3]
const arr2: ReadonlyArray<number> = [1, 2, 3]
```

#### 类型兼容性

B->A,如果能完成**赋值**，则B和A类型兼容。

鸭子辩型法（子结构辩型法）：目标类型（A）需要某一些特征，赋值的类型（B）只要能满足该特征即可

- 基本类型：完全匹配
- 对象类型：鸭子辩型法
- 函数类型：一切都很自然。参数：传递给目标函数的参数可以少传，不能多传（回调函数）

```ts
interface Duck {
  sound: 'gagaga'
  swim(): void
}

const p = {
  name: '伪装成鸭子的人',
  age: 22,
  sound: 'gagaga' as 'gagaga',
  swim () {
    console.log('游泳')
  }
}
const d: Duck = p
```

> 当直接使用对象字面量进行赋值时，会进行更加严格的判断。
>
> （理解：在可控状态下，不允许写错。非可控状态（来自第三方的值），可以适当宽松匹配。）

### TS中的类

- 属性： 使用属性列表来描述类中的属性
- 属性的初始化检查: tsconfig.json: 更加严格的属性初始化配置`"strictPropertyInitialization": true`
- 属性可以修饰为可选
- 属性可以修饰为只读
- 访问修饰符：可以控制类中的某个成员的访问权限
  - public：默认的访问修饰符，公开的所有代码都可以访问（类的内部和外部）
  - private：私有的，只有在类的内部可以访问
  - protected：受保护的..
- 属性简写：属性通过构造函数参数传递，并且直接赋值该属性`(public username: string)`
- 访问器（set, get）： 用于控制属性的读取和赋值

```ts
 /**
* 属性： 使用属性列表来描述类中的属性
* 属性的初始化检查: 在tsconfig.json: 更加严格的属性初始化配置"strictPropertyInitialization": true
*/

class User {
  readonly id: number
  pid?: string
  private publishNum = 3
  private currPublishNum = 0
	constructor(public username: string, private _age: number) {
    this.id = Math.random()
    this.username = username
    this.age = age 
	}
  set age (value: number) {
    if (value < 0) {
			this._age = 0
    } else if (value > 200) {
			this._age = 200
    } else {
      this._age = value
    }
  }
  get age () {
    return Math.floor(this.age)
  }
  publish() {
    if(currPublidhNum < publishNum) {
      console.log('发布一片文章')
      this.publishNum = this.publishNum + 1
    } else {
      console.log('发布达到上限')
    }
  }
}
```
### 泛型

- 是指附属于函数、类、接口、类型别名之上的类型
- 泛型相当于是一个类型变量，在定义时，无法知道具体的类型，可以用该变量来代替，只有到调用时才能确定类型
- 很多时候，ts会只能的根据参数推断出泛型的具体类型
- 泛型可以赋值默认值

#### 在函数中使用泛型

- 在函数名之后`<类型>`

```ts
// 取出数组的前几项
function take<T>(arr: T[], n: number): T[] {
    if (n>= arr.length) {
        return arr
    }
    const newArr: T[] = []
    for (let i =0; i < n; n++) {
        newArr.push(arr[i])
    }
    return newArr
}
take<number>([1, 22, 135], 2)
```

#### 在类型别名、接口、类中使用泛型

- 直接在名称之后`<类型>`

```ts
type CallbackType<T> = (item: T, i: number) => boolean

function filter<T>(arr:T[], callback:CallbackType<T>) {
  const newArr: T[] = []
  arr.forEach((item, i) => {
    if (callback(item, i)){
			newArr.push(item)
    }
  })
  return newArr
}

const list = [1, 2, 3, 4]
const res = filter(list, (item) => item > 3)
console.log(res) // [ 4 ]
```

```ts
// ...dd
class ArrayHelper {
  take<T>(arr: T[], n: nomber): T[] {
		if (n>= arr.length) {
        return arr
    }
    const newArr: T[] = []
    for (let i =0; i < n; n++) {
        newArr.push(arr[i])
    }
    return newArr
  }
}
```

#### 泛型约束

- 泛型约束用于限制泛型的取值

```ts
function nameToUpperCase<T extends {name: string}>(obj: T):T {
  obj.name = obj.name.split(' ').map(str => str[0].toUpperCase() + str.substring(1)).join(' ')
  return obj
}
```

#### 多泛型

```ts
function mixinArray<T, K> (arr1: T[], arr2:K[]): (T | K) [] {
  if (arr1.length !== arr2.length) {
    throw new Error('数组长度不一致')
  }
  const result: (T | K) [] = []
  for (let i = 0; i < arr1.length; i++) {
    result.push(arr1[i])
    result.push(arr2[i])
  }
  return  result
}
```

### 深入理解类和接口

#### 类的继承

如果A和B都是类，并且可以描述为A是B，则A和B形成继承关系：

- B是父类，A是子类
- B派生A，A继承自B
- B是A的基类，A是B的派生类

如果A继承自B，则A自动拥有B的所有成员。

##### 成员重写

- 子类中覆盖分类的成员
- 子类成员不能改变父类成员类型
- 无论是属性还是方法，子类都可以对父类的相应成员进行重写 ，但是要保证类型匹配。
- super关键字，可以子类的方法中调用父类成员

> 在继承关系中，this的指向是动态调用方法时，根据具体调用者确定this的指向。

##### 类型匹配

鸭子辩型法

- 子类的对象始终可以赋值给父类（子类的成员一定包括父类的成员）
- 判断一个对象具体子类型，可以使用instanceof

##### protected修饰符

受保护的成员，只能在自身和子类中访问

##### 单根性和传递性

- 单根性： 每个类最多有一个父类
- 传递性：如果A是B的父类，B是C的父类，那么可以认为A是C的父类

#### 抽象类

有时某个类只表示一个抽象概念，主要用于提取子类的共有成员，而不能直接创建它的对象。

该类可以作为抽象类。

- 在类前面加上`abstract`表示一个抽象类
- 抽象类不能被创建对象

```ts
abstract class Chess {
  
}
```

##### 抽象成员

父类中，可能知道某些成员是必须存在的，但不知道该成员的值或实现是什么，需要有一种强约束，

让继承该类的子类，必须要实现该成员。

抽象类中可以有抽象成员，这些成员必须在子类中实现

```ts
abstract class Chess {
  abstract readonly name: string
  abstract move: (xTarger: number, yTarget: number) => boolean
}
// 子类实现父类抽象成员的三中写法
class Horse extends Chess {
  readonly name: string = '马'
}
class Soldier extends Chess {
  get name () {
    return '兵'
  }
}
class Pao extends Chess {
  name: sring
  constructor () {
		super() // 调用父类的构造函数
		this.name = '炮'
  }
}
```

#### 静态成员

静态成员指，附属在类上的成员

使用static修饰的成员时是静态成员

实例成员：对象成员，属于某个类的实例对象

静态成员：非实例成员，属于某个类的成员

##### 静态方法中的this

实例方法中this，指向当前对象

静态方法中的this指向当前类

#### 设计模式

面对一些常见的功能场景，有一些固定的，经过长期实践的成熟方法，称之为设计模式。

##### 模版模式

有些方法，所有的子类实现流程完全一致，只是流程中的某个步骤的具体实现不一致，

可以将该方法提取到父类，再父类中完成整个流程的实现，遇到实现不一致的方法时，将该方法做成抽象方法。

##### 单例模式

某些类的对象，在系统中最多有一个，为了避免开发者造成随意创建多个对象，可以使用单例模式。

```ts
class Board {
  width: number = 200
  height: number = 200
  init () {

  }
  // static readonly createBoard = new Board()
  private static _board?: Board
  private static createBoard () {
    if (Board._board) {
      return Board._board
    } else {
      Board._board = new Board()
      return Board._board
    }
  }
}
```

#### 接口和类

- 强制某个类实现一个接口，对类的成员实现强约束力。

- 面向对象领域中的接口的语义：表达了某个类是否拥有某种能力。

- 某个类具有某种能力，就是类实现某个接口。
- 类型保护函数：通过调用该函数，触发TS的类型保护，该函数返回一个布尔值。
- 接口可以继承类，表示类所有的成员都在接口中

```ts

interface ITeaching = {
  teachingEn(): void
}
abstract class People {
  abstract career: string
  constructor(
  	public name: string,
    public age: number
  ) {}
}
// Teacher类实现ITeaching接口
class Teacher extends People implements ITeaching {
  career: string = '教师'
  teachingEn () {
    console.log('教英语')
  }
}
class Student extends People  {
  career: string = '学生'
}

// 类型保护函数
function hasITeaching(people: any): people is ITeaching {
  return people.teachingEn
}
const t = new Teacher('lisi',33)
const s = new Student('zhangsan',22)
const list:People[] = [t, s]
list.forEach(p => {
  if(hasITeaching(p)) {
    console.log(p.name)
    p.teachingEn()
  }
})
```

> 接口和类型别名对区别：接口（一定）可以被类实现。
>
> （类只能实现具有静态已知成员的对象类型或对象类型的交集）

#### 索引器

- 索引器的键可以是string，也可以是number
- 索引器的值的类型，是对象所有成员类型的联合类型，或者是any类型。（其他成员类型必须是索引器值类型的子类型）
- 在类中，索引器的位置必须在所有成员之前。

```ts
class User {
  [prop: string]:any
  constructor(
  	public name: string,
    public age: number
  ) {}
  sayHello () {}
}
```

> 在TS中默认情况下，不对索引器（成员表达式），做严格类型检查。
>
> 使用配置`noImplicitAny`为true，开启对隐式any的检查。
>
> 隐式any：TS根据实际情况推导出的any类型。
>
> 索引器的作用：
>
> 1. 在严格的检查下，可以为对象添加成员
> 2. 可以动态操作对象的成员
>
> 在js中，对象的所有成员名都是字符串，如果使用数字作为成员名，会自动转为字符串
>
> 在ts中，使用两种类型的索引器，两种索引器的值类型必须一致。

#### this指向约束

##### 在JS中this指向的几种情况

1. 大部分情况，取决于函数的调用方式：

- 如果直接调用函数（全局调用），this指向全局对象或者undefined（启用严格模式）。
- 如果使用`对象.函数`的形式调用，this指向对象本身。
- 如果是dom事件的处理函数，this指向事件处理对象。

2. 箭头函数：this在函数声明时确定指向，指向函数所在位置的this。
3. 使用bind apply call等函数手动绑定this对象指向。

##### TS中的this

- tsconfig.json编译选项中`noImplicitThis`值为true，表示不允许this隐式指向any（不允许不明确this指向）
- 在TS中，允许在书写函数时，手动声明该函数的this指向，将this作为函数的第一个参数，该参数只作为约束this，并不是真正的参数，也不会出现在编译结果中。

```ts
const user = {
  name: 'll',
  age: 22,
  sayHello () {
		console.log(this)
  }
}
const s = user.sayHello
s() // 全局对象

// class一定严格模式
class User {
  constructor(
  	public name: string,
    public age: number
  ) {}
  sayHello () {
    console.log(this, this.name)
  }
}
const u = new User('ll', 22)
const say = u.sayHello
say() // undefined undefined
```

```ts
interFace IUser {
  name: string
  age: number
  sayHello(this: IUser): void // this不是参数
}
const u: IUser = {
  name: 'll',
  age: 22,
  sayHello() {
    console.log(this)
	}
}

const say = u.sayHello
say() // 报错：say上下文中this，不能分配给IUser
```

