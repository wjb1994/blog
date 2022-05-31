## CSS NEXT 语法

cssnext 和 css4 并不是一个东西，cssnext使用下个版本css的草案语法

CSS 是纯静态的语法，初学者上手很容易，但是随着时间的推移，也暴露出了很多问题，例如：

* 无法定义变量
* 选择器无法嵌套使用
* 无法继承其他选择器

为了解决这些问题，社区出现了很多预处理器，例如：

* less
* sass
* stylus

预处理器解决了传统 css 的问题，但是最终上线时也得编译回源生 css，要是源生的 css 就支持这些功能就好了。css next 代表下一代的 css 规范，这样源生的 css 就支持了这些规则，以后就不需要再使用预处理器来实现这些规则了。

## CSS NEXT 规则

* 自定义属性 & var()
* 自定义属性集 & @apply
* calc() & var()
* 媒体查询 & @custom-media
* 自定义选择器 & @custom-selector
* 嵌套 & &
* color()

#### 1.custom properties & var()

自定义 css 变量，方便复用。

```css
:root {
  --mainColor: red;
}

a {
  color: var(--mainColor);
}
```

#### 2.custom properties set & @apply

自定义一套 css 样式，通过 @apply 复用。

```css
:root {
  --danger-theme: {
    color: white;
    background-color: red;
  }
}

.danger {
  @apply --danger-theme;
}
```

#### 3.reduced calc()

使用计算属性。

```css
:root {
  --fontSize: 1rem;
}

h1 {
  font-size: calc(var(--fontSize) * 2);
}
```
#### 4.更多语法

点击[这里](https://cssnext.github.io/features/)进行查看

## 如何使用

安装：cnpm install postcss-cssnext --save-dev

别忘了在postcss.config.js配置：'postcss-cssnext':{}