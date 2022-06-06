## Lerna 是什么

Lerna 是一个管理工具，用于管理包含多个软件包（package）的 JavaScript 项目

## 解决什么问题

为了代码共享, 大型项目会将代码分成多个代码仓库为独立的包(package)

如果某个包更改, 需要反向要求依赖该包的包升级依赖版本, 包少的时候还不是很成问题, 但当依赖链错综复杂时, 这个升级依赖的过程会极为痛苦

为了解决这个问题, 如 Babel, React 等项目会将多个包放到一个代码仓库进行管理, 此种管理方式称为: Monorepo. Lerna 是一个优化 Monorepo 工作流的工具

## Lerna 工作模式

一个仓库下有多个包, 那么每个包的版本号如何管理?

**Lerna 支持两种模式:** 

* ``固定模式(fixed)``

  所有包是统一的版本号，每次升级，所有包版本统一更新，不管这个包内容改变与否

  具体体现在，lerna 的配置文件 lerna.json 中永远会存在一个确定版本号：

  ```js
  {
  "version": "0.0.1"
  }
  ```
* ``独立模式(independent)``

    每个包是单独的版本号，每次lerna 触发发布命令，每个包的版本都会单独变化

  具体体现在，lerna 的配置文件 lerna.json 中没有一个确定版本号，而是：

  ```js
    {
      "version": "independent"
    }
  ```

lerna init 默认初始化为 Fixed/Locked mode, 推荐使用. 独立版本号会导致版本依赖关系错综复杂, 长期维护会混乱

## 如何使用

#### 安装

```js
npm install --global lerna
```

#### 初始化项目

```js
# 默认固定模式
lerna init 
# 要采用独立模式的话
lerna init -i
# lerna init --independent
```

#### 生成的代码结构

```js
└── lerna/
   ├── packages/
   ├── lerna.json
   └── package.json
```

#### lerna.json

```js
{
    "useWorkspaces": true, // 使用 workspaces 配置。此项为 true 的话，将使用 package.json 的 "workspaces"，下面的 "packages" 字段将不生效
    "version": "0.1.0", // 所有包版本号，独立模式-"independent"
    "npmClient": "cnpm", // npm client，可设置为 cnpm、yarn 等
    "packages": [ // 包所在目录，可指定多个
        "packages/*"
    ],
    "command": { // lerna 命令相关配置
        "publish": { // 发布相关
            "ignoreChanges": [ // 指定文件或目录的变更，不触发 publish
                ".gitignore",
                "*.log",
                "*.md"
            ]
        },
        "bootstrap": { // bootstrap 相关
            "ignore": "npm-*",  // 不受 bootstrap 影响的包
            "npmClientArgs": [ // bootstr 执行参数
                "--no-package-lock"
            ]
        }
    }
}
```

## 常用命令

#### lerna init

初始化 或 升级 当前仓库为 lerna 模式。

#### lerna bootstrap

安装子包依赖。 相当于每个子包下执行 npm i 。 根目录的依赖并不会安装。

#### lerna create \<name>

新增子包

#### lerna add <package> [–dev] [–scope=module]

新增依赖, 默认会在每个包中添加对应依赖。 通过 --scope 参数指定仅特定包增加此依赖。

#### lerna run [script]

每个子包中执行对应的script命令。

#### lerna exec

每个子包中执行对应的命令。 例: lerna exec -- ls  与 lerna run 的区别在于执行的命令是 shell 不是 package.json 中的 scripts。

#### lerna version

创建新的版本. 会自动维护跟目录及子包package.json中的版本号, 子包直接的依赖版本号也会自动更新。

#### lerna clean

删除所有子包中的 node_modules 目录。 根目录的 node_modules 不会删除

## 更优体验 = Lerna + Yarn

#### 配置 lerna 使用 yarn 管理依赖

在 lerna.json 中配置 "npmClient": "yarn" 即可.

#### 配置 lerna 启用 yarn Workspaces

1. 配置lerna.json/useWorkspaces = true ;

2. 配置根目录 package.json/workspaces = ["pacages/*"] , 此时 lerna.json 中的 packages 配置项将不再使用;

3. 配置根目录 package.json/private = true ;

上面三个配置项需同时开启, 只开启一个 lerna 会报错.

此时执行 lerna bootstrap 等价于 lerna bootstrap --npm-client yarn --use-workspaces 等价于 yarn install 

由于 yarn 会自动 hosit 依赖包, 无需再 lerna bootstrap 时增加参数 --hoist (加了参数 lerna 也会报错)