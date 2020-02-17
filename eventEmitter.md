EventEmitter 的核心就是事件触发与事件监听器功能的封装。

原理就是订阅发布者模式

```javascript
class EventsEmitter {
  constructor() {
    this._event = Object.create(null);
  }

  on(eventName, cb) {
    if (typeof cb !== 'function') {
      return
    }

    if (!this._event) {
      this._event = Object.create(null)
    }

    if (!this._event[eventName]) this._event[eventName] = [cb]
    else this._event[eventName].push(cb)
  }

  off(eventName, cb) {
    if (!this._event[eventName]) return
    this._event[eventName] = this._event[eventName].forEach((value) => {
      return cb !== value
    })
  }

  once(eventName, cb) {
    function F() {
      cb.call(this, Array.from(arguments))
      this.off(eventName, cb)
    }
    this.on(eventName, F)
  }

  emmit(eventName, ...arg) {
    if (!this._event[eventName]) return
    this._event[eventName].forEach(cb => {
      cb.call(this, ...arg)
    });
  }
}
```