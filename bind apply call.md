### 相同点

1. 这三者都是用来改变函数 this 的指向
2. 第一个参数都是this要指向的对象，不传则为 window
3. 都可以利用后续参数传参

### 不同点

apply 和 call 都是立即执行的，而且 apply 使用数组传参，call 传入的是数组里面的元素，call 实际上是 apply 的语法糖；

利用该特性可以用 apply 来扁平化数组。

> Array.prototype.concat.apply([], [1,2,3,4,[1,2,3]])

bind 不会立即执行，而是返回一个新函数

### 原生模拟 apply call bind

```JavaScript
// apply()
Fn.prototype.myApply = function(obj, arg) {
    obj.fn = this
    obj.fn(...arg)
    delete obj.fn
}

// call()
Fn.prototype.myCall = function(obj, ...arg) {
    obj.fn = this
    obj.fn(...arg)
    delete obj.fn
}

// bind()
Fn.prototype.myBind = function(context, ...arg) {
    let me = this
    return function (...finnalyArg) {
        return me.call(obj, ...arg, ...finnalyArg)
    }
}
```