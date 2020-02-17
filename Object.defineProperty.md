## Object.defineProperty

该方法允许精确添加或修改对象的属性。通过赋值操作添加的普通属性是可枚举的，能够遍历出来(for...in 或 Object.keys 等方法)，这些属性的值可以被改变，也可以被删除，使用该方法可以修改默认的选项或配置。默认情况下，使用该方法添加的属性是不可修改的。

### 语法

> Object.defineProperty(obj, prop, descriptor)

- obj: 要被定义属性的对象
- prop: 要定义或修改的属性的名称
- descriptor: 将被定义或修改的属性描述符

#### 属性描述符

对象里目前存在的属性描述符有两种主要形式: **数据描述符**和**存取描述符**。这两种形式**不能同时存在！**
1. 数据描述符：是一个具有值的属性，该值可能是可写的，也可能是不可写的。
2. 存取描述符：是由 getter-setter 函数对描述的属性。

通用键值：

- configurable: 当且仅当该属性的 configurable 值为 true 时，该属性的**描述符**才能被改变，同时该属性也能从对应的对象上删除。**默认为 false**
- enumerable: 当且仅当该属性的 enumerable 为 true 时，该属性才能出现ad对象的枚举属性中。**默认为 false**

数据描述符可选键值：

- value: 该属性对应的值，可以是任意有效的 JavaScript 值。**默认 undefined**
- writable: 当且仅当该属性的 writable 值为 true 时，value 才能被赋值运算符改变。**默认为 undefined**

存取描述符可选键值：

- get: 当访问该属性时，该方法会被执行，无参数。**默认为 undefined**
- set: 当修改该属性时，该方法会被执行，接受唯一参数，即该属性新的参数值。**默认为 undefined**

||configurable|enumerable|value|writable|get|set|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|数据描述符|√|√|√|√|x|x|
|存取描述符|√|√|x|x|√|√|

### 示例

如果对象中不存在指定的属性，那么该方法会创建这个属性。

```javascript
var obj = {}

Object.defineProperty(obj, "a", {
  confingurable: true,
  enumerable: false,
  value: 10,
  writable: false
})

console.log(obj.a) // 10
console.log(obj) // {}, 因为 enumerable 值为false，该属性无法显示或被遍历出来

obj.a = 1
console.log(obj.a) // 10 因为 writable 值为 false，无法被修改

delete obj.a        // false 返回值为是否删除成功
console.log(obj.a) // 10 因为 writable 值为 false，也无法被删除
```

```javascript
var obj = {}

Object.defineProperty(obj, "a", {
  configurable: true,
  enumerable: true,
  set: function(val) {
    console.log("我被修改了")
  },
  get: function() {
    console.log("我被获取了")
  }
})

obj.a = 1   // 我被修改了
obj.a // 我被获取了
```

### 关于被设置 get/set 的对象的显示问题

在通过 Object.defineProperty 设置值的时候，被设置的对象的值实际上就是 get 函数中的返回值，所以可以通过闭包来设置要显示的值。