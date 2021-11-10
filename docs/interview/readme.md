## 浏览器 & HTTP
1. http相关 204 206 301 302 304 400 401 403 404状态码含义

(1) 2XX成功
• 200 OK,表示从客户端发来的请求在服务器端被正确处理
• 204 No content,表示请求成功，但响应报文不含实体的主体部分
• 205 Reset Content,表示请求成功，但响应报文不含实体的主体部分，但是与204响应不同在于要求请求方 
重置内容
• 206 Partial Content,进行范围请求
(2) 3XX重定向
• 301 moved permanently,永久性重定向，表示资源已被分配了新的URL
• 302 found,临时性重定向，表示资源临时被分配了新的URL
• 303 see other,表示资源存在着另一个URL,应使用GET方法获取资源
• 304 not modified,表示服务器允许访问资源，但因发生请求未满足条件的情况
• 307 temporary redirect,临时重定向，和302含义类似，但是期望客户端保持请求方法不变向新的地址发出清 
求
(3) 4XX客户端错谩
• 400 bad request,请求报文存在语法错误
• 401 unauthorized,表示发送的请求需要有通过HTTP认证的认证信息
• 403 forbidden,表示对请求资源的访问被服务器拒绝
• 404 not found,表示在服务器上没有找到请求的资源
(4) 5XX服务器错课
• 500 Internal sever error,表示服务器端在执行请求时发生了错误
• 501 Not Implemented,表示服务器不支持当前请求所需要的某个功能
• 503 service unavailable,表明服务器暂时处于超负载或正在停机维护，无法处理请求

2. https加密过程是怎样的

HTTPS加密过程
HTTPS加密请求（一次握手）过程首先，客户端发起握手请求，以明文传输请求信息，包含版本信息，加密一套件候选列表，压缩算法候选列表，随机数，扩展字段等信息（这个没什么好说的,就是用户在浏览器里输入一个HTTPS网址，然后连接到服务端的443端口。）服务端的配置,采 用HTTPS协议的服务器必须要有一套数字证书,可以自己制作,也可以向组织申请。区别就是自己颁发的证书需要客户端验证通过，才可以继续访问， 而使用受信任的公司申请的证书则不会弹出提示页面。这套证书其实就是一对公钥和私钥。

3. 401和403的区别
 一个是没登陆 一个是登陆了没权限

4. 请问在你们的项目里面是如何控制静态资源缓存的 使用的是什么策略

静态资源的缓存主要是采用强弱缓存并存的方式

5. 什么情况命中304 如何命中 如果命中了强缓存 返回什么状态码

304的命中主要是看如果资源在协商缓存（若缓存）中服务器对于资源的对比中，例如通过eTag进行对比，如果一致就会返回304，如果不一致则发送新资源状态码为200

6. 如果我压根就不想要缓存 怎么设置

不想要缓存的话，可以在请求头中设置 cache-control: no-store

7. 三级缓存原理是什么？

1. 先去内存看，如果有，直接加载；
2. 如果内存没有，则去硬盘获取，如果有直接加载；
3. 如果硬盘也没有，那么就进行网络请求
4. 加载到的资源缓存到硬盘和内存

8. Cookie有哪些属性?

基本属性比如max-age/expire/domain/path以及安全相关的secure/httpOnly/same-site等等, 
有特定场景下会使用到，比如顶级域名和子级域名之间的cookie共享和相互修改、删除，会用到domain。

9. 在浏览器里，从输入URL到页面展示，这中间发生了什么？ https://blog.csdn.net/x550392236/article/details/106473683
- 用户输入url并回车
- 浏览器进程检查url，组装协议，构成完整的url
- 浏览器进程通过进程间通信（IPC）把url请求发送给网络进程
- 网络进程接收到url请求后检查本地缓存是否缓存了该请求资源，如果有则将该资源返回给浏览器进程
- 如果没有，网络进程向web服务器发起http请求（网络请求），请求流程如下：
   - 进行DNS解析，获取服务器ip地址，端口（端口是通过dns解析获取的吗？这里有个疑问） 
   - 利用ip地址和服务器建立tcp连接
   - 构建请求头信息
   - 发送请求头信息
   - 服务器响应后，网络进程接收响应头和响应信息，并解析响应内容
- 网络进程解析响应流程；
   - 检查状态码，如果是301/302，则需要重定向，从Location自动中读取地址，重新进行第4步 （301/302跳转也会读取本地缓存吗？这里有个疑问），如果是200，则继续处理请求。
   - 200响应处理：检查响应类型Content-Type，如果是字节流类型，则将该请求提交给下载管理器，该导航流程结束，不再进行后续的渲染，如果是html则通知浏览器进程准备渲染进程准备进行渲染。
- 准备渲染进程
   - 浏览器进程检查当前url是否和之前打开的渲染进程根域名是否相同，如果相同，则复用原来的进程，如果不同，则开启新的渲染进程
- 传输数据、更新状态
   - 渲染进程准备好后，浏览器向渲染进程发起“提交文档”的消息，渲染进程接收到消息和网络进程建立传输数据的“管道”
   - 渲染进程接收完数据后，向浏览器发送“确认提交”
   - 浏览器进程接收到确认消息后更新浏览器界面状态：安全、地址栏url、前进后退的历史状态、更新web页面
#

## JS
1. Object.setPrototypeOf和Object.create 区别

Object.crate() 方法是es5中的关于原型的方法， 这个方法会使用指定的原型对象以及属性去创建一个新的对象。
Object.setPrototypeOf() 方法设置一个指定的对象的原型 ( 即, 内部[[Prototype]]属性）到另一个对象或  null

2. 字符串‘aaa’他只是一个基础数据类型 但是能拥有比如substring slice等等这些方法呢

因为创建变量时基本包装类型做了一次 `new String('aaa')` 的操作

3. 1+1                 2
   1+'1'               '11'
   1+{}                '1[object Object]'
   1+[]                '1'
   1+true              2
   1+function a(){}    '1function a(){}'
   []===true           false
   []===false          false
   []=={}              false
   []==false           true

3. Object.prototype.__proto__ 为什么 === null?
查找属性是要沿着原型链递归查找，所以原型链是要有长度的，null作为一个为空的对象刚好满足要求，是个相对合理的设定

4. 组合继承的缺点，寄生继承的缺点，组合寄生继承的优点
组合继承使用父类实例当做原型，导致原型对象被父类实例属性污染，出现属性冗余问题。寄生继承则是没有盗用父类的构造函数，而只能够继承原型中方法。组合寄生继承解决了前两者的两个缺点是目前最理想的解决方案。

5. 比较一下function构造函数和class关键字的差别
+ class也是特殊的函数
+ function会提升，class不会提升
+ class中可以定义三种方法
 + 在constructor中挂载到this下的方法，成为实例方法
 + 在class中直接定义的非static方法，成为实例的原型上的方法
 + 在class中直接定义的static方法，成为类自己的方法，可以用类名.方法名去访问到

6. 怎么计算元素ele到浏览器视口顶部的距离
let sum = 0
let ele = xxx; // 要求距离的元素
while(ele.offsetParent) {
 sum += ele.offsetTop; // 统计当前到其有定位元素的距离
 ele = ele.offsetParent; // 将父设为当前继续求距离
}
// 输出到视口顶部距离
console.log(sum)

7. 说说prop和attribute的差别
+ prop是js原生对象的直接属性，我们统称为property。
+ attribute定义：html标签的自定义与预定义属性，我们统称为attribute。

8. 说明EventTarget.addEventListener()的第三个参数

新语法：第三个参数可为 Boolean，也可为 Object;
target.addEventListener(type, listener[, useCapture]) -> 
target.addEventListener(type, listener [,{capture: Boolean, bubbling: Boolean, once: Boolean}]);

9. 使用cors 方案解决跨域的时候 cookie 的这些信息会有相关设置吗

发送请求的时候不会带上cookie，需要通过设withCredentials: true来解决
同时后端要配合设置：
- header 信息 Access-Control-Allow-Credentials: true
- Access-Control-Allow-Origin不可以为 '*'，因为 '*' 会和 Access-Control-Allow-Credentials: true 冲突，需配置指定的地址

10. 点击移动端浏览器的前进按钮或后退按钮，往返页面不刷新，可以如何解决

往返缓存指浏览器为了在页面间执行前进后退操作时能拥有更流畅体验的一种策略。
该策略具体表现为：当用户前往新页面前将旧页面的DOM状态保存在缓存里，当用户返回旧页面前将旧页面的DOM状态从缓存里取出并加载。
大部分移动端浏览器都会部署缓存，可大大节省接口请求的时间和带宽。

解决办法
- onunload
// 在新页面监听页面销毁事件
window.addEventListener("onunload", () => {
    // 执行旧页面代码
});

- beforeRouteEnter
若在Vue中使用了keep-alive，可将接口请求放到beforeRouteEnter()里。

- pageshow
pageshow事件在每次页面加载时都会触发，无论是首次加载还是再次加载都会触发，这就是它与load事件的区别。
pageshow事件暴露的persisted可判断页面是否从缓存里取出。
window.addEventListener("pageshow", e => e.persisted && location.reload());

11. Object.create()手写？
```js
function create(obj) {
   function F() {}
   F.prototype = obj
   return new F()
}
```

12. 为什么Object.create()要创建一个function F() {}不直接return {__proto__: obj}？
{__proto__:obj}不能保证此对象的原型就是obj，但是F.prototype = obj;return new F();浏览器就会根据自己的实现去将返回实例的原型设置为obj。（__proto__存在于非IE）

13. 你知道的浅拷贝有哪些？
Object.assign，
slice，
concat，
扩展运算符,
Object.create,
for循环

14. 说说Object.assign和扩展运算符的区别
Object.assign()方法接收的第一个参数作为目标对象，后面的所有参数作为源对象。然后把所有的源对象合并到目标对象中。它会修改了一个对象，因此会触发 ES6 setter。

扩展操作符（…）使用它时，数组或对象中的每一个值都会被拷贝到一个新的数组或对象中。它不复制继承的属性或类的属性，但是它会复制ES6的 symbols 属性。

15. 你知道的深拷贝有哪些？(让你实现，你会怎么设计)
1.JSON.parse(JSON.stringify())
缺陷：会忽略 undefined
会忽略 symbol
不能序列化函数
无法拷贝不可枚举的属性
无法拷贝对象的原型链
拷贝 RegExp 引用类型会变成空对象
拷贝 Date 引用类型会变成字符串
对象中含有 NaN、Infinity 以及 -Infinity，JSON 序列化的结果会变成 null
不能解决循环引用的对象，即对象成环 (obj[key] = obj)。
2.自己设计深拷贝函数
考虑的点：
1.递归 （存在问题，不够健壮）
2.需考虑数组 （问题是只考虑了普通的object，没有考虑数组的话会有问题）
3.循环引用（如果递归进入死循环会导致栈内存溢出了）
4.性能优化（历数组和对象都使用了for in这种方式，实际上for in在遍历时效率是非常低的）
5.其他数据类型（只考虑了普通的object和array两种数据类型，实际上所有的引用类型不止这两个，就需要精确判断引用类型）
自己设计思路：
【基础版】递归实现，通过 for in 遍历传入参数的属性值，如果值是引用类型则再次递归调用该函数，如果是基础数据类型就直接复制。
【存在问题】：1.简单的递归实现深拷贝的函数并不能复制不可枚举的属性以及 Symbol 类型；
2.这种方法只是针对普通的引用类型的值做递归复制，而对于 Array、Date、RegExp、Error、Function 这样的引用类型并不能正确地拷贝；
3.对象的属性里面成环，即循环引用没有解决。

16.  Promise执行过程中可以中断吗？若想中断，怎么对其进行中断。
怎么对其进行中断。
Promise执行是不可以中断的。但实际需求中会出现一种场景，就是在合适的时候，把pending状态的promise给reject掉。例如把网络请求设置超时时间，一旦超时就中断。
这里用定时器模拟一个网络请求，
function timeoutWrapper(p,timeout =2000){
     const wait = new Prkmise(ersolve,reject){
        setTimeout(()=>{

          reject('请求超时')
          
       }，timeout)
    }
    return Promise.race([p,wait])
}

17. event loop 执行过程。
   事件循环从宏任务队列开始，这时宏任务队列中只有一个script（整体代码）任务，遇到任务源时，分发到相应的任务队列中。
   异步任务可分为task 和 microtask 两类(requestAnimationFrame 既不属于 macrotask, 也不属于 microtask)，不同的API注册的异步任务会 依次进入自身对应的队列中，然后等待 Event Loop 将它们依次压入执行栈中执行。

 
18. property 和attribute的区别。
   —property是DOM元素作为对象，附加的内容
   —attribute是dom节点自带的属性它的值只能够是字符串
   —attributes是属于property的一个子集
   —HTML标签中定义的属性和值会保存该DOM对象的attributes属性里面
   —对于 html 的标准属性来说，attribute 和 property 是同步的，是会自动更新的，但是对于自定义的属性来说，他们是不同步的。
 
19. 数组去重的方式。
   Set, filter, 双层for循环然后splice去重,indexof去重,sort,includes,hasOwnProperty,map,[…new Set(arr)]

20. set和map的区别。
   set不允许元素重复
   属性和方法：size-获取元素数量；add(value)-添加元素，返回set实例本身；delete(value)-删除元素返回一个布尔值；has(value)-返回布尔  值，表示该值是否是set实例的元素；clear()-清除所有元素，无返回值。
   map的key可以是任何数据类型，Map中的键值是有序的，Map的键值对个数可以从 size 属性获取。
   属性和方法：set：设置成员key和value; get :获取成员属性值；has:判断是否值存在；delete：删除；clear：清除所有

21. 介绍一下symbol。
   —typeof symbol  =  "symbol"
   —不能使用new命令
   —相同参数的symbol（）返回的值是不相等的：例如let a = Symbol(‘a’) let b = Symbol（’a’）a===b 返回false
   —Symbol 值作为属性名时，该属性是公有属性不是私有属性
   —Symbol 作为对象的属性名，可以保证属性不重名,可以在类的外部访问。
   —Symbol 作为对象属性名时不能用.运算符，要用方括号[]

## CSS
1. flex:1代表了什么意思

flex-grow: 1; flex-shrink : 1; flex-basis: auto;

2. 单行文本溢出省略号和一个向下的三角形  css怎么写

单行文本溢出省略号：overflow: hidden; white-space: nowrap; text-overflow: ellipsis;
向下三角形：
width: 0;
height: 0;
border-style: solid;
border-width: 100px 100px 0 100px;
border-color: #007bff transparent transparent transparent;

3. flex布局 有哪些常用属性

flex-direction 决定排列方向 
flex-wrap 规定轴线如何换行
justify-content 主轴对齐方式
align-items 交叉轴对齐方式
align-content 多根轴线的对齐方式，只有一根轴线不起作用

4.  css如何开启硬件加速

利用transform: translateZ(0);欺骗浏览器开启gpu加速

5. 如何在浏览器画出一个正方形  如何保证这个正方形是浏览器可视区最大的？


第一种：
```css
.container {
   width: 100vmin;
   height: 100vmin;
   border: 1px solid #000;
}
```
第二种：
```css
.container {
   width: 100vmin;
   padding-top: 100vmin;
   border: 1px solid #000;
}
```
第三种：
```css
.container {
   border: 1px solid #000;
   width: 100vmin;
}
.child{
   width: 100%;
   padding-top: 100%;
}
```
第四种：
```css
.container::before {
   content: "";
   width: 100vmin;
   display: block;
   padding-top: 100%;
   border: 1px solid #000;
}
```
6. 什么是BFC,如何形成BFC
BFC:块级格式化上下文.通俗的说就是一个完全独立的空间（布局环境），让空间里的子元素不会影响到外面的布局。
如何形成BFC：
overflow: hidden
display: inline-block
position: absolute
position: fixed
display: table-cell
display: flex
解决了什么问题：
1.使用Float脱离文档流，高度塌陷
2.Margin边距重叠
3.两栏布局，Float脱离文档后文字环绕问题