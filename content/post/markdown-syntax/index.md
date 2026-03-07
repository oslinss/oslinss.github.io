---
title: 前端八股文
date: 2025-11-07
tags: 
    - 前端
    - css
    - html
    - vue
categories:
    - 学习
---

# 前端八股文

## `HTML&CSS`

### 盒模型和怪异盒模型

W3C标准盒模型：总宽度=width+border+padding+margin
`boxing-size: content-box`

IE怪异盒模型：
总宽度 = width + margin
`boxing-size:border-box`

### 元素居中

1.flex布局

```
display:flex;
justify-content:center;
align-items:center;
```

2.grid布局

```
.parent {
  display: grid;
  place-items: center; /* 这相当于 align-items: center和justify-items:center 的简写 */
}
```

3.定位

```
.parent {
  position: relative;
}
 
.child {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

4.水平居中-块级元素

```
.child {
  width: 300px; /* 需要给定一个宽度 */
  margin: 0 auto; /* 左右外边距自动分配 */
}
```

## `JavaScript`

### 基本类型

Number：表示数值，包括整数和浮点数

String：表示字符串

Boolean：布尔值

Undefined：表示未定义的值

Null：表示空对象指针

Symbol：表示唯一且不可变的值，通常用于对象属性的唯一标识符

### 引用类型

Object：对象字面量表示法

`let person = { name: "Nicholas", age: 29 };`

Array：数组

Function：函数实际上是对象，每个函数都是Function类型的实例

`function sum(num1, num2) {
    return num1 + num2;
}`

### 存储差异

基本类型的数据直接存储在栈中，而引用类型的数据存储在堆中，栈中存储的是指向堆内存的引用地址。这导致了在赋值操作时，基本类型是将值直接复制给变量，而引用类型是引用地址的复制。

### 判断数据类型

#### 1.最常见的判断方法：typeof

typeof返回的类型都是字符串形式

```
alert(typeof "helloworld")    ------------------>"string"     
alert(typeof 123)             ------------------>"number"
alert(typeof [1,2,3])         ------------------>"object"
alert(typeof new Function())  ------------------>"function"
alert(typeof new Date())      ------------------>"object"
alert(typeof new RegExp())    ------------------>"object"
alert(typeof Symbol())        ------------------>"symbol"
alert(typeof true)            ------------------>"true"
alert(typeof null)            ------------------>"object"
alert(typeof undefined)       ------------------>"undefined"
alert(typeof 'undefined')     ------------------>"string"
```

#### 2.已知对象类型:   instance of

instanceof 后面一定要是对象类型，并且大小写不能错，该方法适合一些条件选择或分支

```
[1,2,3] instanceof Array                -------->true
new Date() instanceof Date              -------->true
new Function() instanceof Function      -------->true
new Function() instanceof function      -------->false
null instanceof Object                  -------->false
```

#### 3.对象原型链判断方法： Object.prototype.toString.call()

适用于所有类型的判断检测,注意区分大小写. toString方法,在Object原型上返回数据格式

```
console.log(Object.prototype.toString.call("123"))                   [object String]
console.log(Object.prototype.toString.call(123))                     [object Number]
console.log(Object.prototype.toString.call(true))                    [object Boolean]
console.log(Object.prototype.toString.call([1, 2, 3]))               [object Array]
console.log(Object.prototype.toString.call(null))                    [object Null]
console.log(Object.prototype.toString.call(undefined))               [object Undefined]
console.log(Object.prototype.toString.call({name: 'Hello'}))         [object Object]
console.log(Object.prototype.toString.call(function () {}))          [object Function]
console.log(Object.prototype.toString.call(new Date()))              [object Date]
console.log(Object.prototype.toString.call(/\d/))                    [object RegExp]
console.log(Object.prototype.toString.call(Symbol()))                [object Symbol]
```

#### 4.根据对象的构造器constructor进行判断

#### 5.jQuery方法： jquery.type()

如果对象是null跟undefined,直接返回"null"和"undefined"

在使用时,一定要引入jquery文件,不然会报错,jQuery is not defined

```
console.log(jQuery.type(undefined) === "undefined")                         -------->true
console.log(jQuery.type() === "undefined");                                 -------->true
console.log(jQuery.type(window.notDefined) === "undefined")                 -------->true
console.log(jQuery.type(123) === "number")                                  -------->true
console.log(jQuery.type('123') === "string")                                -------->true
console.log(jQuery.type([]) === "array")                                    -------->true
console.log(jQuery.type(true) === "boolean")                                -------->true
console.log(jQuery.type(function(){}) === "function")                       -------->true
console.log(jQuery.type(new Date()) === "date")                             -------->true
console.log(jQuery.type(/\d/) === "regexp")                                 -------->true
console.log(jQuery.type(new Error()) === "error")                           -------->true 
console.log(jQuery.type({name:'Hello'}) === "object")                        ------->true
console.log(jQuery.type(Symbol()) === "symbol")                              ------->true
                                                       ------->其余对象类型一般返回object                                                       
```

#### 6.有局限的判断:严格运算符===

### 数组去重

<br />

**`filter`+`indexOf`方法**

```
const array = [1, 2, 2, 3, 4, 4, 5, 5];
// 使用 filter 和 indexOf 去重
const uniqueArray = array.filter((item, index) => array.indexOf(item) === index);
console.log(uniqueArray); // 输出: [1, 2, 3, 4, 5]
```

<br />

**`Set`数据结构**

```
const uniqueArray = [...new Set(array)];
console.log(uniqueArray); // 输出: [1, 2, 3, 4, 5]
```

<br />

<br />

### 作用域和作用域链

作用域：规定变量和函数的可使用范围

作用域链：每个函数都有一个作用域链，查找变量或者函数时，需要从局部作用域到全局作用域依次查找，这些作用域的集合称作作用域链

### 原型和原型链

**原型**

在js中，每一个对象（函数也是对象）都有一个特殊的属性叫做原型（prototype），它指向另一个对象，这个对象（Test.prototype）被称为原型对象， 原型对象是用来共享属性和方法的

<br />

**原型对象**

* 原型对象有一个constructor属性指向构造函数本身(Test)。

* 原型对象是一个普通的对象，它包含属性和方法。

* 原型对象的属性和方法会被继承到所有通过原型链与它相连的对象。

**隐形原型**

* 每个js对象都有一个隐藏的原型对象属性\_\_proto\_\_，它指向创建它的构造函数的原型对象（Test.prototype）

* proto\_\_存在的意义在于为原型链查找提供方向，原型链查找靠的是\_\_proto，而不是prototype

**原型链**

每个对象都有一个原型(prototype),它指向另外一个对象，而指向的对象又存在属性(proto)指向另外一个对象。当我们访问对象(obj3)的属性时，会先在对象定义的属性中进行查找，没找到就会沿着\_\_proto\_\_一路向上查找，最终形成一个链式结构，这整个链式结构就叫做原型链

### 闭包

JS中内层函数可以访问外层函数的变量，使内部私有变量不受外界干扰，起到保护和保存的作用

好处：

* 隔离作用域，保护私有变量；有了闭包才有局部变量，要不然都是全局变量了。

* 让我们可以使用回调，操作其他函数内部；

* 变量长期驻扎在内存中，不会被内存回收机制回收，即延长变量的生命周期；

坏处：内层函数引用外层函数变量，内层函数占用内存。如果不释放内存，过多时，易引起内存泄露。

引用场景： for循环中的保留i的操作 / 防抖和节流

### 内存泄露、垃圾回收机制

**内存泄露**

是指不再用的内存没有被及时释放出来，导致该段内存无法被使用就是内存泄漏，内存泄漏指我们无法在通过js访问某个对象，而垃圾回收机制却认为该对象还在被引用，因此垃圾回收机制不会释放该对象，导致该块内存永远无法释放，积少成多，系统会越来越卡以至于崩溃

**垃圾回收机制**

就是垃圾收集器按照固定的时间间隔，周期性地寻找那些不再使用的变量，然后将其清楚或释放内存。（标记清除/引用计数）

### Js获取dom

1.getElementById：通过元素的ID属性获取DOM元素。

2.getElementsByClassName：通过元素的class属性获取DOM元素

3.getElementsByTagName：通过元素的标签名获取DOM元素

4.querySelector：通过CSS选择器获取DOM元素

5.querySelectorAll：通过CSS选择器获取DOM元素

### **route和router的区别**

* **router**是用来**操作路由**的

* route**是用来**获取路由信息的 $route.path $ \$route.[params](https://so.csdn.net/so/search?q=params\&spm=1001.2101.3001.7020 "params") route.query等

<br />

### 深浅拷贝

📋浅拷贝

只复制指向对象的指针，新旧对象共享同一块内存。

📄**深拷贝**

另外创造一个一模一样的对象，新对象与原对象不共享内存。

**适用对象**：Object 和 Array 等引用数据类型。

<br />

### **浏览器存储，他们的区别？**

* localStorage：永久保存，以键值对保存，存储空间5M

* sessionStorage：关闭页签/浏览器时清空

* cookie：随着请求发送，通过设置过期时间删除

* session：保存在服务端

## Vue

### Vue2和Vue3的区别

**响应式系统重构**

* **Vue 2 实现**：基于 `Object.defineProperty` 拦截对象属性的 getter/setter，实现依赖收集与更新派发 。存在缺陷：无法监听属性新增/删除、数组索引/长度变化，需通过 `Vue.set/$set` 解决 。

* **Vue 3 升级**：改用 `Proxy` + `Reflect`，可拦截对象所有操作（包括属性增删、数组修改），支持深层响应式，性能提升 2-3 倍 。结合 `ref`（基础类型）和 `reactive`（对象类型） API 管理响应式状态 。

**组件通信升级**

**`<script setup>`** **语法糖**：

* 顶层变量自动暴露给模板，无需 `return` 。

* 编译器宏 `defineProps`/`defineEmits` 在编译时处理，无运行时开销 。

* 默认封闭组件实例，需 `defineExpose` 暴露方法给父组件调用 。

**Fragment 支持**：组件可返回多根节点，减少嵌套 wrapper 层 。

<br />

### Computed和Watch的区别

🧮**Computed**

* 计算属性，依赖其他属性，缓存，只在依赖变化时重新计算

* 适合用于计算值，如格式化数据、过滤列表等

👁️**Watch**

* 观察者，监听数据变化，执行异步或开销大的操作

* 适合用于响应数据变化执行副作用，如 Ajax 请求、DOM 操作等

**场景**

* **Computed**：数值计算，缓存

* **Watch**：异步操作，频率限制

<br />

### v-for和v-if同时使用问题

**问题**

* **v-for 优先级高于 v-if**：每次遍历都会执行 v-if 判断，影响性能。

* **避免同时使用**：将 v-if 放在 v-for 外层，或者用 computed 替换。

**⚠️ 注意：v-for 必须为 item 添加 key，方便 Vue.js 内部机制精准找到列表数据。**

### 生命周期

![1.00](https://cn.vuejs.org/assets/lifecycle_zh-CN.W0MNXI0C.png "组件生命周期图示")

<br />

<br />

**Composition API 使用示例**

```

import {
  onBeforeMount,
  onMounted,
  onBeforeUpdate,
  onUpdated,
  onBeforeUnmount,
  onUnmounted,
  onActivated,
  onDeactivated
} from 'vue';

export default {
  setup() {
    // 组件挂载阶段
    onBeforeMount(() => {
      console.log('DOM 挂载前');
    });

    onMounted(() => {
      console.log('DOM 挂载完成');
      // 实际应用：初始化图表、地图等第三方库
      initChart();
    });

    // 组件更新阶段
    onBeforeUpdate(() => {
      console.log('数据变化，DOM 更新前');
    });

    onUpdated(() => {
      console.log('数据变化，DOM 更新后');
      // 实际应用：更新第三方库
      updateChart();
    });

    // 组件卸载阶段
    onBeforeUnmount(() => {
      console.log('组件卸载前');
      // 实际应用：清除定时器
      clearInterval(timer);
    });

    onUnmounted(() => {
      console.log('组件卸载完成');
      // 实际应用：销毁第三方库实例
      destroyChart();
    });

    // KeepAlive 相关
    onActivated(() => {
      console.log('组件被激活');
      // 实际应用：恢复播放器状态
      video.play();
    });

    onDeactivated(() => {
      console.log('组件被停用');
      // 实际应用：暂停播放器
      video.pause();
    });
  }
};

```

### 性能优化

**一、路由懒加载（import ()）**

* 减小应用初始打包体积，提升首屏加载速度，避免加载未访问的路由代码（仅在访问该路由时才加载对应模块），无需额外依赖，路由配置中用 `import()` 即可

**二、组件异步（defineAsyncComponent）**

* 针对非首屏组件，条件渲染的组件（如弹窗、详情组件），支持加载状态 / 错误处理，需用 `defineAsyncComponent` 包裹。

**三、图片懒加载（v-lazy）**

* 需安装 `vue3-lazyload`，给`<img>`标签绑定`v-lazy`指令，指定图片地址，支持占位图、加载状态配置

**四、<keep-alive>** **缓存**

* **`<keep-alive>`**：包裹路由组件或普通组件，可通过`include/exclude`指定缓存范围，`max`限制缓存数量，减少组件重复渲染、生命周期钩子重复执行的开销，提升返回页面（如列表页→详情页→列表页）的响应速度。

<br />

### 状态管理：从Vuex到Pinia

Actions 支持直接同步/异步修改状态，结合 DevTools 可追踪变更；移除 Mutations 减少模板代码

```
// Pinia Store 定义（组合式 API）
import { defineStore } from 'pinia'
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  const doubleCount = computed(() => count.value * 2)
  function increment() { count.value++ }
  return { count, doubleCount, increment }
})
```

```
// Vuex Module 定义（对比）
export default {
  state: () => ({ count: 0 }),
  getters: { doubleCount: state => state.count * 2 },
  mutations: { INCREMENT(state) { state.count++ } },
  actions: { increment({ commit }) { commit('INCREMENT') } }
}
```

<br />

## SE6

### Promise

**`Promise`是一个构造函数，接收一个函数作为参数，返回一个 Promise 实例**

**Promise的核心优势**

* 可读性：代码结构清晰，易于理解

* 可维护性：错误处理集中，便于维护

* 组合性：可以轻松组合多个异步操作

* 状态管理：明确的pending/fulfilled/rejected状态

**特点**

1.状态不受外界影响

* 只有异步操作的结果可以决定当前是哪一种状态

2.状态一旦改变，永不回头

* pending -> fulfilled或rejected

* 任何时候都可以获取最终结果

**缺点**

🚫1. 无法取消

新建后立即执行，无法中途停止。

⚠️2. 错误处理

未设置回调时，内部错误不会反映到外部。

❓3. 进度未知

pending 状态下，无法得知当前进展。

### async和await

* `async`：标记函数为异步，返回 Promise。

* `await`：暂停 `async` 函数执行，等待 Promise 完成，返回结果或抛出错误。

* 核心优势：异步代码同步化，可读性强，错误处理更直观。

* 关键技巧：串行用 `await`，并行用 `Promise.all()`/`Promise.allSettled()`，错误用 `try/catch` 或 `.catch()`。

  <br />

```
async function conditionalTask() {
  const user = await fetchUser(); // 获取用户信息
  if (user.isVIP) {
    const vipData = await fetchVIPData(); // 仅 VIP 用户执行
    console.log("VIP 数据：", vipData);
  } else {
    const normalData = await fetchNormalData(); // 普通用户执行
    console.log("普通数据：", normalData);
  }
}
```
<br/>

```
async function simpleDemo() {
  try {
    // 尝试执行可能出错的异步操作
    const data = await fetchSimpleData(false); // 传 false 模拟失败
    console.log("成功：", data); // 成功才会执行
  } catch (error) {
    // 捕获所有错误并处理
    console.log("失败：", error.message); // 失败时输出错误信息
  }
}
```

### 宏任务和微任务有哪些？执行顺序

**宏任务**：script，setTimeout，setInterval。

**微任务**：Promise，process.nextTick。

**微任务会优先于宏任务执行。这意味着在当前任务执行结束后，所有微任务都会被立即执行，而宏任务只有在所有微任务执行完毕后才会执行。**

### 箭头函数

* 箭头函数实现了一种更加简洁的书写方式。

* 箭头函数内部没有arguments，也没有prototype属性，所以不能用new关键字调用箭头函数。

* 箭头函数和普通函数最大的区别在于其内部this永远指向其父级对象的this。(重点)

### var let const的区别

* var声明的变量存在变量提升，即变量可以在声明之前调用，var允许重复声明变量var不存在块级作用域

* let和const不存在变量提升，即它们所声明的变量一定要在声明后使用，否则报错

* let和const存在块级作用域

* let和const在同一作用域不允许重复声明变量

### ES6新特性

模板字符串，箭头函数，拓展运输符，map和set，promise和proxy，数组方法Array.from()，map()、filter()，forEach()，some()、every()

## `网络原理`

### 浏览器输入url

* 先进行DNS域名解析，查看本地hosts文件，查看有没有当前域名对应的ip地址，若有直接发起请求，没有的话会在本地域名服务器去查找，没找到的话就去根服务器查找最后查找到对应的ip地址后把对应规则保存到本地的hosts文件中。

* 进行http请求，三次握手四次挥手建立断开连接

* 服务器处理，可能返回304也可能返回200

* 客户端自上而下执行代码渲染页面

### TCP协议和HTTP 协议

* TCP协议在建立过程中会进行三次握手确保双方同步并避免无效连接，四次挥手则正常终止连接或异常终止连接。

* HTTP协议是超文本传输协议，是用于从万维网服务器传输超文本到本地浏览器的传送协议。HTTP是一个基于TCP/IP通信协议来传递数据的。

### HTTP和HTTPS的区别

HTTP 是明文传输，数据未加密，容易被窃取或篡改。而 HTTPS 使用 SSL/TLS 协议对数据进行加密，确保传输过程中的数据安全性和完整性。

### 解决前端跨域问题

JSONP跨域，CORS，nginx代理跨域，nodejs中间件代理跨域，WebSocket协议跨域

### WebSocket和webwork

WebSocket作用是即使通信双向通信，webwork是多进程的

<br />

## 性能优化

### 前端优化

🌐**HTTP 缓存**

服务器端设置，传输时使用。

💾**浏览器缓存**

前端 JS 设置，本地存储。

**目的**：优化性能，减少延迟，节省带宽。

**类型**：

* **强制缓存**：直接使用缓存，不请求服务器。

* **协商缓存**：请求服务器，验证缓存是否有效。

  <br />

### 防抖和节流

⏱️**防抖 (debounce)**

触发事件后 n 秒内只执行一次，后续触发重新计算时间。

* **非立即执行版**：n 秒后执行，期间再次触发重置计时。

* **立即执行版**：立即执行，n 秒内不触发才继续执行。

🔄**节流 (throttle)**

连续触发事件，n 秒内只执行一次。

* **时间戳版**：记录上一次执行时间，与当前时间差大于 n 秒才执行。

* **定时器版**：设置定时器，定时执行，期间再次触发不重置定时器。

<br />

防抖和节流是前端开发中常用的优化技术，用于控制事件触发频率，提高性能。
