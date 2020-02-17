## Proxy

Proxy 用于修改某些操作的默认行为，在目标对象之前进行操作拦截，外界对该对象的访问都必须先通过这层拦截。

语法：

```javascript
var p = new Proxy(target, handler)
```

target: 被包装的对象。可以是数组/对象/函数/Proxy
handler: 对象，拦截过滤代理操作的函数


方法 | 触发时机
---|---
handler.getPrototypeOf() | 在读取代理对象原型时触发
handler.setPrototypeOf() | 在设置动力对象原型时触发
handler.isExtensible() | 在判断一个对象是否可以扩展时触发
handler.preventExtensions(target: any): boolean | 在使一个对象变为不可扩展时触发
handler.getOwnPropertyDescriptor(target: any, prop: string): PropertyDescriptor \| undefined | 在获取某个属性的属性描述时触发
handler.defineProperty(target: any, prop: string, desc: PropertyDescriptor): boolean | 当对某个属性定义/修改属性描述时触发
handler.has(target: any, prop: string): boolean | 在判断代理对象是否拥有某个属性时触发
handler.get(target: any, prop: string, receiver: any): any | 在获取代理对象的属性时触发
handler.set(target: any, prop: string, value: any: receiver: any): boolean | 设置代理对象的属性时触发
handler.deleteProperty(target: any, prop: string): boolean | 删除代理对象的属性时触发
handler.ownKeys(target: any): any[] | 获取代理对象的key时触发
handler.apply(target: Function, ctx: any, argumentsList?: any[]): any | 拦截函数的调用
handler.construct(target: any, args: any[]): any | 拦截 new 操作

Others:

1. 参数中的 target 指代的是要被拦截的对象；receiver 指代 Proxy 的实例本身

1. Object.isExtensible()  方法判断一个对象是否是可扩展的（是否可以在它上面添加新的属性）。

**Object.preventExtensions**、**Object.seal** 或 **Object.freeze** 都可以标记一个对象为不可扩展

## Reflect

Reflect 是一个内置的对象，它提供拦截 JavaScript 操作的方法。这些方法与处理器对象的方法相同。Reflect不是一个函数对象，因此它是不可构造的(不能 new)。

让Object操作都变成函数行为。某些Object操作是命令式，比如name in obj和delete obj[name]，而Reflect.has(obj, name)和Reflect.deleteProperty(obj, name)让它们变成了函数行为

Reflect 大部分与Object对象的同名方法的作用都是相同的，而且它与Proxy对象的方法是一一对应的。

### Reflect.ownKeys(target)

注意这个方法和 Object.keys 的区别。Object.keys 不会显示 enumerable: false 和 Symbol 的 key 值，Reflect.ownKeys 则会全部显示