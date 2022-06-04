## new的实现

new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。new 关键字会进行如下的操作

* 1：创建一个空的简单JavaScript对象（即{}）；
* 2：链接该对象（即设置该对象的构造函数）到另一个对象 ；
* 3：将步骤1新创建的对象作为this的上下文 ；
* 4：如果该函数没有返回对象，则返回this。

所以我们可以根据四个步骤模拟实现new 运算符

```javascript
function Person(name, age){
 this.name = name;
 this.age = age;
}

var person01 = new Person("Monica","Galen")
// new做了以下隐式操作
new Person{
    // 步骤1：创建了一个空对象
    var obj = {};
    //  步骤2...
    obj.__proto__ = Person.prototype;   
    // 步骤3...
    var result = Person.apply(obj,"Monica","Galen");
    // 步骤4...
    return (typeof result === 'object' && result) || obj
}
```

## call的实现

all方法的实现主要有以下三步，比如 fn.call(obj, a, b) ：

* 1： 把调用函数fn的上下文指向obj
* 2： 形参a,b等是以逗号分隔传进去
* 3: 执行函数fn，并返回结果

```js
Function.prototype.myCall = function (context) {
  context = context ? Object(context) : window 
  context.fn = this // 重置上下文
  let args = [...arguments].slice(1) // 截取参数a,b
  let r = context.fn(...args) // 执行函数
  delete context.fn // 删除属性，避免污染
  return r // 返回结果
}

// 浏览器环境下
var a = 1, b = 2;
var obj ={a: 10,  b: 20}
function test(key1, key2){
  console.log(this[key1] + this[key2]) 
}
test('a', 'b') // 3
test.myCall(obj, 'a', 'b') // 30

```

## apply的实现

apply方法和call方法大同小异，唯一差别就是，apply传入的参数是数组格式。

```js
// apply 原理
Function.prototype.myApply = function (context) {
  context = context ? Object(context) : window
  context.fn = this
  let args = [...arguments][1]
  if (!args) {
    return context.fn()
  }
  let r = context.fn(...args)
  delete context.fn;
  return r
}

// 浏览器环境下
var a = 1, b = 2;
var obj ={a: 10,  b: 20}
function test(key1, key2){
  console.log(this[key1] + this[key2]) 
}
test('a', 'b') // 3
test.myCall(obj, ['a', 'b']) // 30  注意这里是传入数组 ['a', 'b']
```

## bind方法实现

bind方法和call、apply方法的差别是，他们都改变了上下文，但是bind没有立即执行函数

```js
// bind 原理
Function.prototype.Mybind = function (context) {
  let _me = this
  return function () {
    return _me.apply(context)
  }
}

var a = 1, b = 2;
var obj ={a: 10,  b: 20}
function test(key1, key2){
  console.log(this[key1] + this[key2]) 
}
var fn = test.bind(obj)
fn('a', 'b') // 30
```



## 防抖函数

防抖函数：事件被触发，每次执行事件处理回调时清除上一次设置的定时器，并重新设置一个定时器，从而实现在规定时间x内执行定时器里的回调函数，如果在规定时间x内该事件又被触发了，那定时器清零重新计算时间

```js
function debounce(callback,delay){
    let timer=null;
    var that=this;

    return function(){
        var args=Array.prototype.slice.call(arguments);

        if(timer !== null){
            clearTimeout(timer)
        }

        timer=setTimeout(()=>{
            callback.apply(that,args)
        },delay)
    }
}
```

## 节流函数

事件被触发，事件处理回调里设置一个定时器，定时时间间隔x，保证每次间隔时间x都会执行一次定时器里的回调，显然节流函数的执行定时器回调等待时间就是定时器设置时间x，执行完当前次定时任务结束后才会进行下一次定时x的任务

> 定时器版

```js
function throttle(fn,delay){
    var timer=null;
    var that=this;

    return function(){
        //var args = [...arguments];
        var args=Array.prototype.slice.call(arguments);

        if(!timer){
            timer = setTimeout(()=>{
                fn.apply(that,args);
                timer=null;
            },delay)
        }
    }
}
```

> 时间戳版

```js
function throttle(fn, delay){
    let previous = 0;
    return function(){
        let now = Date.now(), context = this, args = [...arguments];
        if(now - previous > delay){
            fn.apply(context, args);
            previous = now; // 闭包，记录本次执行时间戳      
        }    
    }        
}

```

## curry柯里化函数

柯里化就是将一个接收多个参数的函数转化为一系列使用一个参数的函数的技术。实现的效果就是

```js
const fun = (a, b, c) => {return [a, b, c]};

//上述函数经过科里化后就是
const curriedFun = curry(fun);
// curriedFun的调用变为 curriedFun(a)(b)(c)

```

下面我们来看看curry函数应该怎么实现

```js
// 观察上诉柯里化调用发现，它其实就是把参数都搜集起来了，每次调用搜集几个参数
// 当搜集的参数足够时执行主方法
const curry = (fn) => {
  // 先记录主方法原始的参数个数，fn.length就是函数接收的参数个数
  const paramsLength = fn.length;

  return executeFun = (...args) => {
    // 如果接收参数够了，执行主方法
    if(args.length >= paramsLength) {
      return fn(...args);
    } else {
      // 如果参数不够，继续接收参数
      return (...args2) => {
        // 注意executeFun接收的参数是平铺的，需要将数组解构
        return executeFun(...args.concat(args2));
      }
    }
  }
}

// 现在看下结果
curriedFun(1)(2)(3); // [1, 2, 3]
curriedFun(1, 2)(3); // [1, 2, 3]
curriedFun(1, 2, 3); // [1, 2, 3]

```

## compose组合函数

函数组合是指将多个函数按顺序执行，前一个函数的返回值作为下一个函数的参数，最终返回结果。
这样做的好处是可以将复杂任务分割成多个子任务，然后通过组合函数再组合成复杂任务。我们要实现的就是这样的组合函数（compose 函数），实际就一个工具函数

```js
function fn1(num) {
    return num + 1
}
function fn2(num) {
    return num + 2
}
function fn3(num) {
    return num + 3
}
      
// 我们需要实现这样一个组合函数，接受参数为需要组合的子函数，最后返回组合后的函数。
  
function compose(fn1,fn2,fn3) {
    return function() {
        // ...
    }
}
// 最终目的使 compose(fn3,fn2,fn1)(5) === fn3(fn2(fn1(5)))
```

接下来我们实现下compose函数

> 普通实现

```js
function compose(...fns) {
      // reverse() 作用是使输入函数从右往左执行
      fns = fns.reverse()
      return function(...args) {
          let res;
          fns.forEach((fn,index) => {
              if(index === 0) {
                  res = fn.apply(this, args)
              } else {
                  res = fn(res)
              }
          })
          return res
      }
}
```

> reduce实现

```js
const compose = function(){
  // 将接收的参数存到一个数组， args == [multiply, add]
  const args = [].slice.apply(arguments);
  return function(x) {
    return args.reduceRight((res, cb) => cb(res), x);
  }
}

//es6实现
const compose = (...args) => x => args.reduceRight((res, cb) => cb(res), x);
```
> pipe函数

pipe函数跟compose函数的作用是一样的，也是将参数平铺，只不过他的顺序是从左往右

```js
const pipe = function(){
  const args = [].slice.apply(arguments);
  return function(x) {
    return args.reduce((res, cb) => cb(res), x);
  }
}

//es6实现
const pipe = (...args) => x => args.reduce((res, cb) => cb(res), x)

// 参数顺序改为从左往右
let calculate = pipe(add, multiply);
let res = calculate(10);
console.log(res);    // 结果还是200

```

## promise实现

代码过多，要学的，[请点我](https://segmentfault.com/a/1190000023180502)

## Object.create实现

Object.create方法的主要逻辑：创建一个对象，手动设置其隐式原型__proto__属性为传入的参数，然后将这个对象返回。

```js
Object.myCreate = function (proto) {
    function Fn () {}
    Fn.prototype = proto
    const obj = new Fn()
    if (propertyObject !== undefined) {
      Object.defineProperties(obj, propertyObject)
    }
    if (proto === null) {
      // 创建一个没有原型对象的对象，Object.create(null)
      obj.__proto__ = null
    }
    return obj
}

// 示例
// 第二个参数为null时，抛出TypeError
// const throwErr = Object.myCreate({a: 'aa'}, null)  // Uncaught TypeError
const obj1 = Object.myCreate({a: 'aa'})
console.log(obj1)  // {}, obj1的构造函数的原型对象是{a: 'aa'}
const obj2 = Object.myCreate({a: 'aa'}, {
  b: {
    value: 'bb',
    enumerable: true
  }
})
console.log(obj2)  // {b: 'bb'}, obj2的构造函数的原型对象是{a: 'aa'}

```

## instanceof实现

```js
// 代码
function _instanceof(L, R) {
  if (typeof L !== 'object') return false

  L = L.__proto__
  R = R.prototype

  while (true) {
    if (L === null) return false

    if (L === R) return true

    L = L.__proto__
  }
}

// 测试
function Car(make, model, year) {
  this.make = make
  this.model = model
  this.year = year
}
const auto = new Car('Honda', 'Accord', 1998)
console.log(_instanceof(auto, Car)) // expected output: true

// 测试
console.log(_instanceof([1, 2], Array)) // expected output: true
console.log(_instanceof({ a: 1 }, Object)) // expected output: true
```

## 参考链接

* [new,call,apply,bind方法的实现原理](https://segmentfault.com/a/1190000021905571)
* [new关键字源码](https://juejin.cn/post/7044056237998080037)
* [js 函数组合 compose函数](https://segmentfault.com/a/1190000039904177)