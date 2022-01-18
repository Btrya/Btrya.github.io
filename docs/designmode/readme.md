# JavaScript 设计模式及应用实践

## 创建型

### 构造器

js中对于构造器最常见的就是`构造函数`的使用

比如一个构造一个User函数：

```js
function User(name, age, identity) {
  this.name = name
  this.age = age
  this.identity = identity
}
```

像这样的一个`User`就是一个构造器，这里用的是 es5 构造函数的写法，es6 中的 class 本质上也还是函数，class 只是一个语法糖，它的本质仍然是构造函数。

我们这么去使用这个构造函数

```js
const user = new User(name, age identity)
```

在创建一个user的过程中，用户的姓名，年龄，身份这些值都属于 用户 的`共性`，每个 user 的这些值可能都不一样，但他们都有一样的属性。

##### 构造器做了什么？

只不过是将 name、age、identity 赋值给对象的过程封装，确保了每个对象都具备这些属性，确保了`共性`不变，同时又让这几个属性的取值开放出来确保了其`个性`。

### 简单工厂模式

现在有一个需求，根据用户 identity 身份的不同，需要根据身份分配不同的职责。

假如现在有 学生、老师 这两个身份，我们可能会写 

```js
function Student(name, age) {
  this.name = name
  this.age = age
  this.identity = 'student'
  this.work = ['吃饭', '睡觉', '学习']
}
function Teacher(name, age) {
  this.name = name
  this.age = age
  this.identity = 'teacher'
  this.work = ['备课', '上课', '改作业']
}
```

现在我们有两个类了，但是后面可能还有更多，不可能一直这样写下去，我们应该把共性抽出来，再根据其个性化的逻辑判断用另一个函数来处理：

```js
function User(name, age, identity, work) {
  this.name = name
  this.age = age
  this.identity = identity
  this.work = work
}
function Factory(name, age, identity) {
  let work
  switch (identity) {
    case 'student':
      work = ['吃饭', '睡觉', '学习']
      break;
    case 'teacher':
      work = ['备课', '上课', '改作业']
      break;
    case 'principal':
      work = ['开会', '开会', '开会']
      break;
    case 'xxx':
      ...
  }
  return new User(name, age, identity, work)
}
```

这么一来我们就可以无脑传参了，Factory函数已经帮我们把个性化逻辑处理好了！

##### 总结

工厂模式其实就是`将创建对象的过程单独封装`。使我们不必去关心创建过程中发生了什么，只要能给到我们实例即可。

所以工厂模式的目的就是为了实现 `无脑传参` 。

### 抽象工厂模式

对比一下简单的工厂模式，假如有更多 身份 那么还需要去修改 Factory 函数，这也是不可取的，违背了开放封闭原则

我们需要做的就是让 软件实体(类、模块、函数)可以扩展，但是不可以修改。

因此才有了抽象工厂模式，举一个 `组装手机` 的例子：

抽象工厂：
```js
class MobilePhoneFactory {
  // 提供操作系统的接口
  createOS() {
    throw new Error("抽象工厂方法不允许直接调用，你需要将我重写！")
  }
  // 提供硬件的接口
  createHardWare() {
    throw new Error("抽象工厂方法不允许直接调用，你需要将我重写！")
  }
}
```

这个`抽象工厂`他是不干活的，应该交给 `具体工厂` 来干活，换句话说 `抽象工厂`就是约定了具体工厂的通用能力

具体工厂：
```js
// 具体工程继承自抽象工厂
class FakeStarFactory extends MobilePhoneFactory {
  createOS() {
    // 提供安卓系统实例
    return new AndroidOS()
  }
  createHardWare() {
    // 提供高通硬件实例
    return new QualcommHardWare()
  }
}
```

这里看到具体工厂调用了两个构造函数 AndroidOS 和 QualcommHardWare，用于生成具体操作系统和硬件实例。

这种被 new 出具体对象类就叫做 `具体产品类`，相应的也会有 `抽象产品类`

```js
// 定义操作系统这类产品的抽象产品类
class OS {
  controlHardWare() {
    throw new Error('抽象产品方法不允许直接调用，你需要将我重写！')
  }
}
// 定义硬件这类产品的抽象产品类
class HardWare() {
  // 手机硬件的共性方法，这里提取的是“根据命令运转”这个共性
  operateByOrder() {
    throw new Error('抽象产品方法不允许直接调用，你需要将我重写！')
  }
}

// 定义具体操作系统的具体产品类
class AndroidOS extends OS {
  controlHardWare() {
    console.log('用安卓系统的方式去操作')
  }
}
class AppleOS extends OS {
  controlHardWare() {
    console.log('用苹果系统的方式去操作')
  }
}
// 定义具体硬件的具体产品类
class QualcommHardWare extends HardWare {
  operateByOrder() {
    console.log('用高通的方式去运转')
  }
}
class MiWare extends HardWare {
  operateByOrder() {
    console.log('用小米的方式去运转')
  }
}
```

这样依赖，当我们需要生产一台FakeStar手机的时候，我们只需要：

```js
// 这是我的手机
const myPhone = new FakeStarFactory()
// 让它拥有操作系统
const myOS = myPhone.createOS()
// 让它拥有硬件
const myHardWare = myPhone.createHardWare()
// 启动操作系统 输出 '用安卓系统的方式去操作'
myOS.controlHardWare()
// 唤醒硬件 输出 '用高通的方式去运转'
myHardWare.operateByOrder()
```

如果这时候 FakeStar 手机过气了，我们需要做一款新的手机，不需要修改抽象工厂，而是重新写一个具体工厂即可：

```js
class newStarFactory extends MobilePhoneFactory {
  createOS() {
    // 操作系统实现代码
  }
  createHardWare() {
    // 硬件实现代码
  }
}
```

这样的操作不会对原有系统造成任何潜在影响，那么对于抽象产品类也是同样的道理。

##### 总结

抽象工厂和简单工厂的异同？

都是去分离一个系统中可变和不可变的部分，不同于场景的复杂度

抽象工厂需要支持更好的扩展，因此需要使用抽象类来降低扩展的成本，同时对类的性质做划分，因此有了以下四个关键角色：

- 抽象工厂(抽象类，不能用于生成具体实例)
- 具体工厂(用于生产产品族里的一个具体的产品)
- 抽象产品(抽象类，不能用于生成具体实例)
- 具体产品(用于生成产品族里的一个具体的产品所依赖的更细粒度的产品)