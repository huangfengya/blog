## 装饰器

装饰器是将一个函数嵌在另一个函数中进行重复使用的目的，增加函数的使用方式。

JavaScript 中一共有四种装饰器：

1. Method Decorator 方法装饰器
2. Property Decorator 属性装饰器
3. Class Decorator 类装饰器
4. Parameter Decorator 参数装饰器

## 方法装饰器

通过使用函数装饰器，可以控制函数的输入和输出。

定义：

```typescript
MethodDecorator = <T>(target: Object, key: string, descriptor: TypedPropertyDescriptor<T>) => TypedPropertyDescriptor<T> | void
```

参数的含义：

target: 被装饰的对象
key: 被装饰的函数名
descriptor: 被传递过来的属性的属性描述符，就是 Object.defineProperty 定义的那些属性

**装饰器的本质就是修改描述符**

```typescript
// 为了传递参数，使用闭包
function decorator() {
    console.log('decorator start')  // 注意，即使没有调用方法，这一步也会执行
    return (target: any, key: string, desc: PropertyDescriptor) => {    // 返回一个闭包函数
        let oldValue = desc.value   // 被包装的方法
        
        desc.value = function() {    // 修改被包装的函数
            console.log('decorator called')
            let value = oldValue.apply(null, arguments)  // 执行被包装的方法
            console.log('decorator end')
            return value    // 这个地方才是调用方法后的真正输出
        }
        
    }
}

class D {
    @decorator()
    public static test() {
        console.log('test func start')
        return 'test func return'
    }
}

console.log(D.test())

// 输出
// decorator start
// decorator called
// test func start
// decorator end
// test func return
```

**注意：每个装饰器接收到的 desc 是紧挨其下面的那个函数/装饰器，即该装饰器用来装饰它下面的所有东西，但是target、key 是不变的**

```typescript
function f() {
    return (target: any, key: string, desc: PropertyDescriptor) => {
        let oldValue = desc.value   // 是 g() 函数
    }
}

function g() {
    return (target: any, key: string, desc: PropertyDescriptor) => {
        let oldValue = desc.value   // 是 test 函数
    }
}

class D {
    @f()
    @g()
    public static test() {
        
    }
}
```

## 属性装饰器

属性装饰器和方法装饰器差不多，用来重新定义getters, setters, enumerable, configurable 等属性，说白了就是使用 Object.defineProperty 来重新定义其 property

定义

```typescript
PropertyDecorator = (target: Object, key: string) => void
```

参数的含义：

target: 被装饰的对象
key: 被装饰的函数名

```typescript
function decorator(target: any, key: string) {
  let val = target[key]
  Object.defineProperty(target, key, {
    get: function() {
      return `Real(${val})`
    },
    set: function(newVal) {
      val = newVal
    }
  })
}

class D {
  @decorator
  public test = "hello"

  public d() {
    console.log(this.test)
  }
}
```

**注意：虽然属性装饰器也可以使用多个装饰器，但是只能设置一次 setter / getter**

## class 装饰器

class 装饰器是通过操作 Class 的构造函数，来实现对 Class 的相关属性和方法的动态添加和修改

定义：

```typescript
ClassDecorator = <TFunction extends Function>(target: TFunction) => TFunction
```

参数的含义：

target: 被装饰的构造函数

```typescript
interface extra {   // 实现一个接口，实现接口中的所有方法；相当于声明必须要实现的接口
  extra: string,
  test: Function
}
type Constructor<T> = new (...args: any[]) => T // mixin constructor type
function f<T extends Constructor<{}>>(target: T) {
  return class extends target implements extra {
    extra = 'extra string'
    test = () => {
      return this.extra
    }
  }
}

@f
class D {
  public a = "hello"
  constructor() {}
}

let d = new D() as D & extra
console.log(d.a)
console.log(d.extra)
console.log(d.test())

// 输出
// hello
// extra string
// extra string
```

[mixin constructor type](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-2.html)

## 参数装饰器

参数装饰器通常是对特殊的参数进行标记，然后在其他的装饰器中读取对应的标记，进行进一步的操作

定义：

```typescript
ParameterDecorator = (target: Object, propertyKey: string | symbol, parameterIndex: number) => void
```

参数的含义：

target: 指被装饰的对象
propertyKey: 被装饰的参数所属的方法名
parameterIndex: 被装饰的对象在数组中的索引位置

```typescript
function f(target: any, key: string, index: number) {
  const metadataKey = `desc_${key}_params`
  if (Array.isArray(target[metadataKey])) {
    target[metadataKey].push(index)
  } else {
    target[metadataKey] = [index]
  }
}

function g(target: any, key: string, desc: PropertyDescriptor) {
  const metadataKey = `desc_${key}_params`
  const originValue = desc.value
  const metadata: number[] = target[metadataKey]  // 被装饰过的参数索引数组
  desc.value = function(...args: any[]) {
    for (let i = 0; i < args.length; i++) {
      if (metadata.includes(i)) {
        args[i] = "Real-" + args[i] // 对装饰参数进行处理
      }
    }
    return originValue.apply(null, args)  // 执行原函数
  }
}

class D {
  constructor() {}

  @g
  public static test(s1: string, @f s2: string) {
    console.log(`${s1} ${s2}`)
  }
}

D.test('hello', 'world')
```