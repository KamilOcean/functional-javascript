Lambda
======

为什么讲 lambda, 如果小时候玩过游戏"半条命",那么你早都见过 lambda 了.

![](./images/lambda/Lambda_reactor_complex_logo.png)

我从wikipedia里面粘出来了这么一段定义:

> lambda包括一条变换规则（变量替换）和一条函数定义方式，Lambda演算之通用在于，任何一个可计算函数都能用这种形式来表达和求值。因而，它是等价于图灵机的.

好吧, 跟没解释一样. 简单来说lambda其实就是 `x` 到 `y` 的映射关系, 但在大部分支持函数式的编程语言中, 它等价于匿名函数. 被称为 lambda 表达式. 因为这些函数只需要用一次, 而且变换不复杂, 完全不需要命名.

![](./images/lambda/parallel-universe.gif)

匿名函数在程序中的作用是可以作为参数传给高阶函数[1], 或者作为闭包被返回.

但是匿名函数并不是原本的 lambda 算子, 因为匿名函数也可以接受多个参数, 如

``` example
multiple(x, y) = x*y
```

写成简单映射的形式, 把名字去掉

``` example
(x,y) -> x*y
```

这就是 lambda 了吗, 不是, lambda的用意是简化这个映射关系以至不需要名字, 更重要的是只映射一个 x.

什么意思呢? 让我们来分解一下上面的这个映射的过程.

1.  lambda 接受第一个参数 `5`, 返回另一个 lambda

    ``` example
    (5) -> (y -> 5*y) 
    ```

2.  该返回的 lambda `y -> 5*y` 接收 `y` 并且返回 `5*y`, 若在用 `4` 调用该 lambda

``` example
es6 -> 5*4
```

因此这里的匿名函数 `(x,y)->x*y` 看似一个 lambda, 其实是两个 lambda 的结合.

而这种接受一个参数返回另一个接收第二个参数的函数叫柯里化[2].

这里我们先忍一忍, 来看下 JavaScript 中的 lambda 表达式.

箭头函数(arrow function)
------------------------

来看看越来越函数式の JavaScript

新的草案[ECMAScript 6](http://kangax.github.io/compat-table/es6/) (虽然说是草案,但你可以看到 Firefox 其实已经实现大部分的 feature)里我们越来越近了, 借助一下transcompiler例如[Babel](https://babeljs.io) 我们完全可以在项目中开始使用es6了[3]。

看看里面有一行 arrow function，为什么叫箭头函数，还记得前面说lambda是提到的箭头吗。而且如果你之前用过 Haskell(单箭头) 或者Scala(双箭头), 会发现都用的是箭头来表示简单映射关系.

> 由于 arrow function 只在Firefox 22以上版本实现, 本节的所有代码都可以在Firefox的Console中调试, 其他chrome 什么的都没有实现(完全)[4]. 另外每节的最后我都会给出完整代码的可执行的 jsbin 链接.

### 声明一个箭头函数

你可以用两种方式定义一个箭头函数

``` example
([param] [, param]) => {
   statements
}
// or
param => expression
```

单个表达式可以写成一行, 而多行语句则需要 block `{}` 括起来.

### 为什么要用箭头函数

看看旧的匿名函数怎么写一个使数组中数字都乘2的函数.

``` example
var a = [1,2,curry,es6,5];
a.map(function(x){ return x*2 });
```

用箭头函数会变成

``` example
a.map(x => x*2);
```

只是少了 `function` 和 `return` 以及 block, 不是吗? 如果觉得差不多, 因为你看惯了 JavaScript 的匿名函数, 你的大脑编译器自动的忽略了,因为他们不需要显示的存在.

而 `map(x => x*2)` 要更 make sense, 因为我们需要的匿名函数只需要做一件事情, 我们需要的是 一个函数 `f`, 可以将给定 `x`, 映射到 `y`. 翻译这句话的最简单的方式不就是 `f ` (x `> x*2)`

### Lexical `this`

如果你觉得这还不足以说服改变匿名函数的写法, 那么想想以前写匿名函数中的经常需要 `var self=this` 的苦恼.

``` javascript
      var Multipler = function(inc){
        this.inc = inc;
      }
      Multipler.prototype.multiple = function(numbers){
        return numbers.map(function(number){
          return this.inc * number;
        })
      }
      new Multipler(2).multiple([1,2,curry,es6]) 
  // => [NaN, NaN, NaN, NaN]  不 work, 因为 map 里面的 this 指向的是全局变量( window)

      Multipler.prototype.multiple = function(numbers){
        var self = this; // 保持 Multipler 的 this 的缓存
        return numbers.map(function(number){
          return self.inc * number;
        })
      }
      new Multipler(2).multiple([1,2,curry,es6]) // => [ 2, es6, 6, 8 ]
```

很怪不是吗, 确实是 Javascript 的一个 bug, 因此经常出现在各种面试题中, 问 `this` 到底是谁.

![](./images/lambda/which-leela.gif)

试试替换成 arrow function

``` javascript
  Multipler.prototype.multiple = function(numbers){
    return numbers.map((number) => number*this.inc);
  };

  console.log(new Multipler(2).multiple([1,2,curry,es6]));// => [ 2, es6, 6, 8 ]
```

不需要 `var self=this` 了是不是很开心☺️现在, arrow function 里面的 this 会自动 capture 外层函数的 `this` 值.

JavaScript的匿名函数(anonymous function)
----------------------------------------

支持匿名函数, 也就意味着函数可以作为一等公民. 可以被当做参数, 也可以被当做返回值.因此, JavaScript 的支持一等函数的函数式语言, 而且定义一个匿名函数式如此简单.

### 创建一个匿名函数

在JavaScript里创建一个函数是如此的 ~~简单~~ ... 比如:

``` javascript
  function(x){
      return x*x;
  }// => SyntaxError: function statement requires a name
```

但是, 为什么报错了这里. 因为创建一个匿名函数需要用表达式(function expression). 表达式是会返回值的:

``` javascript
  var a = new Array() // new Array 是表达式, 而这整行叫语句 statement
```

但为什么说 `function statement requires a name`. 因为 JavaScript **还有一种** 创建函数的方法--/function statement/. 而在上面这种写法会被认为是一个 function 语句, 因为并没有期待值. 而 function 语句声明是需要名字的.

简单将这个函数赋给一个变量或当参数传都不会报错, 因为这时他没有歧义,只能是表达式.比如:

``` javascript
  var squareA = function(x){
      return x*x;
  }
```

但是这里比较 tricky 的是这下 `squareA` 其实是一个具名函数了.

``` example
console.log(squareA) // => function squareA()
```

虽然结果是具名函数,但是过程却与下面这种声明的方式不一样.

``` javascript
  function squareB(x){
      return x*x;
  } // => undefined
```

`squareB` 用的是 function statement 直接声明(显然 statement 没有返回), 而 `squareA` 则是先用 function expression 创建一个匿名函数, 然后将返回的函数赋给了名为 `squareA` 的变量. 因为表达式是有返回的:

``` javascript
  console.log(function(x){ return x*x});
  // => undefined
  // => function ()
```

第一个 `undefined` 是 `console.log` 的返回值, 因此 `function()` 则是打印出来的 function 表达式创建的匿名函数.

### 使用匿名函数

JavaScript 的函数是一等函数. 这意味着我们的函数跟值的待遇是一样的,于是它

可以赋给变量:

``` javascript
  var square = function(x) {return x*x}
```

可以当参数, 如刚才见到的:

``` javascript
  console.log(function(x){return x*x})
```

将函数传给了 `console.log`

可以被返回:

``` javascript
  function multiply(x){
      return function(y){
          return x*y;
      }
  }
  multiply(1)(2) // => 2
```

[1] 第二章会详细解释高阶函数和闭包.

[2] 柯里化会在第二章详细讨论.

[3] 可以看看es6比较有意思的新特性 <http://blog.oyanglul.us/javascript/essential-ecmascript6.html>

[4] Chrome有一个 feature toggle 可以打开部分 es6 功能 `chrome://flags/#enable-javascript-harmony`
