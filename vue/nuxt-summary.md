---
description: 公司项目需要用到nuxt.js的服务端渲染，所以使用了nuxt.js官方的脚手架搭建了项目，在这儿记录一些搭建过程中踩过的坑。
---

# Nuxt.js项目搭建配置踩坑

\[TOC\]

## IE9的适配问题

IE9是一个老大难的问题，因为它不支持ES6的语法，而且ie9也不支持路由中的history模式，所以我在这儿暂时的解决办法如下。[文档&gt;&gt;](https://zh.nuxtjs.org/api/configuration-router/)

```javascript
// nuxt.config.js 
  /*
   ** Global router middleware
   */
  router: {
    mode: 'history',
    middleware: 'global',
    fallback: true 
  },
```

先在设置一下fallback，具体不知道为什么看文档，最重要的是设置babel-polyfill。

```text
yarn add babel-polyfill --dev
```

```javascript
// plugin 下 新建babel-polyfill文档下的index.js
import 'babel-polyfill'
```

```javascript
// nuxt.config.js
  plugins: [
    { src: '@/plugins/babel-polyfill', ssr: true } // 将es6转换为es5 兼容ie9
  ],
```

```javascript
// 在文档根目录下新建 .babelrc 文件
{
  "presets": [
    [
      "env",
      {
        "modules": false,
        "useBuiltIns": "entry"
      }
    ],
    "stage-3"
  ]
}
```

至此，项目就能在ie9上运行起来了，可能开发中还会有问题，到时候会进行补充。

## 全局样式变量的引入和全局样式的引入

因为开发时项目使用的css预编译器是scss，所以注册全局的样式变量如下。

先安装scss预编译器

```text
npm i node-sass sass-loader scss-loader --save-dev
```

```javascript
  // nuxt.config.js 中配置 styleResources
  css: [
    {
      src: '~/assets/styles/index.scss',
      lang: 'scss'
    }
  ],
  modules: [
    // Doc: https://axios.nuxtjs.org/usage
    '@nuxtjs/axios',
    '@nuxtjs/pwa',
    '@nuxtjs/eslint-module',
    '@nuxtjs/style-resources'
  ],
  build: {
    transpile: [/^element-ui/],
    styleResources: {
      scss: './assets/styles/variable/index.scss'
    },
    /*
     ** You can extend webpack config here
     */
    extend(config, ctx) {}
  }
```

## Vue 全局注入函数和属性值

有时您希望在整个应用程序中使用某个函数或属性值，此时，你需要将它们注入到Vue实例（客户端），context（服务器端）甚至 store\(Vuex\)。按照惯例，新增的属性或方法名使用`$`作为前缀。

其实这个[官方文档](https://zh.nuxtjs.org/guide/plugins)有，但是我还是再写一遍加深一遍记忆。

比如说我想全局使用我定义的接口路径。

```javascript
// urls -> index.js (PS:一定要写在plugins文件目录下)

import Vue from 'vue'
import dev from './dev'
import prod from './prod'
// 判断是生产环境还是开发环境
const env = process.env.NODE_ENV
const serverIp = env === 'production' ? prod.prodIp : dev.devIp
const interfacePORT =
  env === 'production'
    ? `${prod.prodInterfacePort}${
        prod.prodName === '' ? '' : '/' + prod.prodName
      }`
    : `${dev.devInterfacePort}${dev.devName === '' ? '' : '/' + dev.devName}`

const serverUrl = 'http://' + serverIp + '/'
const interfaceUrl = 'http://' + serverIp + ':' + interfacePORT + '/'

// 同时注入context 和 Vue中，在Vue中会自动加上在前面加上$ 
export default ({ app }, inject) => {
  inject('serverUrl', serverUrl)
  inject('interfaceUrl', interfaceUrl)
}
```

```javascript
// nuxt.config.js
  plugins: [
    { src: '@/config/url', ssr: false }, // 接口地址的全局引入
  ],
```

然后就可以在项目中全局引入

```javascript
export default {
  mounted(){
    console.log(this.$interfaceUrl)
  }
  asyncData(context){
    console.log(context.app.$interfaceUrl)
  }
}
```

## Vue 全局注册组件

​ 首先，先创建一个\*.vue文件，我的文件名叫`MyViewer.vue`，然后在全局注册这个组件，代码如下。

```javascript
// 文件目录为 src/components/common/index.js
import MyViewer from './MyViewer'

const globalCom = function(Vue) {
  if (globalCom.installed) return
  globalCom.installed = true
  Vue.component('MyViewer', MyViewer)
}

export default globalCom
```

​ 然后，在nuxt.js中的plugins文件目录下创建文件夹`common-components`用于在nuxt.config.js中注册，代码如下。

```javascript
// 文件目录为 src/plugins/common-components/index.js
import Vue from 'vue'
import globalCom from '@/components/common'
Vue.use(globalCom)
```

​ 最后，在nuxt.config.js中引用它，代码如下。

```javascript
  /*
   ** Plugins to load before mounting the App
   * 在这里注册插件
   */
  plugins: [
    { src: '@/plugins/common-components', ssr: false } // 引入全局公共组件
  ],
```

先写这么多，以后随着项目进度继续更新。

## 使用vue-router的导航守卫，判断权限

因为nuxt.js是在服务端渲染的，所以无法获取到window对象，所以无法使用sessionStorage等浏览器属性，然后我就将token存放在了cookies里面，但是我需要在导航守卫中去对token进行写入或者删除操作，我虽然在项目中引入了`vue-cookies`这种插件，但是在导航守卫中是获取不到的，所以，我引入了另外一个插件`cookie-universal-nuxt`，然后我就可以在导航守卫中获取和操作cookie了。具体引入方法和api详见[文档](https://www.npmjs.com/package/cookie-universal-nuxt)

