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

- 安装TypeScript

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

- TS配置文件

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
          "strictNullChecks": true // 严格的空类型检查 
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

### 泛型

- 是指附属于函数、类、接口、类型别名之上的类型
- 泛型相当于是一个类型变量，在定义时，无法知道具体的类型，可以用该变量来代替，只有到调用时才能确定类型
- 很多时候，ts会只能的根据参数推断出泛型的具体类型
- 泛型可以赋值默认值

#### 在函数中使用泛型

- 在函数名之后`<类型>`

```ts
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

