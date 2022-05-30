# [官方文档](https://www.tslang.cn/docs/handbook/interfaces.html)
## 1.数据类型
---
### 基本数据类型:
  * number
  * string
  * boolean
  * undefined
  * null

### 引用数据类型:

  * string[]  number[]  Array\<string>  Array\<number>
  * Object
  * Function

### TS特有类型:
  #### void
  void 表示无任何类型，正好与 any 相反，没有类型，如果是函数则应没有返回值或者返回 undefined

  ```
  function voidFn(): void {}
  function voidFn1(): void { return undefined; }
  function voidFn2(): void { return; }

  function voidFn3(): void { return 1; } // Error
  ```

  #### any 
  any表示任意类型，这也是 ts 中不写类型申明时的默认类型，即不作任何约束，编译时会跳过对其的类型检查
  ```
  let val1: any;
  val1 = 'abc';
  val1 = 123;
  val1 = true;

  const arry: any[] = [123, 'abc', true, null];
  ```

  #### never
  never表示永不存在的值的类型,其实确实有一些情形，值会永不存在，比如，从程序运行的维度讲，如果一个函数执行时抛出了异常，那么这个函数变永远不会有值了
  ```
  function err(msg: string): never {
    throw new Error(msg);
  }

  // 有机会到达终点的函数也算存在返回值，编译会报错
  function err1(): never { // Error
    if (Math.random() > 0.5) {
      throw new Error('message');
    }
  }
  ```
  另外never 是所有类型的子类型，意思就是它可以赋值给任何类型，同时也没有任何类型是 never 的子类型，除了 never 自身，即除了 never 任何类型都不能赋值给 never 类型的变量
  ```
  let num: number = 123;
  let ne: never;

  num = ne;

  let ne1: never;
  let ne2: never;

  ne1 = ne2;

  // any 也不能分配给 never
  let any1: any = 123;
  ne1 = any1; // Error
  ```
  #### unknown
  unknown 表示未知类型，即写代码的时候还不清楚会得到怎样的数据类型，如服务器接口返回的数据，JSON.parse() 返回的结果等；该类型相当于 any，可以理解为官网指定的替代 any 类型的安全版本（因为不提倡直接使用 any 类型）；
  它能被赋值为任何类型，但不能被赋值给除了 any 和 unknown 之外的其他类型，同时，不允许执行 unknown 类型变量的方法（any 可以）
  ```
  let uk: unknown = 'abc';
  uk = 123;
  uk = true;
  uk.toString(); // Error

  let valAny: any = 'abc';
  valAny.toString(); // 'abc'

  let uk1: unknown = uk;
  let uk2: any = uk;
  let uk2: string = uk; // Error
  ```
  

  #### 类型推断
  如果没有明确的指定类型，那么 TypeScript 会依照类型推论（Type Inference）的规则推断出一个类型
  ```
  let myFavoriteNumber = 'seven';
  myFavoriteNumber = 7;

  // index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
  ```
  事实上，它等价于：
  ```
  let myFavoriteNumber: string = 'seven';
  myFavoriteNumber = 7;

  // index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
  ```

  如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成 any 类型而完全不被类型检查：
  ```
  let myFavoriteNumber;
  myFavoriteNumber = 'seven';
  myFavoriteNumber = 7;
  ```
  #### 类型断言
  类型断言可以用来手动指定一个值的类型
  ```
  值 as 类型  //tsx语法
  <类型>值    //tsx无效

  const foo = {};
  foo.bar = 123; // Error: 'bar' 属性不存在于 ‘{}’
  foo.bas = 'hello'; // Error: 'bas' 属性不存在于 '{}'

  interface Foo {
  bar: number;
  bas: string;
  }

  const foo = {} as Foo;
  foo.bar = 123;
  foo.bas = 'hello';

  ```


# 2.高级类型

## 2.1接口

```typescript
interface IPerson{
  name:string;
  age?:number;
}
```
## 2.2 函数

```typescript
let myAdd: (x: number, y: number) => number =
  function(x: number, y: number): number { return x + y;};
```
## 2.3 交叉类型

```typescript
T & U

interface Button {
  type: string
  text: string
}
interface Link {
  alt: string
  href: string
}
const linkBtn: Button & Link = {
  type: 'danger',
  text: '跳转到百度',
  alt: '跳转到百度',
  href: 'http://www.baidu.com'
}
```
## 2.4 联合类型

```typescript
T|U

function getName(n: string | number): Name {
    if (typeof n === 'string') {
        return n.slice(0,1);
    } else {
        return n.toString();
    }
}
```

## 2.5 类型别名

```typescript
type Alias = T | U

interface Common {
  name: string;
}
type People<T> = {
  age: T;
  sex: string;
} & Common;

type P1 = Person<number> 

```

## 2.6 type和interface的区别
![区别](../../asset/js/type%26interface.png)

### 相同点：

* 都可以描述对象或函数
```
// 接口 
interface Sister { 
  name: string; 
  age: number; 
} 
 
interface SetSister { 
  (name: string, age: number): void; 
} 
 
// 类型别名 
type Sister = { 
  name: string; 
  age: number; 
}; 
 
type SetSister = (name: string, age: number) => void; 
```
* 都可以扩展
```
// 接口 
interface SisterAn { 
    name: string; 
} 
 
// 类型别名 
type SisterRan = { 
    age: number; 
} 
// 接口扩展接口 
interface Sister extends SisterAn { 
    age: number; 
}
// 类型别名扩展类型别名 
type SisterPro = SisterRan & { 
    name: string; 
} 
```

### 区别
* 不同的声明范围
与接口不同，可以为任意的类型创建类型别名

类型别名的右边可以是任何类型，包括基本类型、元祖、类型表达式( & 或 | 等);而在接口声明中，右边必须为变量结构。例如，下面的类型别名就不能转换成接口

```
type Name = string 
type Text = string | { text: string }; 
type Coordinates = [number, number]; 
```
* 不同的扩展形式
接口是通过继承的方式来扩展，类型别名是通过 & 来扩展
```
// 接口扩展 
interface SisterAn { 
    name: string; 
} 
interface Sister extends SisterAn { 
    age: number; 
} 
 
// 类型别名扩展 
type SisterRan = { 
    age: number; 
} 
type SisterPro = SisterRan & { 
    name: string; 
} 
```
* 不同的重复定义表现形式
接口可以定义多次，多次的声明会自动合并
```
interface Sister { 
    name: string; 
} 
interface Sister { 
    age: number; 
} 
 
const sisterAn: Sister = { 
    name: 'sisterAn' 
}  
// 报错：Property 'age' is missing in type '{ name: string; }' but required in type 'Sister' 
 
const sisterRan: Sister = { 
    name: 'sisterRan',  
    age: 12 
} 
// 正确 
```
但是类型别名如果定义多次，会报错
```
type Sister = { // Duplicate identifier 'Sister' 
    name: string; 
} 
 
type Sister = { // Duplicate identifier 'Sister' 
    age: number; 
} 
```

# 3.复杂类型

## 3.1 泛型<T>

```typescript
function identity(arg: number): number {
    return arg;
}
function identity(arg: any): any {
    return arg;
}
function identity<T>(arg: T): T {
    return arg;
}
```
## 3.2 类型索引（keyof）

```typescript
interface Button {
    type: string
    text: string
}
type ButtonKeys = keyof Button
// 等效于
type ButtonKeys = "type" | "text"
```

## 3.3 类型约束（extends）

```typescript
type BaseType = string | number | boolean
// 这里表示 copy 的参数
// 只能是字符串、数字、布尔这几种基础类型
function copy<T extends BaseType>(arg: T): T {
  return arg
}
```

## 3.4 条件类型（U ? X : Y）

```typescript
type Extract<T, U> = T extends U ? T : never;

interface Worker {
  name: string
  age: number
  email: string
  salary: number
}
interface Student {
  name: string
  age: number
  email: string
  grade: number
}

type CommonKeys = Extract<keyof Worker, keyof Student>

CommonKeys 

```


## 3.4 类型遍历（in） 

```typescript
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
interface Obj {
  a: string
  b: string
}
type ReadOnlyObj = Readonly<Obj>
```

## 3.5 类型映射(infer)

```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;

type Func<T> = T extends () => infer R ? R : boolean; 
let func1: Func<number>; // => boolean let func2: Func<''>; // => boolean let func3: Func<() => Promise<number>>; // => Promise<number>
```
## 3.6 数据映射(typeof)

```typescript
const options = {
  a: 1
}
type Options = typeof options
```
# 4.工具泛型

## 4.1 Partial

```typescript
type Partial<T> = {
    [P in keyof T]?: T[P]
}
```
## 4.2 Required

```typescript
type Required<T> = {
    [P in keyof T]-?: T[P]
}
```
## 4.3 Record

```plain
type Record<K extends keyof any, T> = {
    [P in K]: T
}

type petsGroup = 'dog' | 'cat' | 'fish';
interface IPetInfo {
    name:string,
    age:number,
}

type IPets = Record<petsGroup, IPetInfo>;

const animalsInfo:IPets = {
    dog:{
        name:'dogName',
        age:2
    },
    cat:{
        name:'catName',
        age:3
    },
    fish:{
        name:'fishName',
        age:5
    }
}
```
## 4.4 Pick

```typescript
type Pick<T, K extends keyof T> = {
    [P in K]: T[P]
}
```
## 4.5 Exclude

```plain
type Exclude<T, U> = T extends U ? never : T
```
## 4.6 Omit

```plain
type Omit<T, K extends keyof any> = Pick<
  T, Exclude<keyof T, K>
>
```

# 5.其他

## 5.1 声明

### 5.1.1 声明文件和普通文件

`*.d.ts`和`*.ts`的区别在于：

 

* `*.d.ts`对于typscript而言，是类型声明文件，且在*.d.ts文件中的顶级声明必须以`declare`或`export`修饰符开头。同时在项目编译过后，`*.d.ts`文件是不会生成任何代码的。
* 而`*.ts`则没有那么多限制，任何在***.d.ts**中的内容，均可以在***.ts**中使用。

### 5.1.2 声明变量

用来全局声明变量、常量、类、全局对象等等

```typescript
interface Process {
  exit(code?: number): void;
}
declare let process: Process;
```

### 5.1.3声明模块

```typescript
//一般通过一下方式或者npm包中已经自带模块申明
yarn add @types/{模块名}

//如若没有模块申明，需要在申明文件中声明
declare module '*.png';
declare module '*.jpg';
declare module '*.json';
declare module '*.bmp';
declare module '*.jpeg';
declare module '*.gif'; 
declare module "rc-form" {
   // 在此只是简单地进行类型描述 
   exportconst createForm: any; 
   exportconst createFormField: any;
   exportconst formShape: any; 
}
```

 

## [5.2 配置](https://www.tslang.cn/docs/handbook/tsconfig-json.html)

目录下存在一个tsconfig.json文件，那么它意味着这个目录是TypeScript项目的根目录。 tsconfig.json文件中指定了用来编译这个项目的根文件和编译选项 

