# Functor & Monad
### Functor
Functor 是 可以被 map over 的类型. 什么叫 map over...

比如 list 就可以说是可以被map over... 那么是不是可枚举类型?

不是的, 来看看 Haskell 中如何解释(其实所有函数式的概念可能用 haskell 是最能说明问题的了).

```haskell
ghci > :t fmap
fmap :: (a -> b) -> fa -> f b
```

`fmap` 又是什么东西, fmap 是 map over Functor 的函数. 这个函数只干一个事情, 可能通过前面解释的一点点 Haskell功夫,你可能能翻译`(a -> b) -> fa -> f b`了把. 给定一个从`a` 到` b` 的映射函数, 再给定一个 a 的 Functor, 返回一个 b 的 Functor.

虽然个个字都认识, 但怎么就不知道啥意思.

如果我再说一个新词, 你是不是会疯掉了-- Lift.

好吧, 把他们都串起来, 你就明白了.
1. 平常我们可以把`a`到`b` 的映射可以叫做 map, 映射的方式就是函数了.
2. 那么类似的对于函数或者其他可以做这种 map 操作的类型或一种计算方式, 叫做 Functor.
3. 而这种 map 就叫做 fmap, 给定 a 集合到 b 集合的映射方式(也就是一个函数), 就能找到 对 a 的一种计算(computation, 任何可变换的类型, 这就是 Functor) 的变换 -- 对 b 的对应计算方式.
4. 如果该计算是一个函数, 那么这个操作叫做 lifting. 非常形象的, 根据 a 到 b 的映射 lift(举) 到另一个层面上.

![](http://learnyouahaskell-zh-tw.csie.org/img/lifter.png)

虽然 lifting 很形象, 但是还是越说越抽象了, 来举个栗子.
### 举个栗子🌰
> **note** 注意我们还没有实现 Functor, 因此下面的栗子还不能运行在你的 console.

前面说了, Functor 可以是数组, 因为数组可以被 map over
```js
var plus1 = n => n+1;
fmap(plus1, [2, 4, 6, 8])// => [3,5,7,9]
```
这里,数组 Array 就是 Functor 类型, 而 fmap 把 2 -> 3 的映射方式对 Array [2,4,6,8] 进行了变换, 得到 [3,5,7,9]. 这跟数组的 map 方法一样, 比较好理解.

再试试换一种 Functor 类型, 试试函数
```js
var times2 = m => m*2;
fmap(plus1, times2) // => function(){}
fmap(plus1, times2)(3) // => 7 (3*2+1)
```
看到 fmap 返回的是一个函数, 因为你 map over 的是一个函数` times2`. 还记得 `(a -> b) -> fa -> f b`的公式么, 因为现在的 Functor 为 Function 类型, 函数类型可以理解为`x->`, 因此我们可以将该公式替换为
```
(a -> b) -> (x -> a) -> (x -> b)
```

再用我们具体的函数 plus1 替换进去
```
(n->n+1) -> (x->n) -> (x -> n+1)
```
也就是说, times2 吃 x 返回任何 n的话, 该 fmap lift 出来的函数会返回 n+1.

这不就是函数组合吗 `plus1(times2(3))`

Functor 还可以是 Monad...

### Functor in JavaScript
首先, 实现 Functor 类型定义.
```js
 Functor = function(type, defs) {
        var oldTypes = types;
        types = function(obj) {
            if (type.prototype.isPrototypeOf(obj)) {
                return defs;
            }
            return oldTypes(obj);
        }
};
```

tobe continue...
