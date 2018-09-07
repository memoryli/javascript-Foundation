#Javascript总结
#内置类型
JS 中分为七种内置类型，七种内置类型又分为两大类型：基本类型和对象（Object）。<br>

基本类型有六种： null，undefined，boolean，number，string，symbol。<br>

其中 JS 的数字类型是浮点类型的，没有整型。并且浮点类型基于 IEEE 754标准实现，在使用中会遇到某些 Bug。NaN 也属于 number 类型，并且 NaN 不等于自身。<br>  
对于基本类型来说，如果使用字面量的方式，那么这个变量只是个字面量，只有在必要的时候才会转换为对应的类型<br>  
```let a = 111 // 这只是字面量，不是 number 类型 ```<br>
```a.toString() // 使用时候才会转换为对象类型 ```<br>
对象（Object）是引用类型，在使用过程中会遇到浅拷贝和深拷贝的问题<br>
```
let a = { name: 'FE' }
let b = a
b.name = 'EF'<br>  
console.log(a.name) // EF<br>  
Typeof<br>  
typeof 对于基本类型，除了 null 都可以显示正确的类型<br>  
typeof 1 // 'number'<br>  
typeof '1' // 'string'<br>  
typeof undefined // 'undefined'<br>  
typeof true // 'boolean'<br>  
typeof Symbol() // 'symbol'<br>  
typeof b // b 没有声明，但是还会显示 undefined<br>  <br>  
typeof 对于对象，除了函数都会显示 object<br>  
typeof [] // 'object'
typeof {} // 'object' 
typeof console.log // 'function' 
```
对于 null 来说，虽然它是基本类型，但是会显示 object，这是一个存在很久了的 Bug<br>  
```typeof null // 'object'  ```<br>
PS：为什么会出现这种情况呢？因为在 JS 的最初版本中，使用的是 32 位系统，为了性能考虑使用低位存储了变量的类型信息，000 开头代表是对象，然而 null 表示为全零，所以将它错误的判断为 object 。虽然现在的内部类型判断代码已经改变了，但是对于这个 Bug 却是一直流传下来。<br>  
如果我们想获得一个变量的正确类型，可以通过 Object.prototype.toString.call(xx)。这样我们就可以获得类似 [Object Type] 的字符串。<br> 
```
let a
```
<br>  
// 我们也可以这样判断 undefined<br>  
```
a === undefined
```
<br>  
// 但是 undefined 保留字，能够在低版本浏览器被赋值<br>  <br>  
```let undefined = 1```<br>  
// 这样判断就会出错<br>  
// 所以可以用下面的方式来判断，并且代码量更少<br>  <br>  
// 因为 void 后面随便跟上一个组成表达式<br>  
// 返回就是 undefined
```
a === void 0
```
类型转换<br> 
### 转Boolean
在条件判断时，除了 undefined， null， false， NaN， ''， 0， -0，其他所有值都转为 true，包括所有对象<br> 
#### 对象转基本类型
对象在转换基本类型时，首先会调用 valueOf 然后调用 toString。并且这两个方法你是可以重写的。<br> 
### 四则运算符
只有当加法运算时，其中一方是字符串类型，就会把另一个也转为字符串类型。其他运算只要其中一方是数字，那么另一方就转为数字。并且加法运算会触发三种类型转换：将值转换为原始值，转换为数字，转换为字符串。<br> 
``` 
	1 + '1' // '11'
	2 * '2' // 4
	[1, 2] + [2, 1] // '1,22,1'
	// [1, 2].toString() -> '1,2'
	// [2, 1].toString() -> '2,1'
	// '1,2' + '2,1' = '1,22,1'
```
对于加号需要注意这个表达式 'a' + + 'b'<br> 
'a' + + 'b' // -> "aNaN"
// 因为 + 'b' -> NaN
// 你也许在一些代码中看到过 + '1' -> 1
== 操作符

上图中的 toPrimitive 就是对象转基本类型。
一般推荐使用 === 判断两个值，但是你如果想知道一个值是不是 null ，你可以通过 xx == null来比较。
这里来解析一道题目 [] == ![] // -> true ，下面是这个表达式为何为 true 的步骤
// [] 转成 true，然后取反变成 false
[] == false
// 根据第 8 条得出
[] == ToNumber(false)
[] == 0
// 根据第 10 条得出
ToPrimitive([]) == 0
// [].toString() -> ''
'' == 0
// 根据第 6 条得出
0 == 0 // -> true
### 原型
每个函数都有 prototype 属性，除了 Function.prototype.bind()，该属性指向原型。<br> 
每个对象都有 __proto__ 属性，指向了创建该对象的构造函数的原型。其实这个属性指向了 [[prototype]]，但是 [[prototype]] 是内部属性，我们并不能访问到，所以使用 _proto_ 来访问。<br> 
对象可以通过 __proto__ 来寻找不属于该对象的属性，__proto__ 将对象连接起来组成了原型链。<br> 
#### prototype
首先来介绍下 prototype 属性。这是一个显式原型属性，只有函数才拥有该属性。基本上所有函数都有这个属性，但是也有一个例外
```let fun = Function.prototype.bind()```
如果你以上述方法创建一个函数，那么可以发现这个函数是不具有 prototype 属性的。<br>
#### prototype 如何产生的
当我们声明一个函数时，这个属性就被自动创建了。
```function Foo() {}```
并且这个属性的值是一个对象（也就是原型），只有一个属性 constructor<br>

constructor 对应着构造函数，也就是 Foo。<br>
#### constructor
constructor 是一个公有且不可枚举的属性。一旦我们改变了函数的 prototype ，那么新对象就没有这个属性了（当然可以通过原型链取到 constructor）。<br>
那么你肯定也有一个疑问，这个属性到底有什么用呢？其实这个属性可以说是一个历史遗留问题，在大部分情况下是没用的，在我的理解里，我认为他有两个作用：<br>
让实例对象知道是什么函数构造了它<br>
如果想给某些类库中的构造函数增加一些自定义的方法，就可以通过 xx.constructor.method 来扩展<br>
#### _proto_
这是每个对象都有的隐式原型属性，指向了创建该对象的构造函数的原型。其实这个属性指向了 [[prototype]]，但是 [[prototype]] 是内部属性，我们并不能访问到，所以使用 _proto_ 来访问。<br>
#### instanceof
instanceof 可以正确的判断对象的类型，因为内部机制是通过判断对象的原型链中是不是能找到类型的 prototype
我们也可以试着实现一下 instanceof
function instanceof(left, right) {
    // 获得类型的原型
    let prototype = right.prototype
    // 获得对象的原型
    left = left.__proto__
    // 判断对象的类型是否等于类型的原型
    while (true) {
    	if (left === null)
    		return false
    	if (prototype === left)
    		return true
    	left = left.__proto__
    }
}
this

this 是很多人会混淆的概念，但是其实他一点都不难，你只需要记住几个规则就可以了。<br>
```
function foo() {
	console.log(this.a)
}
var a = 2
foo()

var obj = {
	a: 2,
	foo: foo
}
obj.foo()
```
// 以上两者情况 `this` 只依赖于调用函数前的对象，优先级是第二个情况大于第一个情况<br>
// 以下情况是优先级最高的，`this` 只会绑定在 `c` 上，不会被任何方式修改 `this` 指向<br>
```
var c = new foo()
c.a = 3
console.log(c.a)
```
// 还有种就是利用 call，apply，bind 改变 this，这个优先级仅次于 new <br>
下面让我们看看箭头函数中的 this <br>
```
function a() {
    return () => {
        return () => {
        	console.log(this)
        }
    }
}
console.log(a()()())
```
箭头函数其实是没有 this 的，这个函数中的 this 只取决于他外面的第一个不是箭头函数的函数的 this。在这个例子中，因为调用 a 符合前面代码中的第一个情况，所以 this 是 window。并且 this 一旦绑定了上下文，就不会被任何代码改变。<br>
### 执行上下文
当执行 JS 代码时，会产生三种执行上下文<br>
全局执行上下文<br>
函数执行上下文<br>
eval 执行上下文<br>
每个执行上下文中都有三个重要的属性<br>
变量对象（VO），包含变量、函数声明和函数的形参，该属性只能在全局上下文中访问<br>
作用域链（JS 采用词法作用域，也就是说变量的作用域是在定义时就决定了）<br>
```
this
var a = 10
function foo(i) {
  var b = 20
}
foo()
```
对于上述代码，执行栈中有两个上下文：全局上下文和函数 foo 上下文。<br>
对于作用域链，可以把它理解成包含自身变量对象和上级变量对象的列表，通过 [[Scope]] 属性查找上级变量<br>
接下来让我们看一个老生常谈的例子，var
b() // call b
console.log(a) // undefined

var a = 'Hello world'

function b() {
	console.log('call b')
}
想必以上的输出大家肯定都已经明白了，这是因为函数和变量提升的原因。通常提升的解释是说将声明的代码移动到了顶部，这其实没有什么错误，便于大家理解。但是更准确的解释应该是：在生成执行上下文时，会有两个阶段。第一个阶段是创建的阶段（具体步骤是创建 VO），JS 解释器会找出需要提升的变量和函数，并且给他们提前在内存中开辟好空间，函数的话会将整个函数存入内存中，变量只声明并且赋值为 undefined，所以在第二个阶段，也就是代码执行阶段，我们可以直接提前使用。
在提升的过程中，相同的函数会覆盖上一个函数，并且函数优先于变量提升。
b() // call b second

function b() {
	console.log('call b fist')
}
function b() {
	console.log('call b second')
}
var b = 'Hello world'
var 会产生很多错误，所以在 ES6中引入了 let。let 不能在声明前使用，但是这并不是常说的 let 不会提升，let 提升了声明但没有赋值，因为临时死区导致了并不能在声明前使用。
var foo = 1
(function foo() {
    foo = 10
    console.log(foo)
}()) // -> ƒ foo() { foo = 10 ; console.log(foo) }
因为当 JS 解释器在遇到非匿名的立即执行函数时，会创建一个辅助的特定对象，然后将函数名称作为这个对象的属性，因此函数内部才可以访问到 foo，但是这又个值是只读的，所以对它的赋值并不生效，所以打印的结果还是这个函数，并且外部的值也没有发生更改。
### 闭包
闭包的定义很简单：函数 A 返回了一个函数 B，并且函数 B 中使用了函数 A 的变量，函数 B 就被称为闭包。<br>
```
function A() {
  let a = 1
  function B() {
      console.log(a)
  }
  return B
}
```
你是否会疑惑，为什么函数 A 已经弹出调用栈了，为什么函数 B 还能引用到函数 A 中的变量。因为函数 A 中的变量这时候是存储在堆上的。现在的 JS 引擎可以通过逃逸分析辨别出哪些变量需要存储在堆上，哪些需要存储在栈上。<br>
经典面试题，循环中使用闭包解决 var 定义函数的问题<br>
```
for ( var i=1; i<=5; i++) {
	setTimeout( function timer() {
		console.log( i );
	}, i*1000 );
}
```
首先因为 setTimeout 是个异步函数，所有会先把循环全部执行完毕，这时候 i 就是 6 了，所以会输出一堆 6。<br>
解决办法两种，第一种使用闭包<br>
```
for (var i = 1; i <= 5; i++) {
  (function(j) {
    setTimeout(function timer() {
      console.log(j);
    }, j * 1000);
  })(i);
}
```
第二种就是使用 setTimeout 的第三个参数<br>
```
for ( var i=1; i<=5; i++) {
	setTimeout( function timer(j) {
		console.log( j );
	}, i*1000, i);
}

第三种就是使用 let 定义 i 了
for ( let i=1; i<=5; i++) {
	setTimeout( function timer() {
		console.log( i );
	}, i*1000 );
}
```
因为对于 let 来说，他会创建一个块级作用域，相当于<br>
```
{ // 形成块级作用域
  let i = 0
  {
    let ii = i
    setTimeout( function timer() {
        console.log( i );
    }, i*1000 );
  }
  i++
  {
    let ii = i
  }
  i++
  {
    let ii = i
  }
  ...
}
```
### 深浅拷贝
```
let a = {
    age: 1
}
let b = a
a.age = 2
console.log(b.age) // 2
```
从上述例子中我们可以发现，如果给一个变量赋值一个对象，那么两者的值会是同一个引用，其中一方改变，另一方也会相应改变。<br>
通常在开发中我们不希望出现这样的问题，我们可以使用浅拷贝来解决这个问题<br>
首先可以通过 Object.assign 来解决这个问题。<br>
```
let a = {
    age: 1
}
let b = Object.assign({}, a)
a.age = 2
console.log(b.age) // 1
```
当然我们也可以通过展开运算符（…）来解决<br>
```
let a = {
    age: 1
}
let b = {...a}
a.age = 2
console.log(b.age) // 1
```
通常浅拷贝就能解决大部分问题了，但是当我们遇到如下情况就需要使用到深拷贝了<br>
```
let a = {
    age: 1,
    jobs: {
        first: 'FE'
    }
}
let b = {...a}
a.jobs.first = 'native'
console.log(b.jobs.first) // native
```
浅拷贝只解决了第一层的问题，如果接下去的值中还有对象的话，那么就又回到刚开始的话题了，两者享有相同的引用。要解决这个问题，我们需要引入深拷贝。<br>
这个问题通常可以通过 JSON.parse(JSON.stringify(object)) 来解决<br>
```
let a = {
    age: 1,
    jobs: {
        first: 'FE'
    }
}
let b = JSON.parse(JSON.stringify(a))
a.jobs.first = 'native'
console.log(b.jobs.first) // FE
```
但是该方法也是有局限性的：<br>
会忽略 undefined<br>
不能序列化函数<br>
不能解决循环引用的对象<br>
```
let obj = {
  a: 1,
  b: {
    c: 2,
    d: 3,
  },
}
obj.c = obj.b
obj.e = obj.a
obj.b.c = obj.c
obj.b.d = obj.b
obj.b.e = obj.b.c
let newObj = JSON.parse(JSON.stringify(obj))
console.log(newObj)
```
如果你有这么一个循环引用对象，你会发现你不能通过该方法深拷贝<br>

在遇到函数或者 undefined 的时候，该对象也不能正常的序列化<br>
```
let a = {
    age: undefined,
    jobs: function() {},
    name: 'yck'
}
let b = JSON.parse(JSON.stringify(a))
console.log(b) // {name: "yck"}
```
你会发现在上述情况中，该方法会忽略掉函数和 undefined<br>
但是在通常情况下，复杂数据都是可以序列化的，所以这个函数可以解决大部分问题，并且该函数是内置函数中处理深拷贝性能最快的。当然如果你的数据中含有以上三种情况下，可以使用 lodash 的深拷贝函数。<br>
如果你所需拷贝的对象含有内置类型并且不包含函数，可以使用 MessageChannel<br>
```
function structuralClone(obj) {
  return new Promise(resolve => {
    const {port1, port2} = new MessageChannel();
    port2.onmessage = ev => resolve(ev.data);
    port1.postMessage(obj);
  });
}

var obj = {a: 1, b: {
    c: b
}}
```
// 注意该方法是异步的<br>
// 可以处理 undefined 和循环引用对象<br>
```
const clone = await structuralClone(obj);
```
### 模块化
在有 Babel 的情况下，我们可以直接使用 ES6 的模块化<br>
```
// file a.js
export function a() {}
export function b() {}
// file b.js
export default function() {}

import {a, b} from './a.js'
import XXX from './b.js'
```
#### CommonJS
CommonJs 是 Node 独有的规范，浏览器中使用就需要用到 Browserify 解析了<br>
// a.js
module.exports = {
    a: 1
}
// or
exports.a = 1

// b.js
var module = require('./a.js')
module.a // -> log 1
在上述代码中，module.exports 和 exports 很容易混淆，让我们来看看大致内部实现
var module = require('./a.js')
module.a
// 这里其实就是包装了一层立即执行函数，这样就不会污染全局变量了，
// 重要的是 module 这里，module 是 Node 独有的一个变量
module.exports = {
    a: 1
}
// 基本实现
var module = {
  exports: {} // exports 就是个空对象
}
// 这个是为什么 exports 和 module.exports 用法相似的原因
var exports = module.exports
var load = function (module) {
    // 导出的东西
    var a = 1
    module.exports = a
    return module.exports
};
对于 CommonJS 和 ES6 中的模块化的两者区别是：
前者支持动态导入，也就是 require(${path}/xx.js)，后者目前不支持，但是已有提案
前者是同步导入，因为用于服务端，文件都在本地，同步导入即使卡住主线程影响也不大。而后者是异步导入，因为用于浏览器，需要下载文件，如果也采用导入会对渲染有很大影响
前者在导出时都是值拷贝，就算导出的值变了，导入的值也不会改变，所以如果想更新值，必须重新导入一次。但是后者采用实时绑定的方式，导入导出的值都指向同一个内存地址，所以导入值会跟随导出值变化
后者会编译成 require/exports 来执行的
AMD
AMD 是由 RequireJS 提出的
// AMD
define(['./a', './b'], function(a, b) {
    a.do()
    b.do()
})
define(function(require, exports, module) {   
    var a = require('./a')  
    a.doSomething()   
    var b = require('./b')
    b.doSomething()
})
防抖
你是否在日常开发中遇到一个问题，在滚动事件中需要做个复杂计算或者实现一个按钮的防二次点击操作。
这些需求都可以通过函数防抖动来实现。尤其是第一个需求，如果在频繁的事件回调中做复杂计算，很有可能导致页面卡顿，不如将多次计算合并为一次计算，只在一个精确点做操作。因为防抖动的轮子很多，这里也不重新自己造个轮子了，直接使用 underscore 的源码来解释防抖动。
```
/**
 * underscore 防抖函数，返回函数连续调用时，空闲时间必须大于或等于 wait，func 才会执行
 *
 * @param  {function} func        回调函数
 * @param  {number}   wait        表示时间窗口的间隔
 * @param  {boolean}  immediate   设置为ture时，是否立即调用函数
 * @return {function}             返回客户调用函数
 */
_.debounce = function(func, wait, immediate) {
    var timeout, args, context, timestamp, result;

    var later = function() {
      // 现在和上一次时间戳比较
      var last = _.now() - timestamp;
      // 如果当前间隔时间少于设定时间且大于0就重新设置定时器
      if (last < wait && last >= 0) {
        timeout = setTimeout(later, wait - last);
      } else {
        // 否则的话就是时间到了执行回调函数
        timeout = null;
        if (!immediate) {
          result = func.apply(context, args);
          if (!timeout) context = args = null;
        }
      }
    };

    return function() {
      context = this;
      args = arguments;
      // 获得时间戳
      timestamp = _.now();
      // 如果定时器不存在且立即执行函数
      var callNow = immediate && !timeout;
      // 如果定时器不存在就创建一个
      if (!timeout) timeout = setTimeout(later, wait);
      if (callNow) {
        // 如果需要立即执行函数的话 通过 apply 执行
        result = func.apply(context, args);
        context = args = null;
      }

      return result;
    };
  };
  ```
整体函数实现的不难，总结一下。<br>
对于按钮防点击来说的实现：一旦我开始一个定时器，只要我定时器还在，不管你怎么点击都不会执行回调函数。一旦定时器结束并设置为 null，就可以再次点击了。<br>
对于延时执行函数来说的实现：每次调用防抖动函数都会判断本次调用和之前的时间间隔，如果小于需要的时间间隔，就会重新创建一个定时器，并且定时器的延时为设定时间减去之前的时间间隔。一旦时间到了，就会执行相应的回调函数。<br>
作用是在短时间内多次触发同一个函数，只执行最后一次，或者只在开始时执行<br>

## 节流
防抖动和节流本质是不一样的。防抖动是将多次执行变为最后一次执行，节流是将多次执行变成每隔一段时间执行。<br>
```
/**
 * underscore 节流函数，返回函数连续调用时，func 执行频率限定为 次 / wait
 *
 * @param  {function}   func      回调函数
 * @param  {number}     wait      表示时间窗口的间隔
 * @param  {object}     options   如果想忽略开始函数的的调用，传入{leading: false}。
 *                                如果想忽略结尾函数的调用，传入{trailing: false}
 *                                两者不能共存，否则函数不能执行
 * @return {function}             返回客户调用函数   
 */
_.throttle = function(func, wait, options) {
    var context, args, result;
    var timeout = null;
    // 之前的时间戳
    var previous = 0;
    // 如果 options 没传则设为空对象
    if (!options) options = {};
    // 定时器回调函数
    var later = function() {
      // 如果设置了 leading，就将 previous 设为 0
      // 用于下面函数的第一个 if 判断
      previous = options.leading === false ? 0 : _.now();
      // 置空一是为了防止内存泄漏，二是为了下面的定时器判断
      timeout = null;
      result = func.apply(context, args);
      if (!timeout) context = args = null;
    };
    return function() {
      // 获得当前时间戳
      var now = _.now();
      // 首次进入前者肯定为 true
	  // 如果需要第一次不执行函数
	  // 就将上次时间戳设为当前的
      // 这样在接下来计算 remaining 的值时会大于0
      if (!previous && options.leading === false) previous = now;
      // 计算剩余时间
      var remaining = wait - (now - previous);
      context = this;
      args = arguments;
      // 如果当前调用已经大于上次调用时间 + wait
      // 或者用户手动调了时间
 	  // 如果设置了 trailing，只会进入这个条件
	  // 如果没有设置 leading，那么第一次会进入这个条件
	  // 还有一点，你可能会觉得开启了定时器那么应该不会进入这个 if 条件了
	  // 其实还是会进入的，因为定时器的延时
	  // 并不是准确的时间，很可能你设置了2秒
	  // 但是他需要2.2秒才触发，这时候就会进入这个条件
      if (remaining <= 0 || remaining > wait) {
        // 如果存在定时器就清理掉否则会调用二次回调
        if (timeout) {
          clearTimeout(timeout);
          timeout = null;
        }
        previous = now;
        result = func.apply(context, args);
        if (!timeout) context = args = null;
      } else if (!timeout && options.trailing !== false) {
        // 判断是否设置了定时器和 trailing
	    // 没有的话就开启一个定时器
        // 并且不能不能同时设置 leading 和 trailing
        timeout = setTimeout(later, remaining);
      }
      return result;
    };
  };
  ```
## 继承
在 ES5 中，我们可以使用如下方式解决继承的问题<br>
```
function Super() {}
Super.prototype.getNumber = function() {
  return 1
}
function Sub() {}
let s = new Sub()
Sub.prototype = Object.create(Super.prototype, {
  constructor: {
    value: Sub,
    enumerable: false,
    writable: true,
    configurable: true
  }
})
``` 
<br>
在 ES6 中，我们可以通过 class 语法轻松解决这个问题<br>
```
class MyDate extends Date {
  test() {
    return this.getTime()
  }
}
let myDate = new MyDate()
myDate.test()
```
### call, apply, bind 区别
call 和 apply 都是为了解决改变 this 的指向。作用都是相同的，只是传参的方式不同。<br>
除了第一个参数外，call 可以接收一个参数列表，apply 只接受一个参数数组。<br>
```
let a = {
    value: 1
}
function getValue(name, age) {
    console.log(name)
    console.log(age)
    console.log(this.value)
}
getValue.call(a, 'yck', '24')
getValue.apply(a, ['yck', '24'])
```
### 模拟实现 call 和 apply<br>

可以从以下几点来考虑如何实现<br>
不传入第一个参数，那么默认为 window<br>
改变了 this 指向，让新的对象可以执行该函数。那么思路是否可以变成给新的对象添加一个函数，然后在执行完以后删除？<br>
```
Function.prototype.myCall = function (context) {
  var context = context || window
  // 给 context 添加一个属性
  // getValue.call(a, 'yck', '24') => a.fn = getValue
  context.fn = this
  // 将 context 后面的参数取出来
  var args = [...arguments].slice(1)
  // getValue.call(a, 'yck', '24') => a.fn('yck', '24')
  var result = context.fn(...args)
  // 删除 fn
  delete context.fn
  return result
}
以上就是 call 的思路，apply 的实现也类似
Function.prototype.myApply = function (context) {
  var context = context || window
  context.fn = this

  var result
  // 需要判断是否存储第二个参数
  // 如果存在，就将第二个参数展开
  if (arguments[1]) {
    result = context.fn(...arguments[1])
  } else {
    result = context.fn()
  }

  delete context.fn
  return result
}
```
bind 和其他两个方法作用也是一致的，只是该方法会返回一个函数。并且我们可以通过 bind 实现柯里化。<br>
同样的，也来模拟实现下 bind
Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  var _this = this
  var args = [...arguments].slice(1)
  // 返回一个函数
  return function F() {
    // 因为返回了一个函数，我们可以 new F()，所以需要判断
    if (this instanceof F) {
      return new _this(...args, ...arguments)
    }
    return _this.apply(context, args.concat(...arguments))
  }
}
Promise 实现
Promise 是 ES6 新增的语法，解决了回调地狱的问题
可以把 Promise 看成一个状态机。初始是 pending 状态，可以通过函数 resolve 和 reject ，将状态转变为 resolved 或者 rejected 状态，状态一旦改变就不能再次变化。
then 函数会返回一个 Promise 实例，并且该返回值是一个新的实例而不是之前的实例。因为 Promise 规范规定除了 pending 状态，其他状态是不可以改变的，如果返回的是一个相同实例的话，多个 then 调用就失去意义了。
Generator 实现
Generator 是 ES6 中新增的语法，和 Promise 一样，都可以用来异步编程
// 使用 * 表示这是一个 Generator 函数
// 内部可以通过 yield 暂停代码
// 通过调用 next 恢复执行
function* test() {
  let a = 1 + 2;
  yield 2;
  yield 3;
}
let b = test();
console.log(b.next()); // >  { value: 2, done: false }
console.log(b.next()); // >  { value: 3, done: false }
console.log(b.next()); // >  { value: undefined, done: true }
从以上代码可以发现，加上 * 的函数执行后拥有了 next 函数，也就是说函数执行后返回了一个对象。每次调用 next 函数可以继续执行被暂停的代码。
以下是 Generator 函数的简单实现
// cb 也就是编译过的 test 函数
function generator(cb) {
  return (function() {
    var object = {
      next: 0,
      stop: function() {}
    };

    return {
      next: function() {
        var ret = cb(object);
        if (ret === undefined) return { value: undefined, done: true };
        return {
          value: ret,
          done: false
        };
      }
    };
  })();
}
// 如果你使用 babel 编译后可以发现 test 函数变成了这样
function test() {
  var a;
  return generator(function(_context) {
    while (1) {
      switch ((_context.prev = _context.next)) {
        // 可以发现通过 yield 将代码分割成几块
        // 每次执行 next 函数就执行一块代码
        // 并且表明下次需要执行哪块代码
        case 0:
          a = 1 + 2;
          _context.next = 4;
          return 2;
        case 4:
          _context.next = 6;
          return 3;
		// 执行完毕
        case 6:
        case "end":
          return _context.stop();
      }
    }
  });
}
Map、FlatMap 和 Reduce
Map 作用是生成一个新数组，遍历原数组，将每个元素拿出来做一些变换然后 append 到新的数组中。
[1, 2, 3].map((v) => v + 1)
// -> [2, 3, 4]
Map 有三个参数，分别是当前索引元素，索引，原数组
['1','2','3'].map(parseInt)
//  parseInt('1', 0) -> 1
//  parseInt('2', 1) -> NaN
//  parseInt('3', 2) -> NaN
FlapMap 和 map 的作用几乎是相同的，但是对于多维数组来说，会将原数组降维。可以将 FlapMap看成是 map + flatten ，目前该函数在浏览器中还不支持。
[1, [2], 3].flatMap((v) => v + 1)
// -> [2, 3, 4]
Reduce 作用是数组中的值组合起来，最终得到一个值
function a() {
    console.log(1);
}

function b() {
    console.log(2);
}

[a, b].reduce((a, b) => a(b()))
// -> 2 1
async 和 await
一个函数如果加上 async ，那么该函数就会返回一个 Promise
async function test() {
  return "1";
}
console.log(test()); // -> Promise {<resolved>: "1"}
可以把 async 看成将函数返回值使用 Promise.resolve() 包裹了下
await 只能在 async 函数中使用
function sleep() {
  return new Promise(resolve => {
    setTimeout(() => {
      console.log('finish')
      resolve("sleep");
    }, 2000);
  });
}
async function test() {
  let value = await sleep();
  console.log("object");
}
test()
上面代码会先打印 finish 然后再打印 object 。因为 await 会等待 sleep 函数 resolve ，所以即使后面是同步代码，也不会先去执行同步代码再来执行异步代码。
async 和 await 相比直接使用 Promise 来说，优势在于处理 then 的调用链，能够更清晰准确的写出代码。缺点在于滥用 await 可能会导致性能问题，因为 await 会阻塞代码，也许之后的异步代码并不依赖于前者，但仍然需要等待前者完成，导致代码失去了并发性。
下面来看一个使用 await 的代码。
var a = 0
var b = async () => {
  a = a + await 10
  console.log('2', a) // -> '2' 10
  a = (await 10) + a
  console.log('3', a) // -> '3' 20
}
b()
a++
console.log('1', a) // -> '1' 1
对于以上代码你可能会有疑惑，这里说明下原理
首先函数 b 先执行，在执行到 await 10 之前变量 a 还是 0，因为在 await 内部实现了 generators ，generators 会保留堆栈中东西，所以这时候 a = 0 被保存了下来
因为 await 是异步操作，所以会先执行 console.log('1', a)
这时候同步代码执行完毕，开始执行异步代码，将保存下来的值拿出来使用，这时候 a = 10
然后后面就是常规执行代码了
Proxy
Proxy 是 ES6 中新增的功能，可以用来自定义对象中的操作
let onWatch = (obj, setBind, getLogger) => {
  let handler = {
    get(target, property, receiver) {
      getLogger(target, property)
      return Reflect.get(target, property, receiver);
    },
    set(target, property, value, receiver) {
      setBind(value);
      return Reflect.set(target, property, value);
    }
  };
  return new Proxy(obj, handler);
};

let obj = { a: 1 }
let value
let p = onWatch(obj, (v) => {
  value = v
}, (target, property) => {
  console.log(`Get '${property}' = ${target[property]}`);
})
p.a = 2 // bind `value` to `2`
p.a // -> Get 'a' = 2
为什么 0.1 + 0.2 != 0.3
解决方案parseFloat((0.1 + 0.2).toFixed(10))
正则表达式
元字符 作用
. 匹配任意字符除了换行符和回车符
[] 匹配方括号内的任意字符。比如 [0-9] 就可以用来匹配任意数字
^ ^9，这样使用代表匹配以 9 开头。[^9]，这样使用代表不匹配方括号内除了 9 的字符
{1, 2} 匹配 1 到 2 位字符
(yck) 只匹配和 yck 相同字符串
| 匹配 | 前后任意字符
\ 转义
* 只匹配出现 -1 次以上 * 前的字符
+ 只匹配出现 0 次以上 + 前的字符
? ? 之前字符可选

修饰语 作用
i 忽略大小写
g 全局搜索
m 多行

简写 作用
\w 匹配字母数字或下划线
\W 和上面相反
\s 匹配任意的空白符
\S 和上面相反
\d 匹配数字
\D 和上面相反
\b 匹配单词的开始或结束
\B 和上面相反
V8 下的垃圾回收机制
V8 实现了准确式 GC，GC 算法采用了分代式垃圾回收机制。因此，V8 将内存（堆）分为新生代和老生代两部分。
新生代算法
新生代中的对象一般存活时间较短，使用 Scavenge GC 算法。
在新生代空间中，内存空间分为两部分，分别为 From 空间和 To 空间。在这两个空间中，必定有一个空间是使用的，另一个空间是空闲的。新分配的对象会被放入 From 空间中，当 From 空间被占满时，新生代 GC 就会启动了。算法会检查 From 空间中存活的对象并复制到 To 空间中，如果有失活的对象就会销毁。当复制完成后将 From 空间和 To 空间互换，这样 GC 就结束了。
老生代算法
老生代中的对象一般存活时间较长且数量也多，使用了两个算法，分别是标记清除算法和标记压缩算法。
在讲算法前，先来说下什么情况下对象会出现在老生代空间中：
新生代中的对象是否已经经历过一次 Scavenge 算法，如果经历过的话，会将对象从新生代空间移到老生代空间中。
To 空间的对象占比大小超过 25 %。在这种情况下，为了不影响到内存分配，会将对象从新生代空间移到老生代空间中。
老生代中的空间很复杂，有如下几个空间
浏览器
事件机制
事件触发三阶段
事件触发有三个阶段
document 往事件触发处传播，遇到注册的捕获事件会触发
传播到事件触发处时触发注册的事件
从事件触发处往 document 传播，遇到注册的冒泡事件会触发
事件触发一般来说会按照上面的顺序进行，但是也有特例，如果给一个目标节点同时注册冒泡和捕获事件，事件触发会按照注册的顺序执行。
// 以下会先打印冒泡然后是捕获
node.addEventListener('click',(event) =>{
	console.log('冒泡')
},false);
node.addEventListener('click',(event) =>{
	console.log('捕获 ')
},true)
注册事件
通常我们使用 addEventListener 注册事件，该函数的第三个参数可以是布尔值，也可以是对象。
一般来说，我们只希望事件只触发在目标上，这时候可以使用 stopPropagation 来阻止事件的进一步传播。通常我们认为 stopPropagation 是用来阻止事件冒泡的，其实该函数也可以阻止捕获事件。stopImmediatePropagation 同样也能实现阻止事件，但是还能阻止该事件目标执行别的注册事件。
事件代理
如果一个节点中的子节点是动态生成的，那么子节点需要注册事件的话应该注册在父节点上
<ul id="ul">
	<li>1</li>
    <li>2</li>
	<li>3</li>
	<li>4</li>
	<li>5</li>
</ul>
<script>
	let ul = document.querySelector('##ul')
	ul.addEventListener('click', (event) => {
		console.log(event.target);
	})
</script>
事件代理的方式相对于直接给目标注册事件来说，有以下优点
节省内存
不需要给子节点注销事件
跨域
因为浏览器出于安全考虑，有同源策略。也就是说，如果协议、域名或者端口有一个不同就是跨域，Ajax 请求会失败。
我们可以通过以下几种常用方法解决跨域的问题
JSONP
JSONP 的原理很简单，就是利用 <script> 标签没有跨域限制的漏洞。通过 <script> 标签指向一个需要访问的地址并提供一个回调函数来接收数据当需要通讯时。
<script src="http://domain/api?param1=a&param2=b&callback=jsonp"></script>
<script>
    function jsonp(data) {
    	console.log(data)
	}
</script> 
SONP 使用简单且兼容性不错，但是只限于 get 请求。
在开发中可能会遇到多个 JSONP 请求的回调函数名是相同的，这时候就需要自己封装一个 JSONP，以下是简单实现
function jsonp(url, jsonpCallback, success) {
  let script = document.createElement("script");
  script.src = url;
  script.async = true;
  script.type = "text/javascript";
  window[jsonpCallback] = function(data) {
    success & success(data);
  };
  document.body.appendChild(script);
}
jsonp(
  "http://xxx",
  "callback",
  function(value) {
    console.log(value);
  }
);
CORS

CORS需要浏览器和后端同时支持。IE 8 和 9 需要通过 XDomainRequest 来实现。
浏览器会自动进行 CORS 通信，实现CORS通信的关键是后端。只要后端实现了 CORS，就实现了跨域。
服务端设置 Access-Control-Allow-Origin 就可以开启 CORS。 该属性表示哪些域名可以访问资源，如果设置通配符则表示所有网站都可以访问资源。
document.domain

该方式只能用于二级域名相同的情况下，比如 a.test.com 和 b.test.com 适用于该方式。
只需要给页面添加 document.domain = 'test.com' 表示二级域名都相同就可以实现跨域
Event loop
众所周知 JS 是门非阻塞单线程语言，因为在最初 JS 就是为了和浏览器交互而诞生的。如果 JS 是门多线程的语言话，我们在多个线程中处理 DOM 就可能会发生问题（一个线程中新加节点，另一个线程中删除节点），当然可以引入读写锁解决这个问题。
S 在执行的过程中会产生执行环境，这些执行环境会被顺序的加入到执行栈中。如果遇到异步的代码，会被挂起并加入到 Task（有多种 task） 队列中。一旦执行栈为空，Event Loop 就会从 Task 队列中拿出需要执行的代码并放入执行栈中执行，所以本质上来说 JS 中的异步还是同步行为。
console.log('script start');

setTimeout(function() {
  console.log('setTimeout');
}, 0);

console.log('script end');
以上代码虽然 setTimeout 延时为 0，其实还是异步。这是因为 HTML5 标准规定这个函数第二个参数不得小于 4 毫秒，不足会自动增加。所以 setTimeout 还是会在 script end 之后打印。
不同的任务源会被分配到不同的 Task 队列中，任务源可以分为 微任务（microtask） 和 宏任务（macrotask）。在 ES6 规范中，microtask 称为 jobs，macrotask 称为 task。
console.log('script start');

setTimeout(function() {
  console.log('setTimeout');
}, 0);

new Promise((resolve) => {
    console.log('Promise')
    resolve()
}).then(function() {
  console.log('promise1');
}).then(function() {
  console.log('promise2');
});

console.log('script end');
// script start => Promise => script end => promise1 => promise2 => setTimeout
以上代码虽然 setTimeout 写在 Promise 之前，但是因为 Promise 属于微任务而 setTimeout 属于宏任务，所以会有以上的打印。
微任务包括 process.nextTick ，promise ，Object.observe ，MutationObserver
宏任务包括 script ， setTimeout ，setInterval ，setImmediate ，I/O ，UI rendering
很多人有个误区，认为微任务快于宏任务，其实是错误的。因为宏任务中包括了 script ，浏览器会先执行一个宏任务，接下来有异步代码的话就先执行微任务。
所以正确的一次 Event loop 顺序是这样的
1、执行同步代码，这属于宏任务
2、执行栈为空，查询是否有微任务需要执行
3、执行所有微任务
4、必要的话渲染 UI
5、然后开始下一轮 Event loop，执行宏任务中的异步代码
Node 中的 Event loop
Node 中的 Event loop 和浏览器中的不相同。
Node 的 Event loop 分为6个阶段，它们会按照顺序反复运行
┌───────────────────────┐
┌─>│        timers         │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     I/O callbacks     │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     idle, prepare     │
│  └──────────┬────────────┘      ┌───────────────┐
│  ┌──────────┴────────────┐      │   incoming:   │
│  │         poll          │<──connections───     │
│  └──────────┬────────────┘      │   data, etc.  │
│  ┌──────────┴────────────┐      └───────────────┘
│  │        check          │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
└──┤    close callbacks    │
   └───────────────────────┘
timer
timers 阶段会执行 setTimeout 和 setInterval
一个 timer 指定的时间并不是准确时间，而是在达到这个时间后尽快执行回调，可能会因为系统正在执行别的事务而延迟。
I/O
I/O 阶段会执行除了 close 事件，定时器和 setImmediate 的回调
idle, prepare
idle, prepare 阶段内部实现
poll
poll 阶段很重要，这一阶段中，系统会做两件事情
1、执行到点的定时器
2、执行 poll 队列中的事件
存储
cookie，localStorage，sessionStorage，indexDB
特性 cookie localStorage sessionStorage indexDB
数据生命周期 一般由服务器生成，可以设置过期时间 除非被清理，否则一直存在 页面关闭就清理 除非被清理，否则一直存在
数据存储大小 4K 5M 5M 无限
与服务端通信 每次都会携带在 header 中，对于请求性能影响 不参与 不参与 不参与
从上表可以看到，cookie 已经不建议用于存储。如果没有大量数据存储需求的话，可以使用 localStorage 和 sessionStorage 。对于不怎么改变的数据尽量使用 localStorage 存储，否则可以用 sessionStorage 存储。
对于 cookie，我们还需要注意安全性。
属性 作用
value 如果用于保存用户登录态，应该将该值加密，不能使用明文的用户标识
http-only 不能通过 JS 访问 Cookie，减少 XSS 攻击
secure 只能在协议为 HTTPS 的请求中携带
same-site 规定浏览器不能在跨域请求中携带 Cookie，减少 CSRF 攻击
Service Worker
Service workers 本质上充当Web应用程序与浏览器之间的代理服务器，也可以在网络可用时作为浏览器和网络间的代理。它们旨在（除其他之外）使得能够创建有效的离线体验，拦截网络请求并基于网络是否可用以及更新的资源是否驻留在服务器上来采取适当的动作。他们还允许访问推送通知和后台同步API。
目前该技术通常用来做缓存文件，提高首屏速度，可以试着来实现这个功能。
// index.js
if (navigator.serviceWorker) {
  navigator.serviceWorker
    .register("sw.js")
    .then(function(registration) {
      console.log("service worker 注册成功");
    })
    .catch(function(err) {
      console.log("servcie worker 注册失败");
    });
}
// sw.js
// 监听 `install` 事件，回调中缓存所需文件
self.addEventListener("install", e => {
  e.waitUntil(
    caches.open("my-cache").then(function(cache) {
      return cache.addAll(["./index.html", "./index.js"]);
    })
  );
});

// 拦截所有请求事件
// 如果缓存中已经有请求的数据就直接用缓存，否则去请求数据
self.addEventListener("fetch", e => {
  e.respondWith(
    caches.match(e.request).then(function(response) {
      if (response) {
        return response;
      }
      console.log("fetch source");
    })
  );
});
### 渲染机制
浏览器的渲染机制一般分为以下几个步骤<br>
1、处理 HTML 并构建 DOM 树。<br>
2、处理 CSS 构建 CSSOM 树。<br>
3、将 DOM 与 CSSOM 合并成一个渲染树。<br>
4、根据渲染树来布局，计算每个节点的位置。<br>
5、调用 GPU 绘制，合成图层，显示在屏幕上<br>

在构建 CSSOM 树时，会阻塞渲染，直至 CSSOM 树构建完成。并且构建 CSSOM 树是一个十分消耗性能的过程，所以应该尽量保证层级扁平，减少过度层叠，越是具体的 CSS 选择器，执行速度越慢。<br>
当 HTML 解析到 script 标签时，会暂停构建 DOM，完成后才会从暂停的地方重新开始。也就是说，如果你想首屏渲染的越快，就越不应该在首屏就加载 JS 文件。并且 CSS 也会影响 JS 的执行，只有当解析完样式表才会执行 JS，所以也可以认为这种情况下，CSS 也会暂停构建 DOM。<br>
### Load 和 DOMContentLoaded 区别
Load 事件触发代表页面中的 DOM，CSS，JS，图片已经全部加载完毕。<br>
DOMContentLoaded 事件触发代表初始的 HTML 被完全加载和解析，不需要等待 CSS，JS，图片加载。<br>
### 图层
一般来说，可以把普通文档流看成一个图层。特定的属性可以生成一个新的图层。不同的图层渲染互不影响，所以对于某些频繁需要渲染的建议单独生成一个新图层，提高性能。但也不能生成过多的图层，会引起反作用。<br>
通过以下几个常用属性可以生成新图层<br>
3D 变换：translate3d、translateZ<br>
will-change<br>
video、iframe 标签<br>
通过动画实现的 opacity 动画转换<br>
position: fixed<br>
### 重绘（Repaint）和回流（Reflow）
重绘和回流是渲染步骤中的一小节，但是这两个步骤对于性能影响很大。<br>
重绘是当节点需要更改外观而不会影响布局的，比如改变 color 就叫称为重绘<br>
回流是布局或者几何属性需要改变就称为回流。<br>
回流必定会发生重绘，重绘不一定会引发回流。回流所需的成本比重绘高的多，改变深层次的节点很可能导致父节点的一系列回流。<br><br>
所以以下几个动作可能会导致性能问题：<br>
改变 window 大小<br>
改变字体<br>
添加或删除样式<br>
文字改变<br>
定位或者浮动<br>
### 盒模型
很多人不知道的是，重绘和回流其实和 Event loop 有关。
1、当 Event loop 执行完 Microtasks 后，会判断 document 是否需要更新。因为浏览器是 60Hz 的刷新率，每 16ms 才会更新一次。<br>
2、然后判断是否有 resize 或者 scroll ，有的话会去触发事件，所以 resize 和 scroll 事件也是至少 16ms 才会触发一次，并且自带节流功能。<br>
3、判断是否触发了 media query<br>
4、更新动画并且发送事件<br>
5、判断是否有全屏操作事件<br>
6、执行 requestAnimationFrame 回调<br>
7、执行 IntersectionObserver 回调，该方法用于判断元素是否可见，可以用于懒加载上，但是兼容性不好<br>
8、更新界面<br>
9、以上就是一帧中可能会做的事情。如果在一帧中有空闲时间，就会去执行 requestIdleCallback 回调。<br>
减少重绘和回流<br>
使用 translate 替代 top<br>
使用 visibility 替换 display: none ，因为前者只会引起重绘，后者会引发回流（改变了布局）<br>
把 DOM 离线后修改，比如：先把 DOM 给 display:none (有一次 Reflow)，然后你修改100次，然后再把它显示出来<br>
不要把 DOM 结点的属性值放在一个循环里当成循环里的变量<br>
不要使用 table 布局，可能很小的一个小改动会造成整个 table 的重新布局<br>
动画实现的速度的选择，动画速度越快，回流次数越多，也可以选择使用 requestAnimationFrame<br>
CSS 选择符从右往左匹配查找，避免 DOM 深度过深<br>
将频繁运行的动画变为图层，图层能够阻止该节点回流影响别的元素。比如对于 video 标签，浏览器会自动将该节点变为图层。<br>
## 性能
网络相关<br>
DNS 预解析<br>
DNS 解析也是需要时间的，可以通过预解析的方式来预先获得域名所对应的 IP。
```
<link rel="dns-prefetch" href="//yuchengkai.cn">
```
缓存<br>
缓存对于前端性能优化来说是个很重要的点，良好的缓存策略可以降低资源的重复加载提高网页的整体加载速度。
通常浏览器缓存策略分为两种：强缓存和协商缓存。
强缓存<br>
实现强缓存可以通过两种响应头实现：Expires 和 Cache-Control 。强缓存表示在缓存期间不需要请求，state code 为 200<br>
Expires: Wed, 22 Oct 2018 08:41:00 GMT<br>
Expires 是 HTTP / 1.0 的产物，表示资源会在 Wed, 22 Oct 2018 08:41:00 GMT 后过期，需要再次请求。并且 Expires 受限于本地时间，如果修改了本地时间，可能会造成缓存失效。<br>
Cache-control: max-age=30<br>
Cache-Control 出现于 HTTP / 1.1，优先级高于 Expires 。该属性表示资源会在 30 秒后过期，需要再次请求<br>
协商缓存<br>
如果缓存过期了，我们就可以使用协商缓存来解决问题。协商缓存需要请求，如果缓存有效会返回 304。
协商缓存需要客户端和服务端共同实现，和强缓存一样，也有两种实现方式。
Last-Modified 和 If-Modified-Since
Last-Modified 表示本地文件最后修改日期，If-Modified-Since 会将 Last-Modified 的值发送给服务器，询问服务器在该日期后资源是否有更新，有更新的话就会将新的资源发送回来。
但是如果在本地打开缓存文件，就会造成 Last-Modified 被修改，所以在 HTTP / 1.1 出现了 ETag
ETag 和 If-None-Match
ETag 类似于文件指纹，If-None-Match 会将当前 ETag 发送给服务器，询问该资源 ETag 是否变动，有变动的话就将新的资源发送回来。并且 ETag 优先级比 Last-Modified 高。
选择合适的缓存策略
对于大部分的场景都可以使用强缓存配合协商缓存解决，但是在一些特殊的地方可能需要选择特殊的缓存策略
对于某些不需要缓存的资源，可以使用 Cache-control: no-store ，表示该资源不需要缓存
对于频繁变动的资源，可以使用 Cache-Control: no-cache 并配合 ETag 使用，表示该资源已被缓存，但是每次都会发送请求询问资源是否更新。
对于代码文件来说，通常使用 Cache-Control: max-age=31536000 并配合策略缓存使用，然后对文件进行指纹处理，一旦文件名变动就会立刻下载新的文件。
使用 HTTP / 2.0
因为浏览器会有并发请求限制，在 HTTP / 1.1 时代，每个请求都需要建立和断开，消耗了好几个 RTT 时间，并且由于 TCP 慢启动的原因，加载体积大的文件会需要更多的时间。
在 HTTP / 2.0 中引入了多路复用，能够让多个请求使用同一个 TCP 链接，极大的加快了网页的加载速度。并且还支持 Header 压缩，进一步的减少了请求的数据大小。
预加载
在开发中，可能会遇到这样的情况。有些资源不需要马上用到，但是希望尽早获取，这时候就可以使用预加载。
预加载其实是声明式的 fetch ，强制浏览器请求资源，并且不会阻塞 onload 事件，可以使用以下代码开启预加载
<link rel="preload" href="http://example.com">
预加载可以一定程度上降低首屏的加载时间，因为可以将一些不影响首屏但重要的文件延后加载，唯一缺点就是兼容性不好。
预渲染
可以通过预渲染将下载的文件预先在后台渲染，可以使用以下代码开启预渲染
<link rel="prerender" href="http://example.com"> 
预渲染虽然可以提高页面的加载速度，但是要确保该页面百分百会被用户在之后打开，否则就白白浪费资源去渲染
优化渲染过程
懒执行
懒执行就是将某些逻辑延迟到使用时再计算。该技术可以用于首屏优化，对于某些耗时逻辑并不需要在首屏就使用的，就可以使用懒执行。懒执行需要唤醒，一般可以通过定时器或者事件的调用来唤醒
懒加载
懒加载就是将不关键的资源延后加载。
懒加载的原理就是只加载自定义区域（通常是可视区域，但也可以是即将进入可视区域）内需要加载的东西。对于图片来说，先设置图片标签的 src 属性为一张占位图，将真实的图片资源放入一个自定义属性中，当进入自定义区域时，就将自定义属性替换为 src 属性，这样图片就会去下载资源，实现了图片懒加载。
懒加载不仅可以用于图片，也可以使用在别的资源上。比如进入可视区域才开始播放视频等等。
文件优化
图片加载优化
不用图片。很多时候会使用到很多修饰类图片，其实这类修饰图片完全可以用 CSS 去代替。
对于移动端来说，屏幕宽度就那么点，完全没有必要去加载原图浪费带宽。一般图片都用 CDN 加载，可以计算出适配屏幕的宽度，然后去请求相应裁剪好的图片。
小图使用 base64 格式
将多个图标文件整合到一张图片中（雪碧图）
选择正确的图片格式：
对于能够显示 WebP 格式的浏览器尽量使用 WebP 格式。因为 WebP 格式具有更好的图像数据压缩算法，能带来更小的图片体积，而且拥有肉眼识别无差异的图像质量，缺点就是兼容性并不好
小图使用 PNG，其实对于大部分图标这类图片，完全可以使用 SVG 代替
照片使用 JPEG
其他文件优化
CSS 文件放在 head 中
服务端开启文件压缩功能
将 script 标签放在 body 底部，因为 JS 文件执行会阻塞渲染。当然也可以把 script 标签放在任意位置然后加上 defer ，表示该文件会并行下载，但是会放到 HTML 解析完成后顺序执行。对于没有任何依赖的 JS 文件可以加上 async ，表示加载和渲染后续文档元素的过程将和 JS 文件的加载与执行并行无序进行。
执行 JS 代码过长会卡住渲染，对于需要很多时间计算的代码可以考虑使用 Webworker。Webworker 可以让我们另开一个线程执行脚本而不影响渲染。
CDN
静态资源尽量使用 CDN 加载，由于浏览器对于单个域名有并发请求上限，可以考虑使用多个 CDN 域名。对于 CDN 加载静态资源需要注意 CDN 域名要与主站不同，否则每次请求都会带上主站的 Cookie。
其他
使用 Webpack 优化项目
对于 Webpack4，打包项目使用 production 模式，这样会自动开启代码压缩
使用 ES6 模块来开启 tree shaking，这个技术可以移除没有使用的代码
优化图片，对于小图可以使用 base64 的方式写入文件中
按照路由拆分代码，实现按需加载
给打包出来的文件名添加哈希，实现浏览器缓存文件
监控
对于代码运行错误，通常的办法是使用 window.onerror 拦截报错。该方法能拦截到大部分的详细报错信息，但是也有例外
对于跨域的代码运行错误会显示 Script error. 对于这种情况我们需要给 script 标签添加 crossorigin 属性
对于某些浏览器可能不会显示调用栈信息，这种情况可以通过 arguments.callee.caller 来做栈递归
对于异步代码来说，可以使用 catch 的方式捕获错误。比如 Promise 可以直接使用 catch 函数，async await 可以使用 try catch
但是要注意线上运行的代码都是压缩过的，需要在打包时生成 sourceMap 文件便于 debug。
对于捕获的错误需要上传给服务器，通常可以通过 img 标签的 src 发起一个请求。

如何渲染几万条数据并不卡住界面
这道题考察了如何在不卡住页面的情况下渲染数据，也就是说不能一次性将几万条都渲染出来，而应该一次渲染部分 DOM，那么就可以通过 requestAnimationFrame 来每 16 ms 刷新一次。
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <ul>控件</ul>
  <script>
    setTimeout(() => {
      // 插入十万条数据
      const total = 100000
      // 一次插入 20 条，如果觉得性能不好就减少
      const once = 20
      // 渲染数据总共需要几次
      const loopCount = total / once
      let countOfRender = 0
      let ul = document.querySelector("ul");
      function add() {
        // 优化性能，插入不会造成回流
        const fragment = document.createDocumentFragment();
        for (let i = 0; i < once; i++) {
          const li = document.createElement("li");
          li.innerText = Math.floor(Math.random() * total);
          fragment.appendChild(li);
        }
        ul.appendChild(fragment);
        countOfRender += 1;
        loop();
      }
      function loop() {
        if (countOfRender < loopCount) {
          window.requestAnimationFrame(add);
        }
      }
      loop();
    }, 0);
  </script>
</body>
</html>

安全
XSS
跨网站指令码（英语：Cross-site scripting，通常简称为：XSS）是一种网站应用程式的安全漏洞攻击，是代码注入的一种。它允许恶意使用者将程式码注入到网页上，其他使用者在观看网页时就会受到影响。这类攻击通常包含了 HTML 以及使用者端脚本语言。
XSS 分为三种：反射型，存储型和 DOM-based
如何攻击
XSS 通过修改 HTML 节点或者执行 JS 代码来攻击网站。
例如通过 URL 获取某些参数
<!-- http://www.domain.com?name=<script>alert(1)</script> -->
<div>{{name}}</div>   
上述 URL 输入可能会将 HTML 改为 <div><script>alert(1)</script></div> ，这样页面中就凭空多了一段可执行脚本。这种攻击类型是反射型攻击，也可以说是 DOM-based 攻击。
也有另一种场景，比如写了一篇包含攻击代码 <script>alert(1)</script> 的文章，那么可能浏览文章的用户都会被攻击到。这种攻击类型是存储型攻击，也可以说是 DOM-based 攻击，并且这种攻击打击面更广
如何防御
最普遍的做法是转义输入输出的内容，对于引号，尖括号，斜杠进行转义
```
function escape(str) {
	str = str.replace(/&/g, "&amp;");
	str = str.replace(/</g, "&lt;");
	str = str.replace(/>/g, "&gt;");
	str = str.replace(/"/g, "&quto;");
	str = str.replace(/'/g, "&##39;");
	str = str.replace(/`/g, "&##96;");
    str = str.replace(/\//g, "&##x2F;");
    return str
}
```

## CSP
内容安全策略 (CSP) 是一个额外的安全层，用于检测并削弱某些特定类型的攻击，包括跨站脚本 (XSS) 和数据注入攻击等。无论是数据盗取、网站内容污染还是散发恶意软件，这些攻击都是主要的手段。<br>
我们可以通过 CSP 来尽量减少 XSS 攻击。CSP 本质上也是建立白名单，规定了浏览器只能够执行特定来源的代码。<br>
通常可以通过 HTTP Header 中的 Content-Security-Policy 来开启 CSP<br>
只允许加载本站资源<br>
```
Content-Security-Policy: default-src ‘self’
```
只允许加载 HTTPS 协议图片<br>
```
Content-Security-Policy: img-src https://*
```
允许加载任何来源框架<br>
```
Content-Security-Policy: child-src 'none'
```
## CSRF
跨站请求伪造（英语：Cross-site request forgery），也被称为 one-click attack 或者 session riding，通常缩写为 CSRF 或者 XSRF， 是一种挟制用户在当前已登录的Web应用程序上执行非本意的操作的攻击方法。[1] 跟跨網站指令碼（XSS）相比，XSS 利用的是用户对指定网站的信任，CSRF 利用的是网站对用户网页浏览器的信任。<br>
简单点说，CSRF 就是利用用户的登录态发起恶意请求。<br>
### 如何攻击
假设网站中有一个通过 Get 请求提交用户评论的接口，那么攻击者就可以在钓鱼网站中加入一个图片，图片的地址就是评论接口<br>
```
<img src="http://www.domain.com/xxx?comment='attack'"/>
```
如果接口是 Post 提交的，就相对麻烦点，需要用表单来提交接口<br>
```
<form action="http://www.domain.com/xxx" id="CSRF" method="post">
    <input name="comment" value="attack" type="hidden">
</form>
```
### 如何防御
防范 CSRF 可以遵循以下几种规则：<br>
Get 请求不对数据进行修改<br>
不让第三方网站访问到用户 Cookie<br>
阻止第三方网站请求接口<br>
请求时附带验证信息，比如验证码或者 token<br>
SameSite<br>
可以对 Cookie 设置 SameSite 属性。该属性设置 Cookie 不随着跨域请求发送，该属性可以很大程度减少 CSRF 的攻击，但是该属性目前并不是所有浏览器都兼容。<br>

验证 Referer<br>
对于需要防范 CSRF 的请求，我们可以通过验证 Referer 来判断该请求是否为第三方网站发起的。
Token<br>
服务器下发一个随机 Token（算法不能复杂），每次发起请求时将 Token 携带上，服务器验证 Token 是否有效
密码安全
密码安全虽然大多是后端的事情，但是作为一名优秀的前端程序员也需要熟悉这方面的知识
加盐
于密码存储来说，必然是不能明文存储在数据库中的，否则一旦数据库泄露，会对用户造成很大的损失。并且不建议只对密码单纯通过加密算法加密，因为存在彩虹表的关系。
通常需要对密码加盐，然后进行几次不同加密算法的加密。
// 加盐也就是给原密码添加字符串，增加原密码长度
sha256(sha1(md5(salt + password + slat)))
但是加盐并不能阻止别人盗取账号，只能确保即使数据库泄露，也不会暴露用户的真实密码。一旦攻击者得到了用户的账号，可以通过暴力破解的方式破解密码。对于这种情况，通常使用验证码增加延时或者限制尝试次数的方式。并且一旦用户输入了错误的密码，也不能直接提示用户输

## HTTPS<br> 
HTTPS 还是通过了 HTTP 来传输信息，但是信息通过 TLS 协议进行了加密。<br> 

# TLS 
TLS 协议位于传输层之上，应用层之下。首次进行 TLS 协议传输需要两个 RTT ，接下来可以通过 Session Resumption 减少到一个 RTT。<br> 

在 TLS 中使用了两种加密技术，分别为：对称加密和非对称加密。<br> 

### 对称加密：

对称加密就是两边拥有相同的秘钥，两边都知道如何将密文加密解密。<br> 

### 非对称加密：

有公钥私钥之分，公钥所有人都可以知道，可以将数据用公钥加密，但是将数据解密必须使用私钥解密，私钥只有分发公钥的一方才知道。<br> 


### LS 握手过程如下图：


客户端发送一个随机值，需要的协议和加密方式 <br> 
服务端收到客户端的随机值，自己也产生一个随机值，并根据客户端需求的协议和加密方式来使用对应的方式，发送自己的证书（如果需要验证客户端证书需要说明）
客户端收到服务端的证书并验证是否有效，验证通过会再生成一个随机值，通过服务端证书的公钥去加密这个随机值并发送给服务端，如果服务端需要验证客户端证书的话会附带证书
服务端收到加密过的随机值并使用私钥解密获得第三个随机值，这时候两端都拥有了三个随机值，可以通过这三个随机值按照之前约定的加密方式生成密钥，接下来的通信就可以通过该密钥来加密解密
通过以上步骤可知，在 TLS 握手阶段，两端使用非对称加密的方式来通信，但是因为非对称加密损耗的性能比对称加密大，所以在正式传输数据时，两端使用对称加密的方式通信。<br>

PS：以上说明的都是 TLS 1.2 协议的握手情况，在 1.3 协议中，首次建立连接只需要一个 RTT，后面恢复连接不需要 RTT 了。
# 算法
# 时间复杂度
通常使用最差的时间复杂度来衡量一个算法的好坏。<br>
常数时间 O(1) 代表这个操作和数据量没关系，是一个固定时间的操作，比如说四则运算。<br>
对于一个算法来说，可能会计算出如下操作次数 aN + 1，N 代表数据量。那么该算法的时间复杂度就是 O(N)。因为我们在计算时间复杂度的时候，数据量通常是非常大的，这时候低阶项和常数项可以忽略不计。<br>
当然可能会出现两个算法都是 O(N) 的时间复杂度，那么对比两个算法的好坏就要通过对比低阶项和常数项了。<br>
# 位运算
位运算在算法中很有用，速度可以比四则运算快很多。<br>
在学习位运算之前应该知道十进制如何转二进制，二进制如何转十进制。这里说明下简单的计算方式<br>
# 左移 <<
```
10 << 1 // -> 20
```
左移就是将二进制全部往左移动，10 在二进制中表示为 1010 ，左移一位后变成 10100 ，转换为十进制也就是 20，所以基本可以把左移看成以下公式 a * (2 ^ b)

# 算数右移 >>
```
10 >> 1 // -> 5
```
算数右移就是将二进制全部往右移动并去除多余的右边，10 在二进制中表示为 1010 ，右移一位后变成 101 ，转换为十进制也就是 5，所以基本可以把右移看成以下公式 int v = a / (2 ^ b)   <br>
# 冒泡排序
冒泡排序的原理如下，从第一个元素开始，把当前元素和下一个索引元素进行比较。如果当前元素大，那么就交换位置，重复操作直到比较到最后一个元素，那么此时最后一个元素就是该数组中最大的数。下一轮重复以上操作，但是此时最后一个元素已经是最大数了，所以不需要再比较最后一个元素，只需要比较到 length - 1 的位置。
以下两个函数是排序中会用到的通用函数
```
function checkArray(array) {
    if (!array || array.length <= 2) return
}
function swap(array, left, right) {
    let rightValue = array[right]
    array[right] = array[left]
    array[left] = rightValue
}
```
以下是实现该算法的代码
```
function bubble(array) {
  checkArray(array);
  for (let i = array.length - 1; i > 0; i--) {
    // 从 0 到 `length - 1` 遍历
    for (let j = 0; j < i; j++) {
      if (array[j] > array[j + 1]) swap(array, j, j + 1)
    }
  }
  return array;
}
```
该算法的操作次数是一个等差数列 n + (n - 1) + (n - 2) + 1 ，去掉常数项以后得出时间复杂度是 O(n * n)

# 插入排序
插入排序的原理如下。第一个元素默认是已排序元素，取出下一个元素和当前元素比较，如果当前元素大就交换位置。那么此时第一个元素就是当前的最小数，所以下次取出操作从第三个元素开始，向前对比，重复之前的操作。
以下是实现该算法的代码
```
function insertion(array) {
  checkArray(array);
  for (let i = 1; i < array.length; i++) {
    for (let j = i - 1; j >= 0 && array[j] > array[j + 1]; j--)
      swap(array, j, j + 1);
  }
  return array;
}
```
该算法的操作次数是一个等差数列 n + (n - 1) + (n - 2) + 1 ，去掉常数项以后得出时间复杂度是 O(n * n)

# 选择排序
选择排序的原理如下。遍历数组，设置最小值的索引为 0，如果取出的值比当前最小值小，就替换最小值索引，遍历完成后，将第一个元素和最小值索引上的值交换。如上操作后，第一个元素就是数组中的最小值，下次遍历就可以从索引 1 开始重复上述操作。
以下是实现该算法的代码
```
function selection(array) {
  checkArray(array);
  for (let i = 0; i < array.length - 1; i++) {
    let minIndex = i;
    for (let j = i + 1; j < array.length; j++) {
      minIndex = array[j] < array[minIndex] ? j : minIndex;
    }
    swap(array, i, minIndex);
  }
  return array;
}
```
该算法的操作次数是一个等差数列 n + (n - 1) + (n - 2) + 1 ，去掉常数项以后得出时间复杂度是 O(n * n)

# 归并排序
归并排序的原理如下。递归的将数组两两分开直到最多包含两个元素，然后将数组排序合并，最终合并为排序好的数组。假设我有一组数组 [3, 1, 2, 8, 9, 7, 6]，中间数索引是 3，先排序数组 [3, 1, 2, 8] 。在这个左边数组上，继续拆分直到变成数组包含两个元素（如果数组长度是奇数的话，会有一个拆分数组只包含一个元素）。然后排序数组 [3, 1] 和 [2, 8] ，然后再排序数组 [1, 3, 2, 8] ，这样左边数组就排序完成，然后按照以上思路排序右边数组，最后将数组 [1, 2, 3, 8] 和 [6, 7, 9] 排序。<br>
以下是实现该算法的代码
```
function sort(array) {
  checkArray(array);
  mergeSort(array, 0, array.length - 1);
  return array;
}

function mergeSort(array, left, right) {
  // 左右索引相同说明已经只有一个数
  if (left === right) return;
  // 等同于 `left + (right - left) / 2`
  // 相比 `(left + right) / 2` 来说更加安全，不会溢出
  // 使用位运算是因为位运算比四则运算快
  let mid = parseInt(left + ((right - left) >> 1));
  mergeSort(array, left, mid);
  mergeSort(array, mid + 1, right);

  let help = [];
  let i = 0;
  let p1 = left;
  let p2 = mid + 1;
  while (p1 <= mid && p2 <= right) {
    help[i++] = array[p1] < array[p2] ? array[p1++] : array[p2++];
  }
  while (p1 <= mid) {
    help[i++] = array[p1++];
  }
  while (p2 <= right) {
    help[i++] = array[p2++];
  }
  for (let i = 0; i < help.length; i++) {
    array[left + i] = help[i];
  }
  return array;
}
```
以上算法使用了递归的思想。递归的本质就是压栈，每递归执行一次函数，就将该函数的信息（比如参数，内部的变量，执行到的行数）压栈，直到遇到终止条件，然后出栈并继续执行函数。对于以上递归函数的调用轨迹如下
```
mergeSort(data, 0, 6) // mid = 3
  mergeSort(data, 0, 3) // mid = 1
    mergeSort(data, 0, 1) // mid = 0
      mergeSort(data, 0, 0) // 遇到终止，回退到上一步
    mergeSort(data, 1, 1) // 遇到终止，回退到上一步
    // 排序 p1 = 0, p2 = mid + 1 = 1
    // 回退到 `mergeSort(data, 0, 3)` 执行下一个递归
  mergeSort(2, 3) // mid = 2
    mergeSort(3, 3) // 遇到终止，回退到上一步
  // 排序 p1 = 2, p2 = mid + 1 = 3
  // 回退到 `mergeSort(data, 0, 3)` 执行合并逻辑
  // 排序 p1 = 0, p2 = mid + 1 = 2
  // 执行完毕回退
  // 左边数组排序完毕，右边也是如上轨迹
```

# 快排
快排的原理如下。随机选取一个数组中的值作为基准值，从左至右取值与基准值对比大小。比基准值小的放数组左边，大的放右边，对比完成后将基准值和第一个比基准值大的值交换位置。然后将数组以基准值的位置分为两部分，继续递归以上操作。<br>
以下是实现该算法的代码
```
function sort(array) {
  checkArray(array);
  quickSort(array, 0, array.length - 1);
  return array;
}

function quickSort(array, left, right) {
  if (left < right) {
    swap(array, , right)
    // 随机取值，然后和末尾交换，这样做比固定取一个位置的复杂度略低
    let indexs = part(array, parseInt(Math.random() * (right - left + 1)) + left, right);
    quickSort(array, left, indexs[0]);
    quickSort(array, indexs[1] + 1, right);
  }
}
function part(array, left, right) {
  let less = left - 1;
  let more = right;
  while (left < more) {
    if (array[left] < array[right]) {
      // 当前值比基准值小，`less` 和 `left` 都加一
	   ++less;
       ++left;
    } else if (array[left] > array[right]) {
      // 当前值比基准值大，将当前值和右边的值交换
      // 并且不改变 `left`，因为当前换过来的值还没有判断过大小
      swap(array, --more, left);
    } else {
      // 和基准值相同，只移动下标
      left++;
    }
  }
  // 将基准值和比基准值大的第一个值交换位置
  // 这样数组就变成 `[比基准值小, 基准值, 比基准值大]`
  swap(array, right, more);
  return [less, more];
}
```
题目需要我们将 [2,0,2,1,1,0] 排序成 [0,0,1,1,2,2] ，这个问题就可以使用三路快排的思想。
```
var sortColors = function(nums) {
  let left = -1;
  let right = nums.length;
  let i = 0;
  // 下标如果遇到 right，说明已经排序完成
  while (i < right) {
    if (nums[i] == 0) {
      swap(nums, i++, ++left);
    } else if (nums[i] == 1) {
      i++;
    } else {
      swap(nums, i, --right);
    }
  }
};
```
# 链表
题目需要将一个单向链表反转。思路很简单，使用三个变量分别表示当前节点和当前节点的前后节点，虽然这题很简单，但是却是一道面试常考题
```
var reverseList = function(head) {
    // 判断下变量边界问题
    if (!head || !head.next) return head
    // 初始设置为空，因为第一个节点反转后就是尾部，尾部节点指向 null
    let pre = null
    let current = head
    let next
    // 判断当前节点是否为空
    // 不为空就先获取当前节点的下一节点
    // 然后把当前节点的 next 设为上一个节点
    // 然后把 current 设为下一个节点，pre 设为当前节点
    while(current) {
        next = current.next
        current.next = pre
        pre = current
        current = next
    }
    return pre
};
```
# 树
# 二叉树的先序，中序，后序遍历
先序遍历表示先访问根节点，然后访问左节点，最后访问右节点。<br>
中序遍历表示先访问左节点，然后访问根节点，最后访问右节点。<br>
后序遍历表示先访问左节点，然后访问右节点，最后访问根节点。
### 递归实现
```
function TreeNode(val) {
  this.val = val;
  this.left = this.right = null;
}
var traversal = function(root) {
  if (root) {
    // 先序
    console.log(root); 
    traversal(root.left);
    // 中序
    // console.log(root); 
    traversal(root.right);
    // 后序
    // console.log(root);
  }
};
```
### 非递归实现
非递归实现使用了栈的结构，通过栈的先进后出模拟递归实现。
```
function pre(root) {
  if (root) {
    let stack = [];
    // 先将根节点 push
    stack.push(root);
    // 判断栈中是否为空
    while (stack.length > 0) {
      // 弹出栈顶元素
      root = stack.pop();
      console.log(root);
      // 因为先序遍历是先左后右，栈是先进后出结构
      // 所以先 push 右边再 push 左边
      if (root.right) {
        stack.push(root.right);
      }
      if (root.left) {
        stack.push(root.left);
      }
    }
  }
}
```
## 斐波那契数列
斐波那契数列就是从 0 和 1 开始，后面的数都是前两个数之和<br>
0，1，1，2，3，5，8，13，21，34，55，89....<br>
那么显然易见，我们可以通过递归的方式来完成求解斐波那契数列<br>
```
function fib(n) {
  if (n < 2 && n >= 0) return n
  return fib(n - 1) + fib(n - 2)
}
fib(10)
```
以上代码已经可以完美的解决问题。但是以上解法却存在很严重的性能问题，当 n 越大的时候，需要的时间是指数增长的，这时候就可以通过动态规划来解决这个问题。<br>
动态规划的本质其实就是两点<br>
1、自底向上分解子问题<br>
2、通过变量存储已经计算过的解<br>
根据上面两点，我们的斐波那契数列的动态规划思路也就出来了<br>
1、斐波那契数列从 0 和 1 开始，那么这就是这个子问题的最底层<br>
2、通过数组来存储每一位所对应的斐波那契数列的值<br>
```
function fib(n) {
  let array = new Array(n + 1).fill(null)
  array[0] = 0
  array[1] = 1
  for (let i = 2; i <= n; i++) {
    array[i] = array[i - 1] + array[i - 2]
  }
  return array[n]
}
fib(10)
```

# 网络
# UDP
## 面向报文
UDP是一个面向报文（报文可以理解为一段段的数据）的协议，UDP只是报文的搬运工，不会对报文进行拆分和拼接操作。<br>
具体来说<br>
在发送端，应用层将数据传递给传输层UDP协议，UDP 只会给数据增加一个 UDP 头标识下是 UDP 协议，然后就传递给网络层了<br>
在接收端，网络层将数据传递给传输层，UDP 只去除 IP 报文头就传递给应用层，不会任何拼接操作<br>

# HTTP

HTTP 协议是个无状态协议，不会保存状态。
# Post 和 Get 的区别
Get 请求能缓存，Post 不能<br>
Post 相对 Get 安全一点点，因为Get 请求都包含在 URL 里，且会被浏览器保存历史纪录，Post 不会，但是在抓包的情况下都是一样的。<br>
Post 可以通过 request body来传输比 Get 更多的数据，Get 没有这个技术 <br>
URL有长度限制，会影响 Get 请求，但是这个长度限制是浏览器规定的，不是 RFC 规定的<br>
Post 支持更多的编码类型且不对数据类型限制 <br>
# 常见状态码

## 2XX 成功
200 OK，表示从客户端发来的请求在服务器端被正确处理<br>
204 No content，表示请求成功，但响应报文不含实体的主体部分<br>
205 Reset Content，表示请求成功，但响应报文不含实体的主体部分，但是与 204 响应不同在于要求请求方重置内容<br>
206 Partial Content，进行范围请求

# 3XX 重定向

301 moved permanently，永久性重定向，表示资源已被分配了新的 URL
302 found，临时性重定向，表示资源临时被分配了新的 URL
303 see other，表示资源存在着另一个 URL，应使用 GET 方法丁香获取资源
304 not modified，表示服务器允许访问资源，但因发生请求未满足条件的情况
307 temporary redirect，临时重定向，和302含义类似，但是期望客户端保持请求方法不变向新的地址发出请求

# 4XX 客户端错误
400 bad request，请求报文存在语法错误
401 unauthorized，表示发送的请求需要有通过 HTTP 认证的认证信息
403 forbidden，表示对请求资源的访问被服务器拒绝
404 not found，表示在服务器上没有找到请求的资源

# 5XX 服务器错误
500 internal sever error，表示服务器端在执行请求时发生了错误
501 Not Implemented，表示服务器不支持当前请求所需要的某个功能
503 service unavailable，表明服务器暂时处于超负载或正在停机维护，无法处理请求
# HTTPS
HTTPS 还是通过了 HTTP 来传输信息，但是信息通过 TLS 协议进行了加密。

# TLS
TLS 协议位于传输层之上，应用层之下。首次进行 TLS 协议传输需要两个 RTT ，接下来可以通过 Session Resumption 减少到一个 RTT。
在 TLS 中使用了两种加密技术，分别为：对称加密和非对称加密。
## 对称加密：
对称加密就是两边拥有相同的秘钥，两边都知道如何将密文加密解密。
## 非对称加密：
有公钥私钥之分，公钥所有人都可以知道，可以将数据用公钥加密，但是将数据解密必须使用私钥解密，私钥只有分发公钥的一方才知道。
## TLS 握手过程如下图：
1、客户端发送一个随机值，需要的协议和加密方式<br>
2、服务端收到客户端的随机值，自己也产生一个随机值，并根据客户端需求的协议和加密方式来使用对应的方式，发送自己的证书（如果需要验证客户端证书需要说明）<br>
3、客户端收到服务端的证书并验证是否有效，验证通过会再生成一个随机值，通过服务端证书的公钥去加密这个随机值并发送给服务端，如果服务端需要验证客户端证书的话会附带证书<br>
4、服务端收到加密过的随机值并使用私钥解密获得第三个随机值，这时候两端都拥有了三个随机值，可以通过这三个随机值按照之前约定的加密方式生成密钥，接下来的通信就可以通过该密钥来加密解密<br>
通过以上步骤可知，在 TLS 握手阶段，两端使用非对称加密的方式来通信，但是因为非对称加密损耗的性能比对称加密大，所以在正式传输数据时，两端使用对称加密的方式通信。<br>
# HTTP 2.0

HTTP 2.0 相比于 HTTP 1.X，可以说是大幅度提高了 web 的性能。<br>
在 HTTP 1.X 中，为了性能考虑，我们会引入雪碧图、将小图内联、使用多个域名等等的方式。这一切都是因为浏览器限制了同一个域名下的请求数量，当页面中需要请求很多资源的时候，队头阻塞（Head of line blocking）会导致在达到最大请求数量时，剩余的资源需要等待其他资源请求完成后才能发起请求。<br>
## 二进制传输
HTTP 2.0 中所有加强性能的核心点在于此。
## 多路复用
在 HTTP 2.0 中，有两个非常重要的概念，分别是帧（frame）和流（stream）。<br>
帧代表着最小的数据单位，每个帧会标识出该帧属于哪个流，流也就是多个帧组成的数据流。<br>
多路复用，就是在一个 TCP 连接中可以存在多条流。换句话说，也就是可以发送多个请求，对端可以通过帧中的标识知道属于哪个请求。通过这个技术，可以避免 HTTP 旧版本中的队头阻塞问题，极大的提高传输性能<br>
## Header 压缩
在 HTTP 1.X 中，我们使用文本的形式传输 header，在 header 携带 cookie 的情况下，可能每次都需要重复传输几百到几千的字节。<br>
在 HTTP 2.0 中，使用了 HPACK 压缩格式对传输的 header 进行编码，减少了 header 的大小。并在两端维护了索引表，用于记录出现过的 header ，后面在传输过程中就可以传输已经记录过的 header 的键名，对端收到数据后就可以通过键名找到对应的值。
## 服务端 Push
在 HTTP 2.0 中，服务端可以在客户端某个请求后，主动推送其他资源。<br>
可以想象以下情况，某些资源客户端是一定会请求的，这时就可以采取服务端 push 的技术，提前给客户端推送必要的资源，这样就可以相对减少一点延迟时间。当然在浏览器兼容的情况下你也可以使用 prefetch 。
## QUIC
这是一个谷歌出品的基于 UDP 实现的同为传输层的协议，目标很远大，希望替代 TCP 协议。
# 从输入 URL 到页面加载完成的过程
1、首先做 DNS 查询，如果这一步做了智能 DNS 解析的话，会提供访问速度最快的 IP 地址回来<br>
2、接下来是 TCP 握手，应用层会下发数据给传输层，这里 TCP 协议会指明两端的端口号，然后下发给网络层。网络层中的 IP 协议会确定 IP 地址，并且指示了数据传输中如何跳转路由器。然后包会再被封装到数据链路层的数据帧结构中，最后就是物理层面的传输了<br>
3、TCP 握手结束后会进行 TLS 握手，然后就开始正式的传输数据<br>
4、数据在进入服务端之前，可能还会先经过负责负载均衡的服务器，它的作用就是将请求合理的分发到多台服务器上，这时假设服务端会响应一个 HTML 文件<br>
5、首先浏览器会判断状态码是什么，如果是 200 那就继续解析，如果 400 或 500 的话就会报错，如果 300 的话会进行重定向，这里会有个重定向计数器，避免过多次的重定向，超过次数也会报错<br>
6、浏览器开始解析文件，如果是 gzip 格式的话会先解压一下，然后通过文件的编码格式知道该如何去解码文件<br>
7、文件解码成功后会正式开始渲染流程，先会根据 HTML 构建 DOM 树，有 CSS 的话会去构建 CSSOM 树。如果遇到 script 标签的话，会判断是否存在 async 或者 defer ，前者会并行进行下载并执行 JS，后者会先下载文件，然后等待 HTML 解析完成后顺序执行，如果以上都没有，就会阻塞住渲染流程直到 JS 执行完毕。遇到文件下载的会去下载文件，这里如果使用 HTTP 2.0 协议的话会极大的提高多图的下载效率。<br>
8、初始的 HTML 被完全加载和解析后会触发 DOMContentLoaded 事件<br>
9、CSSOM 树和 DOM 树构建完成后会开始生成 Render 树，这一步就是确定页面元素的布局、样式等等诸多方面的东西<br>
10、在生成 Render 树的过程中，浏览器就开始调用 GPU 绘制，合成图层，将内容显示在屏幕上了<br>
