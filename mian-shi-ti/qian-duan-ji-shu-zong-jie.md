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

实现思想：如果是一个数组，就声明一个数据组，然后循环遍历，递归赋值。 如果是一个对象，就声明一个对象，然后判断是否子元素,递归赋值

