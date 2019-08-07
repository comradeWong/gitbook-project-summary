---
description: 列举一些开发项目时常用的技术问题
---

# 前端技术总结

## Vue

### **vue生命周期总共有几个阶段？**

它可以总共分为8个阶段：创建前/后, 载入前/后,更新前/后,销毁前/销毁后。

beforeCreate、created、beforeMounted、mounted、beforeUpdated、updated、beforeDestroy、destroyed. 

### MVVM框架是什么？它和其它框架（Jquery）的区别是什么？哪些场景适合？

MVVM分为Model、View、ViewModel三者

* **Model** 代表数据模型，数据和业务逻辑都在Model层中定义
* **View** 代表UI视图，负责数据展示
* **ViewModel** 负责监听 **Model** 中数据的改变并且控制视图更新，处理用户交互操作：

**Model** 和 **View** 并无直接关联，而是通过 **ViewModel** 来进行联系的， **Model** 和 **ViewModel** 之间有着双向数据绑定的联系。因此当 **Model** 中的数据改变时会触发 **View** 层的刷新，**View** 中由于用户交互操作而改变的数据也会在 **Model** 中同步。

区别：这种模式实现了 Model 和 View的数据自动同步，因此开发时这需要要专注对数据的维护操作即可，而不需要自己操作dom 场景。

数据操作比较多的场景，更加便捷。

### **是否了解过虚拟DOM，其有什么优势？v-for 中的 key 属性的作用？**

虚拟 DOM 为这React、Vue两个框架都带来了跨平台的能力。（React-Native 和 Weex）

虚拟 DOM 最大的优势是 diff 算法，减少 JavaScript 操作真实 DOM 的带来的性能消耗。

虚拟 DOM 最大的优势在于抽象了原本的渲染过程，实现了跨平台的能力，而不仅仅局限于浏览器的 DOM，可以是安卓和 IOS 的原生组件，可以是近期很火热的小程序，也可以是各种GUI。

v-for中的key用于对虚拟dom进行唯一性标识，优化渲染速度。



### **vue 数组更新后不渲染，为什么？怎么解决？\(数组更新检测\)**

由于 JavaScript 的限制， Vue 不能检测以下变动的数组：

javascript 当你利用索引直接设置一个项时， 例如： vm.items\[indexOfItem\] = newValue

以下两种方式都可以实现和 vm.items\[indexOfItem\] = newValue 相同的效果， 同时也将触发状态更新： 

// Vue.set Vue.set\(example1.items, indexOfItem, newValue\)

// Array.prototype.splice\` example1.items.splice\(indexOfItem, 1, newValue\)

```text
  变异方法

  Vue 将被侦听的数组的变异方法进行了包裹，所以它们也将会触发视图更新。这些被包裹过的方法包括：

  - `push()`
  - `pop()`
  - `shift()`
  - `unshift()`
  - `splice()`
  - `sort()`
  - `reverse()`

  你可以打开控制台，然后对前面例子的 `items` 数组尝试调用变异方法。
  比如 `example1.items.push({ message: 'Baz' })`。
```

### vue中对象如何动态添加根级别的响应式属性？\(对象更新检测\)

```javascript
  var vm = new Vue({
    data: {
      userProfile: {
        name: 'Anika'
      }
    }
  })
```

你可以添加一个新的 `age` 属性到嵌套的 `userProfile` 对象：

```javascript
  Vue.set(vm.userProfile, 'age', 27)
```

你还可以使用 `vm.$set` 实例方法，它只是全局 `Vue.set` 的别名：

```javascript
  vm.$set(vm.userProfile, 'age', 27)
```

有时你可能需要为已有对象赋值多个新属性，比如使用 `Object.assign()` 或 `_.extend()`。在这种情况下，你应该用两个对象的属性创建一个新的对象。所以，如果你想添加新的响应式属性，不要像这样：

```javascript
  Object.assign(vm.userProfile, {
    age: 27,
    favoriteColor: 'Vue Green'
  })
```

你应该这样做：

```javascript
  vm.userProfile = Object.assign({}, vm.userProfile, {
    age: 27,
    favoriteColor: 'Vue Green'
  })
```



### **是否使用过Vue Cli工具？版本是2还是3？它们二者之间最大的区别是什么？它们创建项目的语法分别是什么？是否使用过vue ui可视化图形工具？**

```text
Vue cli 2
vue init webpack <Project Name>
```

```text
  Vue cli 3
  vue create <Project Name>
```



### **之前是否使用过任何代码检测工具Eslint? Prettier?**

### **之前是否使用过任何的css预编译器？**

stylus、scss、sass、less

### **Vue项目中如何注册全局组件？**

```javascript
// commonCom/index.js
import DateRadioGroup from './DateRadioGroup'

const globalCom = {
  install: function(Vue) {
    Vue.component('DateRadioGroup', DateRadioGroup)
  }
}

export default globalCom
```

```javascript
// main.js
import globalCom from './commonCom' // 引入全局组件
Vue.use(globalCom)
```

### Vue项目中怎么注册一个组件并引用？

### **Vue项目中如何注册全局方法？**

```javascript
/**
 * @author: WangXinYu
 * @describe: 深拷贝对象
 * @param deep 是否深拷贝 obj 对象 或是 数组
 * @create: 2019/5/27 14:27
 **/
function deepCopy(obj, deep) {
  var o = obj instanceof Array ? [] : {}
  for (var k in obj) {
    var val = obj[k]

    if (deep && typeof val === 'object') {
      o[k] = deepCopy(val, deep)
    } else {
      o[k] = val
    }
  }
  return o
}

const utils = {
  install: function(Vue) {
    Vue.prototype.$deepCopy = deepCopy // 深拷贝对象
  }
}

export default utils
```

```javascript
// main.js
import utils from '@/utils'
Vue.use(utils)
```

### **Vue如何定义公共的css类**

需要引入预编译器，scss、stylus、less等，在main.js中import相应文件即可。

### **是否在项目中使用过Vue Router？如果使用过，是否了解导航守卫？那么导航中守卫有哪几种？项目中常用的全局前置守卫，你们原先的项目主要用来做什么？**

用过，知道，在项目中主要用于从store中获取token进行页面的权限判断。

1. 全局前置守卫（beforeEach）
2. 全局解析守卫（beforeResolve）**同时在所有组件内守卫和异步路由组件被解析之后**，解析守卫就被调用。
3. 全局后置钩子（afterEach）
4. 路由独享守卫 （beforeEnter）
5. 组件内的守卫

   ```javascript
   const Foo = {
     template: `...`,
     beforeRouteEnter (to, from, next) {
       // 在渲染该组件的对应路由被 confirm 前调用
       // 不！能！获取组件实例 `this`
       // 因为当守卫执行前，组件实例还没被创建
     },
     beforeRouteUpdate (to, from, next) {
       // 在当前路由改变，但是该组件被复用时调用
       // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
       // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
       // 可以访问组件实例 `this`
     },
     beforeRouteLeave (to, from, next) {
       // 导航离开该组件的对应路由时调用
       // 可以访问组件实例 `this`
     }
   }
   ```

### Vue Router 传参 params和query有什么区别？

1.params只能用name来引入路由，query用path来引入 

2.params类似于post，query更加类似于我们ajax中get传参，说的再简单一点，前者在浏览器地址栏中不显示参数，后者显示，所以params传值相对安全一些。

 3.取值用法类似分别是this.$route.params.name和this.$route.query.name。

 4.params传值一刷新就没了，query传值刷新还存在。  

如果想让params 不要一刷新就没了怎么办？路由path后加 /:id/:name

### **是否在项目中使用过Vuex?使用过，是否使用将store模块化过？**

```javascript
// store/index.js
export default new Vuex.Store({
  modules: {
    common,
    page,
    proConfig,
    device,
    savingOperation,
    projectManage
  },
  strict: debug,
  plugins: debug ? [createLogger()] : []
})
```

```javascript
/**
 * @author: Wang.X.Y/comrade.wang.cn@gmail.com
 * @Date: 2018/11/22
 * @Time: 10:09
 * @Description: store/common/index.js
 */
const state = {
  token: sessionStorage.getItem('token') || '',
}
const getters = {
  get_token(state) {
    return state.token
  }
}
const mutations = {
  set_token(state, token) {
    sessionStorage.setItem('token', token)
    state.token = token
  }
}
const actions = {
  set_token(context, token) {
    context.commit('set_token', token)
  }
}

export default {
  namespaced: true,
  state,
  getters,
  actions,
  mutations
}
```

### vuex是什么？怎么使用？哪种功能场景使用它？ <a id="articleHeader7"></a>

只用来读取的状态集中放在store中； 改变状态的方式是提交mutations，这是个同步的事物； 异步逻辑应该封装在action中。  
在main.js引入store，注入。新建了一个目录store，….. export 。  
场景有：单页应用中，组件之间的状态、音乐播放、登录状态、加入购物车

**state**  
Vuex 使用单一状态树,即每个应用将仅仅包含一个store 实例，但单一状态树和模块化并不冲突。存放的数据状态，不可以直接修改里面的数据。  
**mutations**  
mutations定义的方法动态修改Vuex 的 store 中的状态或数据。  
**getters**  
类似vue的计算属性，主要用来过滤一些数据。  
**action**   
actions可以理解为通过将mutations里面处里数据的方法变成可异步的处理数据的方法，简单的说就是异步操作数据。view 层通过 store.dispath 来分发 action。

### Vue是一个SPA单页面应用，极其不利于SEO\(搜索引擎优化\)，请问怎么优化SEO?

* **SSR**\(Server Side Rendering\)**服务端渲染**
* 利用插件进行预渲染 （prerender-spa-plugin + vue-meta-info）

### Vue组件间的参数传递 <a id="articleHeader3"></a>

 **1.父组件与子组件传值**  
父组件传给子组件：子组件通过props方法接受数据;  
子组件传给父组件：$emit方法传递参数  
**2.非父子组件间的数据传递，兄弟组件传值**  
eventBus，就是创建一个事件中心，相当于中转站，可以用它来传递事件和接收事件。项目比较小时，用这个比较合适。（虽然也有不少人推荐直接用VUEX，具体来说看需求咯。技术只是手段，目的达到才是王道。）

## JavaScript

### 谈谈你对ECMAScript6的理解

ECMAScript6是ES2015标准；

* 新增了块级作用域\(let,const\)
* 提供了定义类的语法糖\(class\)
* 新增了一种基本数据类型\(Symbol\)
* 新增了变量的解构赋值
* 函数参数允许设置默认值，引入了rest参数，新增了箭头函数
* 数组新增了一些API，如 isArray / from / of 方法;数组实例新增了 entries\(\)，keys\(\) 和 values\(\) 等方法
* 对象和数组新增了扩展运算符
* ES6 新增了模块化\(import/export\)
* ES6 新增了 Set 和 Map 数据结构
* ES6 原生提供 Proxy 构造函数，用来生成 Proxy 实例
* ES6 新增了生成器\(Generator\)和遍历器\(Iterator\)

### 什么是深拷贝，什么是浅拷贝？怎么实现深拷贝？

变量存储类型分两类

①基本类型：直接存储在栈中的数据。（字符串、布尔值、未定义、数字、null）

②引用类型：将该对象引用地址存储在**栈**中，然后对象里面的数据存放在**堆**中。（数组、对象、函数）

这里解释一下为什么null是基本类型：有人说他用type of打印出来不是oject吗？

null只是一个空指针对象，没有数据。根据引用类型特点可以看一下是否符合。  
常用：使用JSON.parse\(JSON.stringify\(obj\)\)

浅拷贝：也就是拷贝A对象里面的数据，但是不拷贝A对象里面的子对象

深拷贝：会克隆出一个对象，数据相同，但是引用地址不同（就是拷贝A对象里面的数据，而且拷贝它里面的子对象）

赋值：获得该对象的引用地址

原理是把一个对象序列化成为一个JSON字符串，将对象的内容转换成字符串的形式再保存在磁盘上，再用JSON.parse\(\)反序列化将JSON字符串变成一个新的对象

缺点是: 会忽略undefined、symbol、funciton

实现思想：如果是一个数组，就声明一个数据组，然后循环遍历，递归赋值。 如果是一个对象，就声明一个对象，然后判断是否子元素,递归赋值。

### 请一句话概括一下什么是闭包。

一句话可以概括：闭包就是能够读取其他函数内部变量的函数，或者子函数在外调用，子函数所在的父函数的作用域不会被释放。

### Promise函数中的代码是立即执行的吗？Promise有哪三种状态？Promise的状态是否可逆？

是。

Promise的内部实现是一个状态机。Promise有三种状态：pending，resolved，rejected。当Promise刚创建完成时，处于pending状态；当Promise中的函数参数执行了resolve后，Promise由pending状态变成resolved状态；如果在Promise的函数参数中执行的不是resolve方法，而是reject方法，那么Promise会由pending状态变成rejected状态。

不可逆。Promise状态的一旦变成resolved或rejected时，Promise的状态和值就固定下来了，不论你后续再怎么调用resolve或reject方法，都不能改变它的状态和值。

### 是否了解async / await 函数？其是怎么实现异步回调的？

* async/await是写异步代码的新方式，以前的方法有**回调函数**和**Promise**。
* async/await是基于Promise实现的，它不能用于普通的回调函数。
* async/await与Promise一样，是非阻塞的。
* async/await使得异步代码看起来像同步代码，这正是它的魔力所在。

async 函数返回一个 Promise 对象，当函数执行的时候，一旦遇到 await 就会先返回，等到触发的异步操作完成，再接着执行函数体内后面的语句。

## HTML

### **请描述一下 cookies，sessionStorage 和 localStorage 的区别？**

cookie是网站为了标示用户身份而储存在用户本地终端（Client Side）上的数据（通常经过加密）。

* cookie数据始终在同源的http请求中携带（即使不需要），记会在浏览器和服务器间来回传递。
* sessionStorage和localStorage不会自动把数据发给服务器，仅在本地保存。

存储大小：

* cookie数据大小不能超过4k。
* sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大。

有期时间：

* localStorage 存储持久数据，浏览器关闭后数据不丢失除非主动删除数据；
* sessionStorage 数据在当前浏览器窗口关闭后自动删除。
* cookie 设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。

## CSS

