## HTTP
1. http相关 204 206 301 302 304 400 401 403 404状态码含义
答：
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
答：
HTTPS加密过程
HTTPS加密请求（一次握手）过程首先，客户端发起握手请求，以明文传输请求信息，包含版本信息，加密一套件候选列表，压缩算法候选列表，随机数，扩展字段等信息（这个没什么好说的,就是用户在浏览器里输入一个HTTPS网址，然后连接到服务端的443端口。）服务端的配置,采 用HTTPS协议的服务器必须要有一套数字证书,可以自己制作,也可以向组织申请。区别就是自己颁发的证书需要客户端验证通过，才可以继续访问， 而使用受信任的公司申请的证书则不会弹出提示页面。这套证书其实就是一对公钥和私钥。

3. 401和403的区别
答： 一个是没登陆 一个是登陆了没权限

4. 请问在你们的项目里面是如何控制静态资源缓存的 使用的是什么策略
答：
静态资源的缓存主要是采用强弱缓存并存的方式

5. 什么情况命中304 如何命中 如果命中了强缓存 返回什么状态码
答：
304的命中主要是看如果资源在协商缓存（若缓存）中服务器对于资源的对比中，例如通过eTag进行对比，如果一致就会返回304，如果不一致则发送新资源状态码为200

6. 如果我压根就不想要缓存 怎么设置
答：
不想要缓存的话，可以在请求头中设置 cache-control: no-store

7. 三级缓存原理是什么？
答：
1. 先去内存看，如果有，直接加载；
2. 如果内存没有，则去硬盘获取，如果有直接加载；
3. 如果硬盘也没有，那么就进行网络请求
4. 加载到的资源缓存到硬盘和内存

## JS
1. Object.setPrototypeOf和Object.create 区别
答：
Object.crate() 方法是es5中的关于原型的方法， 这个方法会使用指定的原型对象以及属性去创建一个新的对象。
Object.setPrototypeOf() 方法设置一个指定的对象的原型 ( 即, 内部[[Prototype]]属性）到另一个对象或  null

2. 字符串‘aaa’他只是一个基础数据类型 但是能拥有比如substring slice等等这些方法呢
答：
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

## CSS
1. flex:1代表了什么意思
答：
flex-grow: 1; flex-shrink : 1; flex-basis: auto;

2. 单行文本溢出省略号和一个向下的三角形  css怎么写
答：
单行文本溢出省略号：overflow: hidden; white-space: nowrap; text-overflow: ellipsis;
向下三角形：
width: 0;
height: 0;
border-style: solid;
border-width: 100px 100px 0 100px;
border-color: #007bff transparent transparent transparent;

3. flex布局 有哪些常用属性
答：
flex-direction 决定排列方向 
flex-wrap 规定轴线如何换行
justify-content 主轴对齐方式
align-items 交叉轴对齐方式
align-content 多根轴线的对齐方式，只有一根轴线不起作用

4.  css如何开启硬件加速
答：
利用transform: translateZ(0);欺骗浏览器开启gpu加速

5. 如何在浏览器画出一个正方形  如何保证这个正方形是浏览器可视区最大的？
答：

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