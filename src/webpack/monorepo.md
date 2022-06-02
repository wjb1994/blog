## 什么是monorepo

Monorepo 的意思是在版本控制系统的单个代码库里包含了许多项目的代码

大致的文件目录结构如下：
```
├── packages                                            
│   ├── react             
│   │   └──src               
│   │   └──package.json                            
│   ├── react-dom                                  
│   │   └── src          
│   │   └── package.json      
│   ├──......    
├── package.json 
```

## 为什么要monorepo

假如你是一个npm工具的维护者，管理着多个功能相近的包，或者这些包之间存在依赖关系。如果将这些包拆分在不同仓库里，那么面临要跨多个包进行更改时，工作会非常繁琐和复杂。

为了简化流程，很多大型项目采用了menorepo的做法，即把所有的包放在一个仓库中管理。Babel、React、Vue、Jest等都使用了menorepo的管理方式。

Menorepo的优点是可以在一个仓库里维护多个package，可统一构建，跨package调试、依赖管理、版本发布都十分方便，搭配工具还能统一生成CHANGELOG；

缺点是代码仓库体积会变大，只开发其中一个package也需要安装整个项目的依赖。

## yarn workspace 

Yarn Workspaces（工作区）是Yarn提供的monorepo的依赖管理机制，从Yarn 1.0开始默认支持，用于在代码仓库的根目录下管理多个package的依赖。

很多知名的开源项目也使用了 Yarn Workspace，如 vue、react、jest 等。

* 开发多个互相依赖的package时，workspace会自动对package的引用设置软链接（symlink），比

* yarn link更加方便，且链接仅局限在当前workspace中，不会对整个系统造成影响

* 所有package的依赖会安装在最根目录的node_modules下，节省磁盘空间，且给了yarn更大的依赖优化空间
所有package使用同一个yarn.lock，更少造成冲突且易于审查

## 如何使用workspace

根目录的package.json设置
```json
{
  {
    "name": "root",
    "version": "1.0.0",
    "private": true,
    "workspaces": [
      "packages/*"
    ],
  }
}

```
> private：

根目录一般是项目的脚手架，无需发布，"private": true会确保根目录不被发布出去。

> workspaces:

声明workspace中package的路径。值是一个字符串数组，支持Glob通配符。

## 看个栗子

假设项目中有foo和bar两个package：

```js
projects/
|--project1/
|  |--package.json
|  |--node_modules/
|  |  |--a/
|--project2
|  |--package.json
|  |--node_modules/
|  |  |--a/
|  |  |--project1/
```

project1/package.json:

```js
{
    
  "name": "project1",
  "version": "1.0.0",
  "dependencies": {
    
    "a": "1.0.0"
  }
}
```

project2/package.json:

```js
{
    
  "name": "project2",
  "version": "1.0.0",
  "dependencies": {
    
    "a": "1.0.0",
    "project1": "1.0.0"
  }
}
```

没有使用 Yarn Workspace 前，需要分别在 project1 和 project2 目录下分别执行 yarn|npm install 来安装依赖包到各自的 node_modules 目录下。或者使用 yarn|npm upgrade 来升级依赖的包。

这会产生很多不良的问题：

1. 如果 project1 和 project2 有相同的依赖项目 a，a 都会各自下载一次，这不仅耗时降低开发效率，还额外占用重复的磁盘空间；当 project 项目比较多的时候，此类问题就会显得十分严重。

2. 如果 project2 依赖 project1，而 project1 并没有发布到 npm 仓库，只是一个本地项目，有两种方式配置依赖：

   * 使用相对路径（如 file: 协议）在 project2 中指定 project1 的依赖。
   * 使用 yarn|npm link 来配置依赖。

3. 没有一个统一的地方对全部项目进行统一构建等，需要到各个项目内执行 yarn|npm build 来构架项目。

使用 Yarn Workspace 之后，上述问题都能得到很好的解决。而且这是 Yarn 内置的功能，并不需要安装什么其他的包，只需要简单的在 projects 目录（Yarn 称之为 workspace-root）下增加如下内容的 package.json 文件即可。

projects/package.json：

```js
{
    
  "private": true,
  "workspaces": ["project1", "project2"] // 也可以使用通配符设置为 ["project*"]
  //"workspaces": ["packages/*"]
}
```

在 workspace-root 目录下执行 yarn install：

```js
$ cd projects
$ rm -r project1/node_modules
$ rm -r project2/node_modules

$ yarn install
yarn install v1.22.0
info No lockfile found.
[1/4]   Resolving packages...
[2/4]   Fetching packages...
[3/4]   Linking dependencies...
[4/4]   Building fresh packages...
success Saved lockfile.
  Done in 0.56s.
```

此时查看目录结构如下：

```js
projects/
|--package.json
|--project1/
|  |--package.json
|--project2
|  |--package.json
|--node_modules/
|  |--a/
|  |--project1/ -> ./project1/
```

说明：

* projects 是各个子项目的上级目录，术语上称之为 workspace-root，而 project1 和 project2 术语上称之为 workspace。

* yarn install 命令既可以在 workspace-root 目录下执行，也可以在任何一个 workspace 目录下执行，效果是一样的。

* 如果需要某个特殊的 workspace 不受Yarn Workspace管理，只需在此 workspace 目录下添加.yarnrc文件，并添加如下内容禁用即可：

```js
workspaces-experimental false
```

* 在 project1 和 project2 目录下并没有 node_modules 目录（特殊情况下才会有，如当 project1 和 project2 依赖了不同版本的 a 时）。

* /node_modules/project1 是 /project1 的软链接，软链接的名称使用的是 /project1/package.json#name 属性的值。

* 如果只是修改单个 workspace，可以使用 --focus 参数来快速安装相邻的依赖配置从而避免全部安装一次。

## Yarn Workspace 命令

### yarn workspace

针对特定的 workspace 执行指定的 \<command>，如：

```js
$ yarn workspace project1 add vue --dev 《 往 project1 添加 vue 开发依赖
$ yarn workspace project1 remove vue    《 从 project1 移除 vue 依赖
```

在 {workspace}/package.json#scripts 中定义的脚本命令，也可以作为 <command> 来执行。

下面是一个利用这个特点创建统一构建命令的例子：

projects/package.json:

```
{
    
  "scripts": {
    
    "build": "yarn workspaces run build"
  }
}
```

project1|project2/package.json:

```
{
    
  "scripts": {
    
    "build": "rollup -i index.js -f esm -o dist/bundle.js"
  }
}
```

执行 yarn build 的结果：

```js
$ yarn build
yarn run v1.22.0
$ yarn workspaces run build

> project1
$ rollup -i index.js -f esm -o dist/bundle.js

index.js → dist/bundle.js...
created dist/bundle.js in 70ms

> project2
$ rollup -i index.js -f esm -e project1 -o dist/bundle.js

index.js → dist/bundle.js...
created dist/bundle.js in 80ms
  Done in 2.45s.
```

### yarn workspaces

* yarn workspaces run <command>

在每个 workspace 下执行 <command>。如：

```js
yarn workspaces run test
```

将会执行各个 workspace 的 test script。

* yarn workspaces info [--json]

显示当前各 workspace 之间的依赖关系树。

```js
$ yarn workspaces info
yarn workspaces v1.21.1
{
    
  "project1": {
    
    "location": "project1",
    "workspaceDependencies": [],
    "mismatchedWorkspaceDependencies": []
  },
  "project2": {
    
    "location": "project2",
    "workspaceDependencies": [
      "project1"
    ],
    "mismatchedWorkspaceDependencies": []
  }
}
  Done in 0.12s.
```



## Yarn Workspace与Lerna

Lerna是社区主流的monorepo管理工具之一，集成了依赖管理、版本发布管理等功能。

使用Learn管理的项目的目录结构和yarn workspace类似。

Lerna的依赖管理是也基于yarn/npm，但是安装依赖的方式和yarn workspace有些差异：

Yarn workspace只会在根目录安装一个node_modules，这有利于提升依赖的安装效率和不同package间的版本复用。而Lerna默认会进到每一个package中运行yarn/npm install，并在每个package中创建一个node_modules。

目前社区中最主流的方案，也是yarn官方推荐的方案，是集成yarn workspace和lerna。使用yarn workspace来管理依赖，使用lerna来管理npm包的版本发布。

## 参考链接

* [5 分钟搞懂 Monorepo](https://xie.infoq.cn/article/4f870ba6a7c8e0fd825295c92)