## Promise 标准

1. 标准中只有一个 **then** 方法，没有 **catch、race、all** 方法，甚至没有构造函数
2. **then** 方法返回一个新的 Promise 方法
3. 不同的 promise 实现需要可以互相调用
4. Promise 的初始状态为 **pending** 它可以由此状态转为 **fulfilled** 或 **rejected** 状态，一旦确定状态后就不可改变。

```javascript
class fakePromise {
  /* Promise 构造函数接受一个executor函数，
  executor 执行完异步或同步操作后，
  调用它的 resolve 和 reject 两个参数
  */
  constructor(executor) {
    let _this = this
    this.status = 'pending' // 状态机
    this.data = undefined   // 数据
    this.onResolvedCallback = []    // resolve 回调函数集
    this.onRejectedCallback = []    // reject 回调函数集
    
    /*
    resolve 和 reject 函数是在判断状态后改为相应的值，
    并执行相应的回调函数
    */
    function resolve(value) {
      if (value instanceof fakePromise) {
        value.then(resolve, reject)
        return
      }
      setTimeout(function() {
        if (_this.status === 'pending') {
          _this.status = 'fulfilled'
          _this.data = value
          _this.onResolvedCallback.forEach(cb => {
            cb(value)
          });
        }
      })
    }

    function reject(reason) {
      setTimeout(function() {
        if (_this.status === 'pending') {
          _this.status = 'rejected'
          _this.data = reason
          _this.onRejectedCallback.forEach(cb => {
            cb(reason)
          })
        }
      })
    }

    try {
      executor(resolve, reject)
    } catch (e) {
      reject(e)
    }
  }
  
  /*
  then 函数接收两个参数，onResolve 和 onReject，
  分别为成功和失败后的回调
  */
  then(onResolve, onReject) {
    let _this = this
    let promise2 = null

    // 根据规范，如果不为 function 则不处理
    onResolve = typeof onResolve === 'function'
      ? onResolve : function(value) {return value}
    onReject = typeof onReject === 'function'
      ? onReject : function(reason) {throw reason}
    
    if (_this.status === 'fulfilled') {
      // 根据规范，返回的是一个新 promise
      return promise2 = new fakePromise(function(resolve, reject) {
        setTimeout(function() {
          try {
            let x = onResolve(value)
            // 如果返回的是 promise，则取他的结果作为 promise2 的结果
            if (x instanceof fakePromise) {
              x.then(resolve, reject)
            } else {
              // 否则以他的返回值作为promise2的结果
              resolve(x)
            }
          } catch (e) {
            reject(e)
          }
        })
      })
    }
    // 大体同上一个 if
    if (_this.status === 'rejected') {
      return promise2 = new fakePromise(function(resolve, reject) {
        setTimeout(function() {
          try {
            let x = onReject(_this.data)
            if (x instanceof fakePromise) {
              x.then(resolve, reject)
            } else {
              resolve(x)
            }
          } catch (e) {
            reject(e)
          }
        })
      })
    }
    
    /*
    注意此处只会在异步执行的时候出发，比如setTimeout
    与上一个if的区别在于需要将执行的函数加入函数集中
    */
    if (_this.status === 'pending') {
      return promise2 = new fakePromise(function(resolve, reject) {
        _this.onResolvedCallback.push(function(value) {
          try {
            let x = onResolve(_this.data)
            if (x instanceof fakePromise) {
              x.then(resolve, reject)
            } else {
              resolve(x)
            }
          } catch (e) {
            reject(e)
          }
        })

        _this.onRejectedCallback.push(function(reason) {
          try {
            let x = onReject(_this.data)
            if (x instanceof fakePromise) {
              x.then(resolve, reject)
            } else {
              resolve(x)
            }
          } catch (e) {
            reject(e)
          }
        })
      })
    }
  }

  catch(onReject) {
    return this.then(null, onReject)
  }
}
```

## Promise 方法扩展

fakePromise.all

接收一个promise对象数组为参数 只有全部 promise 进入 fulfilled 状态才会继续后面的处理 通常会用来处理 多个并行异步操作

```javascript
/**
参数：promise 对象数组
返回值：promise 实例
当这个数组里面所有的promise 都进入 Fulfilled 状态
*/

fakePromise.all = function(promiseArr) {
    return new fakePromise((resolve, reject) => {
        let values = []
        let count = []
        promiseArr.foreach((promise, index) => {
            promise.then(value => {
                values[index] = value
                count++
                if (count === pormiseArr.length) {
                    resolve(values)
                }
            }, reject)
        })
    })
}
```

fakePromise.race

接收一个promise对象数组为参数 Promise.race 只要有一个promise对象进入 fulfilled 或者 rejected 状态的话，就会继续进行后面的处理

```javascript
/**
 * Promise.race
 * 参数: 接收 promise对象组成的数组作为参数
 * 返回值: 返回一个Promise实例
 * 只要有一个promise对象进入 FulFilled 或者 Rejected 状态的话，就会继续进行后面的处理(取决于哪一个更快)
 */

fakePromise.race = function(promiseArr) {
    return new fakePromise((resovle, reject) => {
        promises.foreach((promise) => {
            promise.then(resolve, reject)
        })
    })
}
```

## async/await

async 是 generator 的语法糖，包含一个 generator 函数和自启动执行器，每次当promise成功，则递归执行下一个函数

```javascript
function asyncFun(func) {
    var gen = func()
    function next(data) {
        var result = gen.next()
        if (result.done) return result.value
        result.value.then(function(data {
            next(data)
        }))
    }
    
    next()
}

// 如何使用
function getNum(num) {
    return new Promise((resolve, reject) => {
        resolve(num + 1)
    })
}
var func = function* () {
    var f1 = yield getNum(1)
    var f2 = yield getNum(f1)
    console.log(f2)
}
asyncFunc(func)
```
