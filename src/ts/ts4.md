## TypeScript 编译

* 运行 tsc，它会在当前目录或者是父级目录寻找 tsconfig.json 文件 
* tsc -w 来启用 TypeScript 编译器的观测模式，在检测到文件改动之后，它将重新编译
* tsc -p ./项目地址  用来编译指定的项目
* tsc 指定文件   编译该文件，但忽略tsconfig.json的配置,目录下的tsconfig无效

## 声明文件的识别规则

 1. 在 tsconfig.json include 字段包含的范围内编写 .d.ts 会被自动识别
 2. node_modules/@types/下存放各个第三方模块的声明文件，通过yarn add @types/react自动下载到此处，自己编写的声明文件不要放在这里


## 声明文件的使用

1. 在项目根目录新建 types 文件夹。
2. 在 tsconfig.json 里的 include 添加上 types
3. 在 typings 文件夹里新建类型声明文件，格式为 XXX.d.ts 
```ts
{
  "compilerOptions": {
    "module": "commonjs",
    "noImplicitAny": true,
    "removeComments": true,
    "preserveConstEnums": true,
    "sourceMap": true,
    "lib": ["esnext", "dom"],
    "outDir": "./dist"
  },
  "files": ["index.ts"],
  "include": ["./src/typing"]
}

```
4. xxx.d.ts文件内容

```ts
declare module 'XXX' {
  const content: any
  /// 这里的 content 可以根据自己的需要，添加需要的类型，这的话可以让 ts 更好的提示
  /**
  type content = {
    test: string
  }
 */
  export = content
}

```

## 常用路径写法参考

1. `./src/**/*`
2. `./src/**/*.ts`
