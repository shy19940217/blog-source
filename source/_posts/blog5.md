---
title: vue常用配置以及用法总结
date: 2018-03-29 11:26:03
tags: vue
---

## vue常见问题

### polyfill 与 transform-runtime

首先，vue-cli 为我们自动添加了 babel-plugin-transform-runtime 这个插件，该插件多数情况下都运作正常，可以转换大部分 ES6 语法。

但是，存在如下两个问题：

异步加载组件时，会产生 polyfill 代码冗余
不支持对全局函数与实例方法的 polyfill

两个问题的原因均归因于 babel-plugin-transform-runtime 采用了沙箱机制来编译我们的代码（即：不修改宿主环境的内置对象）

由于异步组件最终会被编译为一个单独的文件，所以即使多个组件中使用了同一个新特性（例如：Object.keys()），那么在每个编译后的文件中都会有一份该新特性的 polyfill 拷贝。如果项目较小可以考虑不使用异步加载，但是首屏的压力会比较大。

　　不支持全局函数（如：Promise、Set、Map），Set 跟 Map 这两种数据结构应该大家用的也不多，影响较小。但是 Promise 影响可能就比较大了。

　　不支持实例方法（如：'abc'.includes('b')、['1', '2', '3'].find((n) => n < 2) 等等），这个限制几乎废掉了大部分字符串和一半左右数组的新特性。

一般情况下 babel-plugin-transform-runtime 能满足大部分的需求，当不满足需求时，推荐使用完整的 babel-polyfill。

### 响应式数据失效

数组

　　由于 Vue.js 响应式数据依赖于对象方法 Object.defineProperty。但很明显，数组这个特殊的“对象”并没有这个方法，自然也无法设置对象属性的 descriptor，从而也就没有 getter() 和 setter() 方法。所以在使用数组索引角标的形式更改元素数据时（arr[index] = newVal），视图往往无法响应式更新。
　　为解决这个问题，Vue.js 中提供了 $set() 方法：
```javascript
vm.arr.$set(0, 'newVal')
```

### 异步组件

使用处于 Stage.3 阶段的动态导入函数 import()，同时借助 webpack 的代码分割功能，在 Vue.js 中我们可以很轻松地实现一个异步组件。

```javascript
 const AsyncComponent = () => import('./AsyncComponent')
```
### 常用类库

axios 调接口的类库
lodash 一些处理数据的方法类库
iview  vue的ui组件
jquery 一般来说尽量少用 不到万不得己就不要用了  太low
vuex  对数据就行状态管理

### 文件目录

component 放一些公用组件

views 放一些页面

api 放调接口的api

store 放状态管理的文件

router 放路由文件

libs 放一些工具库

### 全局引用组件和局部引用组件

全局引用组件
一般是建一个文件专门注册全局组件的
然后在main.js里直接引用
```javascript
import IconSvg from 'components/Icon-svg'
const install = Vue => {
    Vue.component('Icon', IconSvg)
    Vue.prototype.$alert = Alert
}
```
在页面上就可以直接用

局部组件可以直接引入无需配置

### store里的问题

一般而言，我们需要将store下的state放在computed中，将组件自身的state，不需要像vuex这样动态的、传递的放在 data 下即可。

store会在页面刷新时数据丢失

### 项目的登录拦截及用户权限访问控制问题
```javascript
 { 
        path: '/account', 
        meta: { requireAuth: true}, 
        component: accout
        name: '个人中心'
  }
router.beforeEach((to, from, next) =>{
    if(to.meta.requireAuth){ //是否需要登录拦截
        if(store.state.tokens.token){ //已登录
            next()
        }else{
            next({
                path: ‘/‘,
                query: {redirect : to.fullPath}
            })
        }
    }else{
        next()
    }
})
```
同理，用户权限的认证也可以这么做。另外需要注意的是，这个登录状态需要使用localstorage或者cookie保存，只存在store里面会导致页面一刷新登录状态就没了。

### 路由问题

在同一个路由下切换，取不到变化的路由参数

解决方案
```javascript
watch: {
  '$route' (to, from) {
  //这样就可以获取到变化的参数了，然后执行参数变化后相应的逻辑就行了
    console.log(this.$route.query)
  }
```
###  keep-alive的问题
```javascript
 { //home会被缓存
    path:"/home",
    component:home,
    meta:{keepAlive: true}
  }
  { //hello不会被缓存
    path:"/hello",
    component:hello,
    meta:{keepAlive: false}
  }
  <div id="app">
  <!--缓存想要缓存的页面，实现后退不刷新-->
  <!--加上v-if的判断，可以自定义想要缓存的组件，自定义在router里面-->
  <keep-alive>
   <router-view v-if="$route.meta.keepAlive"></router-view>
  </keep-alive>
  <router-view v-if="!$route.meta.keepAlive"></router-view>
 </div>
```
页面一旦缓存以后，vue的生命周期只有active可以用

### 计算属性

计算属性只会根据响应式数据变化，不会根据你本地存储(sessionstorage localstorage)的数据的变化而变化







