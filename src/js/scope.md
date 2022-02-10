# 作用域与执行上下文

多开发人员经常混淆作用域和执行上下文的概念，误认为它们是相同的概念，但事实并非如此。

我们知道 JavaScript 属于解释型语言，JavaScript 的执行分为：解释和执行两个阶段,这两个阶段所做的事并不一样：

#### 解释阶段：

* 词法分析
* 语法分析
* 作用域规则确定

#### 执行阶段：

* 创建执行上下文
* 执行函数代码
* 垃圾回收
JavaScript 解释阶段便会确定作用域规则，因此作用域在函数定义时就已经确定了，而不是在函数调用时确定，但是执行上下文是函数执行之前创建的。执行上下文最明显的就是 this 的指向是执行时确定的。而作用域访问的变量是编写代码的结构确定的。

作用域和执行上下文之间最大的区别是：

**执行上下文在运行时确定，随时可能改变；作用域在定义时就确定，并且不会改变**。

一个作用域下可能包含若干个上下文环境。有可能从来没有过上下文环境（函数从来就没有被调用过）；有可能有过，现在函数被调用完毕后，上下文环境被销毁了；有可能同时存在一个或多个（闭包）。**同一个作用域下，不同的调用会产生不同的执行上下文环境，继而产生不同的变量的值**。

# 作用域

## 变量声明

###  var声明

用 `var` 声明的变量，不是函数作用域就是全局作用域。它们在代码块外也是可见的（译注：也就是说，`var` 声明的变量只有函数作用域和全局作用域，没有块级作用域）。 举个例子

```typescript
if (true) {
  var test = true; // 使用 "var" 而不是 "let"
}
alert(test); // true，变量在 if 结束后仍存在
```
由于 `var` 会忽略代码块，因此我们有了一个全局变量 `test`。
如果我们在第二行使用 `let test` 而不是 `var test`，那么该变量将仅在 `if` 内部可见： 

```typescript
if (true) {
  let test = true; // 使用 "let"
}
alert(test); // Error: test is not defined
```

###  let声明

块是由 {} 界定的代码块，大括号中有一个块。大括号内的任何内容都包含在一个块级作用域中.

因此，在带有let的块中声明的变量仅可在该块中使用 

```typescript
{
  // 使用在代码块外不可见的局部变量做一些工作
  let message = "Hello"; // 只在此代码块内可见
  alert(message); // Hello
}
alert(message); // Error: message is not defined
```
对于 `if`，`for` 和 `while` 等，在 `{...}` 中声明的变量也仅在内部可见： 
```typescript
if (true) {
  let phrase = "Hello!";
  alert(phrase); // Hello!
}
alert(phrase); // Error, no such variable!
```
###  const声明

const 声明的变量在块级作用域内，像let声明一样，const声明只能在声明它们的块级作用域中访问不能被修改并且不能被重新声明

这意味着用const声明的变量的值保持不变。不能修改或重新声明。因此，如果我们使用const声明变量，那么我们将无法做到这一点: 

```typescript
const greeting = 'say Hi';
greeting = 'say Hello instead'; // error: Assignment to constant variable.
```

## **全局作用域**

在代码中任何地方都能访问到的对象拥有全局作用域，一般来说以下几种情形拥有全局作用域：

```typescript
var outVariable = "我是最外层变量"; //最外层变量
function outFun() {
    //最外层函数
    var inVariable = "内层变量";
    function innerFun() {
        //内层函数
        console.log(inVariable);
    }
    innerFun();
}
console.log(outVariable); //我是最外层变量
outFun(); //内层变量
console.log(inVariable); //inVariable is not defined
innerFun(); //innerFun is not defined
```

所有末定义直接赋值的变量自动声明为拥有全局作用域

```typescript
function outFun2() {
    variable = "未定义直接赋值的变量";
    var inVariable2 = "内层变量2";
}
outFun2(); //要先执行这个函数，否则根本不知道里面是啥
console.log(variable); //未定义直接赋值的变量
console.log(inVariable2); //inVariable2 is not defined

```


## **函数作用域**

函数作用域,是指声明在函数内部的变量，和全局作用域相反，局部作用域一般只在固定的代码片段内可访问到，最常见的例如函数内部。

```typescript
function doSomething() {
    var blogName = "浪里行舟";
    function innerSay() {
        alert(blogName);
    }
    innerSay();
}
alert(blogName); //脚本错误
innerSay(); //脚本错误
```

## **块级作用域**

块级作用域可通过新增命令 let 和 const 声明，所声明的变量在指定块的作用域外无法被访问。块级作用域在如下情况被创建：

1. 在一个函数内部
2. 在一个代码块（由一对花括号包裹）内部
```typescript
function getValue(condition) {
    if (condition) {
        let value = "blue";
        return value;
    } else {
        // value 在此处不可用
        return null;
    }
    // value 在此处不可用
}
```

## **箭头函数**

箭头函数本身并不具有this，箭头函数在被申明确定this，这时候他会直接将当前作用域的this作为自己的this。还是之前的例子我们将函数改为箭头函数：

```typescript
var myName = "大飞哥";

var obj = {
  myName: "小小飞",
  func: () => {
    console.log(this.myName);
  }
}

var anotherFunc = obj.func;

obj.func();      // 大飞哥
anotherFunc();   // 大飞哥
```
上述代码里面的`obj.func()`输出也是“大飞哥”，是因为`obj`在创建时申明了箭头函数，这时候箭头函数会去寻找当前作用域，因为`obj`是一个对象，并不是作用域，所以这里的作用域是window，this也就是window了。 
再来看一个例子：

```typescript
var myName = "大飞哥";

var obj = {
  myName: "小小飞",
  func: function () {
    return {
      getName: () => {
        console.log(this.myName);
      }
    }
  }
}

var anotherFunc = obj.func().getName;

obj.func().getName();      // 小小飞
anotherFunc();   // 小小飞
```
两个输出都是“小小飞”，`obj.func().getName()`输出“小小飞”很好理解，这里箭头函数是在`obj.func()`的返回值里申明的，这时他的this其实就是`func()`的this，因为他是被`obj`调用的，所以this指向obj。 
## 作用域闭包

>红宝书(p178)上对于闭包的定义：闭包是指有权访问另外一个函数作用域中的变量的函数，
>MDN 对闭包的定义为：闭包是指那些能够访问自由变量的函数。
由此，我们可以看出闭包共有两部分组成：

* 是一个函数
* 能访问另外一个函数作用域中的变量
对于闭包有下面三个特性：

1、闭包可以访问当前函数以外的变量

```plain
function getOuter(){
    var date = '815';
    function getDate(str){
        console.log(str + date);  //访问外部的date
    }
    return getDate('今天是：'); //"今天是：815"
}
getOuter();
```
 2、即使外部函数已经返回，闭包仍能访问外部函数定义的变量
```plain
function getOuter(){
    var date = '815';
    function getDate(str){
        console.log(str + date);  //访问外部的date
    }
    return getDate;     //外部函数返回
}
var today = getOuter();
today('今天是：');   //"今天是：815"
today('明天不是：');   //"明天不是：815"
```
 3、闭包可以更新外部变量的值
```plain
function updateCount(){
    var count = 0;
    function getCount(val){
        count = val;
        console.log(count);
    }
    return getCount;     //外部函数返回
}
var count = updateCount();
count(815); //815
count(816); //816
```

## 闭包应用场景

具体应用场景你知道哪些？？

* 保护函数内的变量安全：如迭代器、生成器。
* 在内存中维持变量：如缓存数据、柯里化。

### 私有属性

```plain
var foo = (function(){
    var secret = 'secret'
    // “闭包”内的函数可以访问 secret 变量，而 secret 变量对于外部却是隐藏的
    return {
        get_secret() {
            return secret
        },
        new_secret(new_secret) {
            secret = new_secret
        }
    }
})()

foo.secret              // undefined
foo.get_secret()        // 'secret'
foo.new_secret('哈哈哈') // 修改secret值
foo.get_secret()        // '哈哈哈'
```
### 单例模式

```plain
class Modal {
    constructor(name) {
        this.name = name
        this.getName()
    }
    getName() {
        return this.name
    }
}

let ProxySing = (function(){
    let instance;
    return function(name) {
        if (!instance) {
            instance = new Modal(name)
        }
        return instance
    }
})()

let a = new ProxySing('问题框');
let b = new ProxySing('回答框');

console.log(a === b); // true
console.log(a.getName());  // '问题框'
console.log(b.getName());  // '问题框'
```
### 函数防抖

```plain
const fn = () => console.log('fn')
window.onresize = debounce(fn, 1000)
function debounce(fn, interval) {
    let timer = null;
    return function (...args) {
        if(timer) clearTimeout(timer);
        timer = setTimeout(() => {
            fn.apply(this, args);
        }, interval);
    }
}
```

# 执行上下文

当JavaScript代码执行一段可执行代码时，会创建对应的执行上下文

对于每个执行上下文，都有三个重要属性：

* 变量对象(Variable object，VO)
* 作用域链(Scope chain)
* this

## 变量对象

变量对象是与执行上下文相关的数据作用域，存储了在上下文中定义的变量和函数声明。

因为不同执行上下文下的变量对象稍有不同，所以我们来聊聊全局上下文下的变量对象和函数上下文下的变量对象。

### 全局上下文

全局上下文中的变量对象就是全局对象呐！

1.可以通过 this 引用，在客户端 JavaScript 中，全局对象就是 Window 对象

```plain
console.log(this);
```
2.全局对象是由 Object 构造函数实例化的一个对象。
```plain
console.log(this instanceof Object);
```
3.预定义了一堆，嗯，一大堆函数和属性。
```plain
// 都能生效
console.log(Math.random());
console.log(this.Math.random());
```
4.作为全局变量的宿主。
```plain
var a = 1;
console.log(this.a);
```
5.客户端 JavaScript 中，全局对象有 window 属性指向自身。
```plain
var a = 1;
console.log(window.a);

this.window.b = 2;
console.log(this.b);
```
### 函数上下文

执行上下文的代码会分成两个阶段进行处理：分析和执行，我们也可以叫做：

1. 进入执行上下文
2. 代码执行
#### **进入执行上下文**

当进入执行上下文时，这时候还没有执行代码，

变量对象会包括：

1. 函数的所有形参 (如果是函数上下文)
    1. 由名称和对应值组成的一个变量对象的属性被创建
    2. 没有实参，属性值设为 undefined
2. 函数声明
    1. 由名称和对应值（函数对象(function-object)）组成一个变量对象的属性被创建
    2. 如果变量对象已经存在相同名称的属性，则完全替换这个属性
3. 变量声明
    1. 由名称和对应值（undefined）组成一个变量对象的属性被创建；
    2. 如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性
举个例子：

```plain
function foo(a) {
  var b = 2;
  function c() {}
  var d = function() {};

  b = 3;

}

foo(1);
```
在进入执行上下文后，这时候的 AO 是：
```plain
AO = {
    arguments: {
        0: 1,
        length: 1
    },
    a: 1,
    b: undefined,
    c: reference to function c(){},
    d: undefined
}
```
#### **代码执行**

在代码执行阶段，会顺序执行代码，根据代码，修改变量对象的值

还是上面的例子，当代码执行完后，这时候的 AO 是：

```plain
AO = {
    arguments: {
        0: 1,
        length: 1
    },
    a: 1,
    b: 3,
    c: reference to function c(){},
    d: reference to FunctionExpression "d"
}
```
到这里变量对象的创建过程就介绍完了，让我们简洁的总结我们上述所说：
1. 全局上下文的变量对象初始化是全局对象
2. 函数上下文的变量对象初始化只包括 Arguments 对象
3. 在进入执行上下文时会给变量对象添加形参、函数声明、变量声明等初始的属性值
4. 在代码执行阶段，会再次修改变量对象的属性值
思考题：

1.第一题

```plain
function foo() {
    console.log(a);
    a = 1;
}

foo(); // ???

function bar() {
    a = 1;
    console.log(a);
}
bar(); // ???
```
第一段会报错：`Uncaught ReferenceError: a is not defined`。
第二段会打印：`1`。

这是因为函数中的 "a" 并没有通过 var 关键字声明，所有不会被存放在 AO 中。

第一段执行 console 的时候， AO 的值是：

```plain
AO = {
    arguments: {
        length: 0
    }
}
```
没有 a 的值，然后就会到全局去找，全局也没有，所以会报错。
当第二段执行 console 的时候，全局对象已经被赋予了 a 属性，这时候就可以从全局找到 a 的值，所以会打印 1。

2.第二题

```plain
console.log(foo);

function foo(){
    console.log("foo");
}

var foo = 1;
```
会打印函数，而不是 undefined 。
这是因为在进入执行上下文时，首先会处理函数声明，其次会处理变量声明，如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性。 

## 作用域链

当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级(词法层面上的父级)执行上下文的变量对象中查找，一直找到全局上下文的变量对象，也就是全局对象。这样由多个执行上下文的变量对象构成的链表就叫做作用域链。

下面，让我们以一个函数的创建和激活两个时期来讲解作用域链是如何创建和变化的。

### 函数创建

函数的作用域在函数定义的时候就决定了。

这是因为函数有一个内部属性 [[scope]]，当函数创建的时候，就会保存所有父变量对象到其中，你可以理解 [[scope]] 就是所有父变量对象的层级链，但是注意：[[scope]] 并不代表完整的作用域链！

举个例子：

```plain
 
function foo() {
    function bar() {
        ...
    }
}
```

函数创建时，各自的[[scope]]为：

```plain
foo.[[scope]] = [
  globalContext.VO
];

bar.[[scope]] = [
    fooContext.AO,
    globalContext.VO
];
```
### 函数激活

当函数激活时，进入函数上下文，创建 VO/AO 后，就会将活动对象添加到作用链的前端。

这时候执行上下文的作用域链，我们命名为 Scope：

```plain
Scope = [AO].concat([[Scope]]);
```
至此，作用域链创建完毕。
## **this**

`this`的绑定规则总共有下面5种。

1. 默认绑定（严格/非严格模式）
2. 隐式绑定
3. 显式绑定
4. new绑定
5. 箭头函数绑定

### this 的指向

 ES5 中，其实 this 的指向，始终坚持一个原理：this 永远指向最后调用它的那个对象

下面我们来看一个最简单的例子：

```plain
var name = "windowsName";
function a() {
    var name = "Libai";
    console.log(this.name);          // windowsName
    console.log("inner:" + this);    // inner: Window
}
var b = {
    name: "Libai",
    fn : function () {
        console.log(this.name);      // Libai
    }
}
a();
b.fn();             // Libai
window.b.fn();      // Libai 最后调用它的对象仍然是对象 a
```
 这个相信大家都知道为什么 log 的是 `windowsName`，我们看最后调用 a 的地方 `a()`，前面没有调用的对象那么就是全局对象 `window`，这就相当于是 `window.a()`
函数 `fn` 是对象 `b` 调用的，所以打印的值就是 `b` 中的 `name` 的值。是不是有一点清晰了呢~ 

### this的绑定规则

### 1 默认绑定

全局上下文默认this指向`全局对象window`, 严格模式下指向`undefined`。 

```plain
function foo() { 
    // "use strict";
    console.log( this.a );
}
var a = 2;
foo(); // 非严格模式下2，严格模式下TypeError
```
 
### 2 隐式绑定

这种情况是直接调用。this相当于全局上下文的情况。

```plain
let name = 2222;
let obj = {
    name: 3333,
    a: function() {
        console.log(this.name); // 2222
    }
}
let func = obj.a;
func();    
```
### 3.显示绑定

通过`call(..)` 或者 `apply(..)`方法。第一个参数是一个对象，在调用函数时将这个对象绑定到`this`。因为直接指定`this`的绑定对象，称之为显示绑定。

 

**apply 和 call 的区别**

```plain
fun.call(this, arg1, arg2)    // 使用 call，参数列表
fun.apply(this, [arg1, arg2]) // 使用 apply，参数数组

var a = {
    name : "Libai",
    fn : function (a,b) {
        console.log(this.name, a + b)
    }
}
var b = a.fn;

b.call(a, 1, 2)         // Libai 3
b.apply(a, [1, 2])      // Libai 3
```

call的源码：

```plain
// es6
Function.prototype.call = function (context, ...args) {
    context = context || window;
    context.fn = this;

    let result = context.fn(...args);

    delete context.fn
    return result;
}
```
 apply的源码：
```plain
// es6
Function.prototype.apply = function (context, args) {
    context = context || window;
    context.fn = this;

    let result = context.fn(...args);
    
    delete context.fn
    return result;
}
```
 
**bind**

bind 是创建一个新的函数，我们必须要手动去调用：

```plain
var a = {
    name : "Libai",
    fn : function (a,b) {
        console.log(this.name, a + b)
    }
}
var b = a.fn;

b.bind(a, 1, 2)()
```
bind的实现：
```plain
Function.prototype.bind = function (context,...args) {
    var self = this;
    return function (...bindArgs) {
        return self.apply(context, args.concat(bindArgs));
    }
}
```
### 4 new绑定

函数调用前使用了 new 关键字, 则是调用了构造函数。 这看起来就像创建了新的函数，但实际上 JavaScript 函数是重新创建的对象：

```plain
// 构造函数:
function myFunction(arg1, arg2) {
    this.firstName = arg1;
    this.lastName  = arg2;
}
// This creates a new object
var a = new myFunction("Li", "Cherry");
a.lastName;  // "Cherry"
```

### 5.箭头函数

箭头函数的 this 始终指向函数定义时的 this，而非执行时。

箭头函数需要记着这句话：“`箭头函数中没有 this 绑定，必须通过查找作用域链来决定其值`，如果箭头函数被非箭头函数包含，则 `this` 绑定的是最近一层非箭头函数的 `this`，否则，`this`为 `window`”。 

```plain
var name = "windowsName";
var a = {
    name : "Libai",
    func1: function () {
        console.log(this.name)     
    },
    func2: function () {
        setTimeout( () => {
            this.func1()
        },100);
    }
};
a.func2()     // Libai
```
 等价与:
```plain
var name = "windowsName";
var a = {
    name : "Libai",
    func1: function () {
        console.log(this.name)     
    },
    func2: function () {
        var _this = this;
        setTimeout( function() {
            _this.func1()
        },100);
    }
};
a.func2()       // Libai
```
 
