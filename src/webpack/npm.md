## npm包基础

### package.json

> 作用

我们在项目中管理项目依赖包时，通过会创建一个package.json文件，这个有很多作用，如：

* 指定包/项目名、版本号
* 指定程序入口点
* 指定包npm脚本信息
* 为项目所依赖的包提供文档
* 根据npm的语义规则为依赖包指定所需的版本

> 属性

package.json是一个json格式的文件。在一个package.json，至少应该包含以下属性：

* name:包/项目名,必填属性
* version:版本号,必填属性
* main: 定义了 npm 包的入口文件，browser 环境和 node 环境均可使用
* module: 定义 npm 包的 ESM 规范的入口文件，browser 环境和 node 环境均可使用
* browser:指定该模块供浏览器使用的入口文件。如果字段未定义，则回退到 main 字段指向的文件
* bin:在开发脚手架时候会用到bin字段, 包的命令
* files:依赖包里包含的文件名数组
* unpkg:让 npm 上所有的文件都开启 cdn 服务。
* typings|types:ts的声明文件入口
* dependencies:表示生产环境下的依赖管理，–save 简写 -S
* devDependencies:表示开发环境下的依赖管理，–save-dev 简写 -D
* peerDependencies:npm 1 和 2 版本会自动安装 peerDependencies。从 npm@3 开始将不再自动安装
* resolutions:覆盖特定嵌套依赖项的版本

示例，下面是一个最简单化的package.json文件：

```js
{
  "name": "antd",
  "version": "4.20.7",
  "title": "Ant Design",
  "files": [
    "dist",
    "lib",
    "es"
  ],
  "main": "lib/index.js",
  "module": "es/index.js",
  "unpkg": "dist/antd.min.js",
  "typings": "lib/index.d.ts",
  "scripts": {},
  "dependencies": {},
  "devDependencies": {}
}

```


> 如何创建

创建一个package.json文件，可以使用npm init命令：

```
npm init
```

## npm包开发

> 工具类包

业务开发时都会遇到重复的功能，或者开发相同的工具函数，每次遇到时都要去其他项目中拷贝代码

我们可以整合各个项目的需求，开发一个适合自己项目的工具类的npm包，包的结构如下

```js
hello-npm
|-- lib/（存放打包后的文件）
|-- src/（源码）
|-- package.json
|-- rollup.config.base.js（rollup基础配置）
|-- rollup.config.dev.js（rollup开发配置）
|-- rollup.config.js（rollup正式配置）
|-- README.md
|-- tsconfig.json
```

首先看下package.json的配置，rollup根据开发环境区分不同的配置

```js
{
  "name": "hello-npm",
  "version": "1.0.0",
  "description": "我是npm包的描述",
  "main": "lib/bundle.cjs.js",
  "jsnext:main": "lib/bundle.esm.js",
  "module": "lib/bundle.esm.js",
  "browser": "lib/bundle.browser.js",
  "types": "types/index.d.ts",
  "author": "",
  "scripts": {
    "dev": "npx rollup -wc rollup.config.dev.js",
    "build": "npx rollup -c rollup.config.js && npm run build:types",
    "build:types": "npx tsc",
  },
  "license": "ISC"
}
```

然后配置rollup的base config文件：

```js
import typescript from "@rollup/plugin-typescript";
import pkg from "./package.json";
import json from "rollup-plugin-json";
import resolve from "rollup-plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";
import eslint from "@rollup/plugin-eslint";
import { babel } from '@rollup/plugin-babel'
const formatName = "hello";
export default {
  input: "./src/index.ts",
  output: [
    {
      file: pkg.main,
      format: "cjs",
    },
    {
      file: pkg.module,
      format: "esm",
    },
    {
      file: pkg.browser,
      format: "umd",
      name: formatName,
    },
  ],
  plugins: [
    json(),
    commonjs({
      include: /node_modules/,
    }),
    resolve({
      preferBuiltins: true,
      jsnext: true,
      main: true,
      brower: true,
    }),
    typescript(),
    eslint(),
    babel({ exclude: "node_modules/**" }),
  ],
};
```

这里我们将打包成commonjs、esm和umd三种模块规范的包，然后是生产环境的配置，加入terser和filesize分别进行压缩和查看打包大小：

```js
import { terser } from "rollup-plugin-terser";
import filesize from "rollup-plugin-filesize";

import baseConfig from "./rollup.config.base";

export default {
  ...baseConfig,
  plugins: [...baseConfig.plugins, terser(), filesize()],
};
```

然后是开发环境的配置：

```
import baseConfig from "./rollup.config.base";
import serve from "rollup-plugin-serve";
import livereload from "rollup-plugin-livereload";

export default {
  ...baseConfig,
  plugins: [
    ...baseConfig.plugins,
    serve({
      contentBase: "",
      port: 8020,
    }),
    livereload("src"),
  ],
};
```

环境配置好后，我们就可以打包了

```
# 测试环境
npm run dev
# 生产环境
npm run build
```

> 全局包

还有一类npm包比较特殊，是通过npm i -g [pkg]进行全局安装的，比如常用的vue create、static-server、pm2等命令，都是通过全局命令安装的；那么全局npm包如何开发呢？

我们来实现一个全局命令的计算器功能，新建一个项目然后运行：

```
cd my-calc
npm init -y
```
在package.json中添加bin属性，它是一个对象，键名是告诉node在全局定义一个全局的命令，值则是执行命令的脚本文件路径，可以同时定义多个命令，这里我们定义一个calc命令

```
{
  "name": "my-calc",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "bin": {
    "calc": "./src/calc.js",
  },
  "license": "ISC",
}
```

命令定义好了，我们来实现calc.js中的内容：

```js
#!/usr/bin/env node

if (process.argv.length <= 2) {
  console.log("请输入运算的数字");
  return;
}

let total = process.argv
  .slice(2)
  .map((el) => {
    let parseEl = parseFloat(el);
    return !isNaN(parseEl) ? parseEl : 0;
  })
  .reduce((total, num) => {
    total += num;
    return total;
  }, 0);

console.log(`运算结果：${total}`);
```

需要注意的是，文件头部的#!/usr/bin/env node是必须的，告诉node这是一个可执行的js文件，如果不写会报错；然后通过process.argv.slice(2)来获取执行命令的参数，前两个参数分别是node的运行路径和可执行脚本的运行路径，第三个参数开始才是命令行的参数，因此我们在命令行运行来看结果：

```
calc 1 2 3 -4
```

> 组件库

React组件，可以看这里https://github.com/yotcap/question-renderer

在Vue项目中，我们在很多项目中也会用到公共组件，可以将这些组件提取到组件库，我们可以仿照element-ui来实现一个我们自己的ui组件库；首先来构建我们的项目目录：

```js
|- lib
|- src
    |- MyButton
        |- index.js
        |- index.vue
        |- index.scss
    |- MyInput
        |- index.js
        |- index.vue
        |- index.scss
    |- main.js
|- rollup.config.js
```

　我们构建MyButton和MyInput两个组件，vue文件和scss不再赘述，我们来看下导出组件的index.js：

```js
import MyButton from "./index.vue";

MyButton.install = function (Vue) {
  Vue.component(MyButton.name, MyButton);
};
export default MyButton;
```

组件导出后在main.js中统一组件注册：

```js
import MyButton from "./MyButton/index.js";
import MyInput from "./MyInput/index";
import { version } from "../package.json";

import "./MyButton/index.scss";
import "./MyInput/index.scss";

const components = [MyButton, MyInput];

const install = function (Vue) {
  components.forEach((component) => {
    Vue.component(component.name, component);
  });
};
if (typeof window !== "undefined" && window.Vue) {
  install(window.Vue);
}
export { MyButton, MyInput, install };
export default { version, install };
```

然后配置rollup.config.js：

```js
import resolve from "rollup-plugin-node-resolve";
import vue from "rollup-plugin-vue";
import babel from "@rollup/plugin-babel";
import commonjs from "@rollup/plugin-commonjs";
import scss from "rollup-plugin-scss";
import json from "@rollup/plugin-json";

const formatName = "MyUI";
const config = {
  input: "./src/main.js",
  output: [
    {
      file: "./lib/bundle.cjs.js",
      format: "cjs",
      name: formatName,
      exports: "auto",
    },
    {
      file: "./lib/bundle.js",
      format: "iife",
      name: formatName,
      exports: "auto",
    },
  ],
  plugins: [
    json(),
    resolve(),
    vue({
      css: true,
      compileTemplate: true,
    }),
    babel({
      exclude: "**/node_modules/**",
    }),
    commonjs(),
    scss(),
  ],
};
export default config;
```

这里我们打包出commonjs和iife两个模块规范，一个可以配合打包工具使用，另一个可以直接在浏览器中script引入。我们通过rollup-plugin-vue插件来解析vue文件，需要注意的是5.x版本解析vue2，最新的6.x版本解析vue3，默认安装6.x版本；如果我们使用的是vue2，则需要切换老版本的插件，还需要安装以下vue的编译器：

```js
npm install --save-dev vue-template-compiler
```

打包成功后我们就能看到lib目录下的文件了，我们就能像element-ui一样，愉快的使用自己的ui组件了，在项目中引入我们的UI：

```js
/* 全局引入 main.js */
import MyUI from "my-ui";
// 引入样式
import "my-ui/lib/bundle.cjs.css";

Vue.use(MyUI);


/* 在组件中按需引入 */
import { MyButton } from "my-ui";
export default {
  components: {
    MyButton
  }
}
```


## npm发布

> 注册一个npm账号

前往[NPM](https://www.npmjs.com/)官网或者[verdaccio](https://verdaccio.org/zh-cn/blog/)私库进行注册

> 登录

* 初始化:npm adduser 
* 非初始化:npm login
* 查看用户:npm whoami

> 发布

* npm publish
* npm unpublish 

## npm调试

* [npm link](https://www.imyangyong.com/blog/2020/06/node/npm%20%E5%8C%85%E8%B0%83%E8%AF%95%E6%8A%80%E5%B7%A7%20-%20npm%20link/)
* [yalc](https://segmentfault.com/a/1190000039658156)

## 参考链接

* [开发一个规范的 npm 包](https://juejin.cn/post/6945376222863949831)
* [从零开始发布自己的NPM包](https://xieyufei.com/2021/12/28/Npm-Package.html#%E5%B7%A5%E5%85%B7%E7%B1%BB%E5%8C%85)