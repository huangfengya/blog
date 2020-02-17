```javascript
// 小程序注入 watcher
function defindeReactive(obj, key, val, callback) {
  let property = Object.getOwnPropertyDescriptor(obj, key)
  let getter = property && property.get
  let setter = property && property.set

  Object.defineProperty(obj, key, {
    enumerable: false,
    get() {
      let value = getter ? getter.call(obj) : val // 调用原先的 getter
      return value
    },
    set(newVal) {
      let oldVal = getter ? getter.call(obj) : val
      if (typeof callback === 'function') {
        callback(newVal, val)
      }
      if (setter) {
        setter.call(obj, newVal)
      } else {
        val = newVal
      }
    }
  })
}

export function watch(cxt, cbObj) {
  let data = cxt.data
  for (let key in data) {
    defindeReactive(cxt, key, cbObj[key])
  }
}
```