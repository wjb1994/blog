## 1.数据类型

* number
* string
* boolean
* undefined
* null
* string[]  number[]  Array<string> Array<number>
* Object
* Function
* any
* void 
* never
* 类型推断
* 类型断言

# 2.高级类型

## 2.1 接口 

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

