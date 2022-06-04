

## css

## 1. nth-child和:nth-of-type之间的差异

```typescript
<section>
    <p>我是第1个p标签</p>
    <p>我是第2个p标签</p>  <!-- 希望这个变红 -->
</section>
p:nth-child(2) { color: red; } //我是第2个p标签
p:nth-of-type(2) { color: red; } //我是第2个p标签

<section>
    <div>我是一个普通的div标签</div>
    <p>我是第1个p标签</p>
    <p>我是第2个p标签</p>  <!-- 希望这个变红 -->
</section>
p:nth-child(2) { color: red; } //我是第1个p标签
p:nth-of-type(2) { color: red; }//我是第2个p标签
```

## 2.css权重

|**选择器**|**格式**|**优先级权重**|
|:----|:----|:----|
|id选择器|#id|100|
|类选择器|.classname|10|
|属性选择器|a[ref=“eee”]|10|
|伪类选择器|li:last-child|10|
|标签选择器|div|1|
|伪元素选择器|li:after|1|
|相邻兄弟选择器|h1+p|0|
|子选择器|ul>li|0|
|后代选择器|li a|0|
|通配符选择器|*|0|

```typescript
<!--阅读下面HTML代码，段落标签<p>内文本最终显示的颜色是 -->
<style type="text/css"> 

     body { color:#333; } 

     #text{ color:#666; }

     .content { color:#00f; } 

     .gray { color:#f00; } 

</style> 

<p id="text" class="content gray">我本将心向明月，奈何明月照沟渠。</p>
```
## 3.对盒模型的理解

盒模型有 2 种：标准盒模型和 IE 盒模型，本别是由 W3C 和 IExplore 制定的标准。

如果给某个元素设置如下样式：

```css
.box {
    width: 200px;
    height: 200px;
    padding: 10px;
    border: 1px solid #ccc;
    margin: 10px;
}
```

标准盒模型认为：盒子的实际尺寸 = 内容（设置的宽/高） + 内边距 + 边框

![标准模型](../../asset/css/content.png)


IE 盒模型认为：盒子的实际尺寸 = 设置的宽/高 = 内容 + 内边距 + 边框

![IE 盒模型](../../asset/css/border.png)

在 CSS3 中新增了一个属性 box-sizing，允许开发者来指定盒子使用什么标准，它有 2 个值：

* content-box：标准盒模型；
* border-box：IE 盒模型；

# JavaScript

## 1. 运算符 ?? 与 || 比较
```typescript
let height = 0;
alert(height || 100); // 100
alert(height ?? 100); // 0
```
## 2.var变量提升

```typescript
var a = 99;            // 全局变量a
f();                   // f是函数，虽然定义在调用的后面，但是函数声明会提升到作用域的顶部。 
console.log(a);        // a=>99,  此时是全局变量的a
function f() {
  console.log(a);      // 当前的a变量是下面变量a声明提升后，默认值undefined
  var a = 10;
  console.log(a);      // a => 10
}
// 输出结果：
undefined
10
99
```
## 3.let没有变量提升与暂时性死区

```typescript
console.log(test);    // 错误：Uncaught ReferenceError ...
let test = 'abc';
// 这里就可以安全使用test
```
## 4.变量作用域

```typescript
//var 声明的变量没有块级作用域，它们仅在当前函数内可见，或者全局可见
{ 
  var i = 9;
} 
console.log(i);  // 9

//ES6新增的let，可以声明块级作用域的变量。
{ 
  let i = 9;     // i变量只在 花括号内有效！！！
} 
console.log(i);  // Uncaught ReferenceError: i is not defined
```

```typescript
for (var i = 0; i <10; i++) {  
  setTimeout(function() {  // 同步注册回调函数到 异步的 宏任务队列。
    console.log(i);        // 执行此代码时，同步代码for循环已经执行完成
  }, 0);
}
// 输出结果
10   共10个

// i虽然在全局作用域声明，但是在for循环体局部作用域中使用的时候，变量会被固定，不受外界干扰。
for (let i = 0; i < 10; i++) { 
  setTimeout(function() {
    console.log(i);    //  i 是循环体内局部作用域，不受外界影响。
  }, 0);
}
// 输出结果：
0  1  2  3  4  5  6  7  8 9
```
## 5.全局作用域

```typescript
//显式声明
var winValue = 10;
console.log(window.winValue); // 10

//隐式声明 不带有声明关键字的变量，JS 会默认帮你声明一个全局变量
function foo(value) {
    result = value + 1;     // 没有用 var 声明
    return result;
};
foo(1);    
console.log(window.result);    // 2 <=  挂在了 window全局对象上 
```
## 6.函数作用域

1. 每个函数都有自己的作用域，而且调用一次就会生成新的作用域
2. 只能在函数内部才能访问，外部是没有权限访问的
3. 进入函数内部时开启，函数执行完毕后销毁

```typescript
function bar() {
    var foo = 'test';
}
console.log(foo); // Uncaught ReferenceError: foo is not defined
```

## 7.块级作用域

凡是由{}符号包裹起来的都是块作用域

```typescript
for(let i = 0; i < 5; i++) {
    // ...
}
console.log(i); // Uncaught ReferenceError: i is not defined
//在 for 循环执行完毕之后 i 变量就被释放了
```
## 8.词法(静态)作用域

```typescript
var value = 1;
function foo() {
    console.log(value);
}
function bar() {
    var value = 2;
    foo();
}
bar();
// 结果是1
```
## 9.动态作用域

```typescript
var name = "The Window";
var object = {
　　　　name : "My Object",
　　　　getNameFunc : function(){
　　　　　　return this.name;
　　　　}
};
console.log(object.getNameFunc())
// 结果是My Object

var name = "The Window";
var object = {
　　　　name : "My Object",
　　　　getNameFunc : function(){
　　　　　　return function(){
　　　　　　　　return this.name;
　　　　　　};
　　　　}
};
console.log(object.getNameFunc()())
// 结果是The Window
```
## 10.作用域链

作用域链：当访问一个变量时，解释器会首先在当前作用域查找，如果没有找到，就去父作用域找，直到找到该变量或者不在父作用域中，这就是作用域链。

```typescript
<!-- DEMO1 -->
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f();
}
checkscope();

<!-- DEMO2 -->
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}
checkscope()();

// 结果都是 local scope

var a = 1
function foo () {
    var b = 2
    console.log(a)
}
foo() // 1
console.log(b) // Uncaught ReferenceError: b is not defined
```
## 11.闭包

闭包由两部分构成：

1. 函数
2. 能访问另外一个函数作用域中的变量

```typescript
function bar() {
    var x = 1;
    return function () {
        var y = 2;
        return x + y;
    }
}
var foo = bar(); // 这一句执行完，变量x并没有被回收，因为要内部函数还需要引用
console.log(foo()) // 3  执行内部函数，引用外部变量x
```

## 12.原型继承

```typescript
let animal = {
  eats: true
};
let rabbit = {
  jumps: true
};
rabbit.__proto__ = animal; // (*)
// 现在这两个属性我们都能在 rabbit 中找到：
alert( rabbit.eats ); // true (**)
alert( rabbit.jumps ); // true
```

## 13.原型对象

```typescript
let animal = {
  eats: true
};
function Rabbit(name) {
  this.name = name;
}
Rabbit.prototype = animal;
let rabbit = new Rabbit("White Rabbit"); //  rabbit.__proto__ == animal
alert( rabbit.eats ); // true
```
## 14.原型链

```typescript
 // 原型链继承
 function Super(){
   this.color=['red','yellow','black']
 }
 function Sub(){}
 //继承了color属性 Sub.prototype.color=['red','yellow','black']
 Sub.prototype=new Super()
 //创建实例 instance1.__proto__.color
 const instance1=new Sub()
 const instance2=new Sub()
 console.log(instance1.__proto__.color===instance2.__proto__.color) //true
```
## 15.作用域链和原型继承查找时的区别

如果去查找一个普通对象的属性，但是在当前对象和其原型中都找不到时，会返回undefined；但查找的属性在作用域链中不存在的话就会抛出ReferenceError。

## 16.JavaScript 中的包装类型

在 JavaScript 中，基本类型是没有属性和方法的，但是为了便于操作基本类型的值，在调用基本类型的属性或方法时 JavaScript 会在后台隐式地将基本类型的值转换为对象

```typescript
var a = "abc";
a.length; // 3
a.toUpperCase(); // "ABC"

a.name="ruijie"
console.log(a.name)；//undefined

var a = new Boolean( false );
if (!a) {
	console.log( "Oops" ); // never runs
}
```
## 17.判断基本数据类型

### typeof 

```typescript
console.log(typeof 2);               // number
console.log(typeof true);            // boolean
console.log(typeof 'str');           // string
console.log(typeof []);              // object    
console.log(typeof function(){});    // function
console.log(typeof {});              // object
console.log(typeof undefined);       // undefined
console.log(typeof null);            // object
```
其中数组、对象、null都会被判断为object，其他判断都正确。
### instanceof 

`instanceof`可以正确判断对象的类型，**其内部运行机制是判断在其原型链中能否找到该类型的原型**。

```typescript
console.log(2 instanceof Number);              // false
console.log(true instanceof Boolean);          // false 
console.log('str' instanceof String);          // false 
console.log([] instanceof Array);              // true
console.log(function(){} instanceof Function); // true
console.log({} instanceof Object);             // true
```
### **constructor**

需要注意，如果创建一个对象来改变它的原型，`constructor`就不能用来判断数据类型了

```typescript
console.log((2).constructor === Number);                // true
console.log((true).constructor === Boolean);            // true
console.log(('str').constructor === String);            // true
console.log(([]).constructor === Array);                // true
console.log((function() {}).constructor === Function);  // true
console.log(({}).constructor === Object);               // true
```
### **Object.prototype.toString.call()**

使用 Object 对象的原型方法 toString 来判断数据类型

```typescript
var a = Object.prototype.toString;
 
console.log(a.call(2));            //[object Number]
console.log(a.call(true));         //[object Boolean]
console.log(a.call('str'));        //[object String]
console.log(a.call([]));           //[object Array]
console.log(a.call(function(){})); //[object Function]
console.log(a.call({}));           //[object Object]
console.log(a.call(undefined));    //[object Undefined]
console.log(a.call(null));         //[object Null]
```
注意：同样是检测对象obj调用toString方法，obj.toString()的结果和Object.prototype.toString.call(obj)的结果不一样，这是为什么？
这是因为toString是Object的原型方法，而Array、function等**类型作为Object的实例，都重写了toString方法**

## 18.数据类型转换

### 基本数据类型 — 基本数据类型转换

#### 转换为string类型

转换发生在输出内容的时候，也可以通过 `String(value)` 进行显式转换。原始类型值的 string 类型转换通常是很明显的。 

```typescript
let value = true;
alert(typeof value); // boolean
value = String(value); // 现在，值是一个字符串形式的 "true"
alert(typeof value); // string
```

#### 转换为number类型

转换发生在进行算术操作时，也可以通过 `Number(value)` 进行显式转换。 

|值|变成……|
|:----|:----|
|undefined|NaN|
|null|0|
|true / false|1 / 0|
|string|“按原样读取”字符串，两端的空白会被忽略。空字符串变成 0。转换出错则输出 NaN。|

#### 转换为boolean类型

转换发生在进行逻辑操作时，也可以通过 `Boolean(value)` 进行显式转换 

|值|变成……|
|:----|:----|
|0, null, undefined, NaN, ""|false|
|其他值|true|

### 对象 — 基本数据类型转换 

1. 调用 `obj[Symbol.toPrimitive](hint)` —— 带有 symbol 键 `Symbol.toPrimitive`（系统 symbol）的方法，如果这个方法存在的话，
```typescript
let user = {
  name: "John",
  money: 1000,
  [Symbol.toPrimitive](hint) {
    alert(`hint: ${hint}`);
    return hint == "string" ? `{name: "${this.name}"}` : this.money;
  }
};
// 转换演示：
alert(user); // hint: string -> {name: "John"}
alert(+user); // hint: number -> 1000
alert(user + 500); // hint: default -> 1500
```
2. 否则，如果 hint 是 `"string"` —— 尝试调用 `obj.toString()` 或 `obj.valueOf()`，无论哪个存在
```typescript
let user = {name: "John"};
alert(user); // [object Object]
alert(user.valueOf() === user); // true
```
3. 否则，如果 hint 是 `"number"` 或 `"default"` —— 尝试调用 `obj.valueOf()` 或 `obj.toString()`，无论哪个存在。
```typescript
let user = {
  name: "John",
  money: 1000,
  // 对于 hint="string"
  toString() {
    return `{name: "${this.name}"}`;
  },
  // 对于 hint="number" 或 "default"
  valueOf() {
    return this.money;
  }
};
alert(user); // toString -> {name: "John"}
alert(+user); // valueOf -> 1000
alert(user + 500); // valueOf -> 1500
```

## 19.null与undefined的区别

首先 Undefined 和 Null 都是基本数据类型，这两个基本数据类型分别都只有一个值，就是 undefined 和 null。

undefined 代表的含义是**未定义**，null 代表的含义是**空对象**。一般变量声明了但还没有定义的时候会返回 undefined，null主要用于赋值给一些可能会返回对象的变量，作为初始化。

```typescript
null == undefined //true
null === undefined //false
```
## 20. == 操作符的强制类型转换规则？

1. 首先会判断两者类型是否**相同，**相同的话就比较两者的大小；
2. 类型不相同的话，就会进行类型转换；
3. 会先判断是否在对比 `null` 和 `undefined`，是的话就会返回 `true`
4. 判断两者类型是否为 `string` 和 `number`，是的话就会将字符串转换为 `number`
```typescript
 1 == '1'
       ↓
 1 ==  1
```
5.判断其中一方是否为 `boolean`，是的话就会把 `boolean` 转为 `number` 再进行判断
```typescript
'1' == true
        ↓
'1' ==  1
        ↓
 1  ==  1
```
6.判断其中一方是否为 `object` 且另一方为 `string`、`number` 或者 `symbol`，是的话就会把 `object` 转为原始类型再进行判断
```typescript
'1' == { name: 'js' }
        ↓
'1' == '[object Object]'
```
其流程图如下：
![图片](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAA+0AAAGqCAIAAADx5zK5AAAgAElEQVR4AezBDWDThYH//3e+TZ+oLYUWKM8gCiIoDyKIrFg0aBXKOoGdG3N1j26CeP68bfdTdwM32G3nNv93K5vb4Xa3qmPzsUmxtYyAtVPrQxxqGdXa0AKl0KaVlJKHJvnl7I1/B4ql0DRpP6+XKRQKISIiIiIiMcVARERERERijYGIiIiIiMQaAxERERERiTUGIiIiIiISawxERERERCTWGIiIiIiISKwxEBERERGRWGMgIiIiIiKxxkBERERERGKNgYiIiIiIxBoDERERERGJNQYiIiIiIhJrDEREREREJNYYiIiIiIhIrDEQEREREZFYYyAiIiIiIrHGQEREREREYo2BiIiIiIjEGgMREREREYk1BiIiIiIiEmsMREREREQk1hiIiIiIiEisMRARERERkVhjICIiIiIiscZARERERERijYGIiIiIiMQaAxERERERiTUGIiIiIiISawxERERERCTWGIiIiIiISKwxEBERERGRWGMgIiIiIiKxxkBERERERGKNgYiIiIiIxBoDERERERGJNQYiIiIiIhJrDEREREREJNYYiIiIiIhIrDEQEREREZFYYyAiIiIiIrHGQEREREREYo2BiIiIiIjEGjODjOlhpNdCtyMiIiIi0cBARERERERijYGIiIiIiMQaAxERERERiTUGIiIiIiISawxERERERCTWGIiIiIiISKwxEBERERGRWGMgIiIiIiKxxkBERERERGKNgYiIiIiIxBoDERERERGJNQYiIiIiIhJrDEREREREJNYYiIiIiIhIrDEQEREREZFYYyAiIiIiIrHGQPrMjGFcMxoT/2NSKjljiDc4FylmcsYwIokuk1NZlIWIiIiIDEIG0mfSEtixnLUzSU/ghRXcNg1/kHPhC/LHpXx7Nl1+dQ3/NAsRERERGYTMSJ95qYnvvcaPFrBiIh2drK2guweu5KuX0N1/vM0bzfwmh+7eaGb5c3TxByl6l1su4tsvk5nEkjGsLkdEREREBiEz0pf+9U1unszSceQUc7yT7p56n7da6O6vbbR4uauS7lxeuvvNPv7xMrJHM2MYbV5K9iMiIiIig5AZ6UuXDWfmcNp8rJ3J7ka6G57ExFS6O3yCEwEmptJdQhzd7WnhjWY+fxHT0vl9Lb4gIiIiIjIImZE+k2zm8eso3k/h29hX8KVp/GYfJ83KYOVkujvuJ2zlZLp7p5VH36W7R/7KpvmkxvPtlxERERGRwckUCoUYTEwPEzG/yGblhczYxlEPP1nI16cz+wlqj3GOhiXSeCt1bqZvI8JCtyMiIiIi0cAUCoUYTEwPE+uGJXL4i3z3VX78JhEWuh0RERERiQZmJKbcczm3TeMDH7/ei4iIiIgMWmYkprzl4td7Kd5PqxcRERERGbTMSEx5/gDPH0BEREREBjkDERERERGJNQYiIiIiIhJrDEREREREJNYYiIiIiIhIrDEQEREREZFYYyAiIiIiIrHGQEREREREYo2BiIiIiIjEGgMREREREYk1BiIiIiIiEmtMoVAIERERERGJKQYiIiIiIhJrDEREREREJNYYiIiIiIhIrDEQEREREZFYYyAiIiIiIrHGQEREREREYo2BRA3ThxARERER+SQGIiIiIiISawxERERERCTWGIiIiIiISKwxEBERERGRWGMgIiIiIiKxxkBERERERGKNgYiIiIiIxBoDERERERGJNQYiIiIiIhJrDEREREREJNYYiIiIiIhIrDEQEREREZFYYyAiIiIiIrHGQEREREREYo2BiIiIiIjEGgMREREREYk1BtLfAoFAa2sr3bS2tgYCAUREREREPoaB9Kt33nln7ty5t912G93cdtttc+fOfeeddxARERER+ShmpF9lZGQ4nc49e/Zs376dD23fvr24uDgtLS0jIwMRERERkY9iIP0qKytr48aNwPr16/nQ+vXrgY0bN2ZlZSEiIiIi8lFMoVAI6VednZ1z5sx5++23+ZuZM2c6HA6z2YyIiIiIyEcxkP5mNpsLCwvpprCw0Gw2IyIiIiLyMQwkCixevHjNmjV8aM2aNYsXL0ZERERE5OOZQqEQEgUOHz48ffp0YO/evVlZWYiIiIiIfDxTKBRCRERERERiioGIiIiIiMQaAxERERERiTUGIiIiIiISawxERERERCTWGIiIiIiISKwxkKjhDfDXVmRg+EsLMjC0+6n9ABkY/tKCDAwuD/VuRAY5A4kO3gD5ZSx6FkczEuusTuY/xdoKJNa1+8ktIbuYmjYk1hXVMPcJ7q9CYp3Lg8VGjpV6NyKDmYFEAW+A/DJKG3B5sdhwNCOxy+pkVTm+IFuqWVuBxK52P7klVDbR2EGOlZo2JHYV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Nt8qjnl848INLN4A7TggyUVlM+30iaWulWOtfOPM2Xx7OObxRZJdRDs52IDfJMhwEGHQ4ufEXT+M3XWsLMPy5+k0+rh2Fe2c2ZN5WQx9jSDDwY+G85sx/GIdz+7gBKW4qGjmy+odQ7Of6hbaOKBPLG4P9V7+JXM23yqOeXz9YpykdeOTSkJ1jaDFT4sfGzFn8+3hmMe/4eence/p3P0JjxfxH3F6EjtqafDSxgHJLiqa+Tc4YHIPPqmkxU+obpE0+Wjx05EDesRQ1siXYs5G5KvmRGzrnN7cmcGYJKYtRb4p3gCfy4TKZjo62IB8KZXNtBMwafHzpaRG89AEFpQwMJ7Tkzg/j442VTFrBW0CJk8W8/oeals5cRXN/BsONdKOCQcakBPX6OOTStqpbUW+fqUNzMxnZRn/KRuO0o4JFc38e0z48Agd1Xj4IiaUNSLyLeREbOtAA0v3c9cnrKtARI7j15+y4hBjk1m0j5VlePx0VNfKxiraqWpBRL6UN/ciIl8PJ2JbO2rZUYuInIi1FaytQERE5KRhICIiIiIidmMgIiIiIiJ2YyAiIiIiInZjICIiIiIidmMgIiIiIiJ2YyAiIiIiInZjICIiIiIidmMgIiIiIiJ2YyAiIiIiInZjICIiIiIiduNERETkP8ecjZwczNmIyLeZwzRNRERERETEVgxERERERMRuDERERERExG4MRERERETEbgxERERERMRuDERERERExG4MRERERETEbgxEROSLOT6DnBQcn0Hsz/EZRDo3AxERERERsRsDERERERGxGwMREREREbEbAxERERERsRsDERERERGxGwMREREREbEbAxERERERsRsDERERERGxGwMREREREbEbAxERERERsRsDERERERGxGwMREREREbEbAxERERERsRsDERERERGxGwMREREREbEbAxERERERsRsDERHpwO/3u91uQrjdbr/fj9iQ3+93u92EcLvdfr8fsRu/3+92uwnhdrv9fj8inZKBiIj8s+Li4szMzJycHELk5ORkZmYWFxcjtlJcXJyZmZmTk0OInJyczMxMxFaKi4szMzNzcnIIkZOTk5mZWVxcjEjn40RERP5ZYmJiaWlpYWFhXl4en8nLy1uyZEl8fHxiYiJiK4mJiaWlpYWFhXl5eXwmLy9vyZIl8fHxiK0kJiaWlpYWFhbm5eXxmby8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)


```typescript
0 == undefined;       //false
0 === undefined;      //false
0 == null;            //false
0 === null;           //false
undefined == null;    //true 
undefined === null    //false
```
## 21.Object.is() 与比较操作符 “===”、“==” 的区别

* 使用双等号（==）进行相等判断时，如果两边的类型不一致，则会进行强制类型转化后再进行比较。
* 使用三等号（===）进行相等判断时，如果两边的类型不一致时，不会做强制类型准换，直接返回 false。
* 使用 Object.is 来进行相等判断时，一般情况下和三等号的判断相同，它处理了一些特殊的情况，比如 -0 和 +0 不再相等，两个 NaN 是相等的。


```typescript
NaN == NaN         //false
NaN === NaN        //false
Object.is(NaN,NaN) //true

+0 == -0           //true
+0 === -0          //true
Object.is(+0,-0)   //false
```

