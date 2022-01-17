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