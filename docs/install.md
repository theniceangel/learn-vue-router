# 安装

Vue-Router 作为 Vue 官方插件，安装过程也是通过如下方法：

```js
import VueRouter from 'vue-router'
import Vue from 'vue'

Vue.use(VueRouter)
```

`Vue.use` 内部其实就是执行了 `VueRouter.install`([原理分析](https://theniceangel.github.io/learn-vuex/analyze/vuex.html#%E6%80%BB%E7%BB%93))。先看下 `install` 的定义，位于 `vue-router/src/install.js`。

```js
export function install (Vue) {
  // 确保只插件只安装一次
  if (install.installed && _Vue === Vue) return
  install.installed = true

  _Vue = Vue

  const isDef = v => v !== undefined

  // 如果 router-view 匹配到了路由，挂载路由对应的组件实例至当前匹配到的路由记录的 instances 对象上
  // 这样做是为了执行组件内的 `beforeRouteUpdate`、`beforeRouteLeave` 导航守卫的时候能绑定上下文
  const registerInstance = (vm, callVal) => {
    let i = vm.$options._parentVnode
    if (isDef(i) && isDef(i = i.data) && isDef(i = i.registerRouteInstance)) {
      i(vm, callVal)
    }
  }

  // 混入 beforeCreate 与 destroyed 生命周期，使得所有组件执行的时候，都会执行这两个钩子
  Vue.mixin({
    beforeCreate () {
      // 实例化 vm 传了 router 实例，一般根 Vue 会传，当然也可以子组件传 router
      if (isDef(this.$options.router)) {
        this._routerRoot = this
        this._router = this.$options.router
        // 调用 router 实例的 init方法
        this._router.init(this)
        // 将 _route 做成响应式数据，指向的是当前路由
        Vue.util.defineReactive(this, '_route', this._router.history.current)
      } else {
        // 没有传入 router 的子组件实例化的时候，就将 _routerRoot 指向最近的传了 router 的祖先组件实例
        this._routerRoot = (this.$parent && this.$parent._routerRoot) || this
      }
      // 将当前组件实例绑定到匹配到的路由记录上去
      registerInstance(this, this)
    },
    destroyed () {
      debugger
      registerInstance(this)
    }
  })
  // 访问 vm.$router 代理到 this._routerRoot 的 router 实例
  Object.defineProperty(Vue.prototype, '$router', {
    get () { return this._routerRoot._router }
  })

  // 访问 $route 代理到 响应式 _route 数据上
  Object.defineProperty(Vue.prototype, '$route', {
    get () { return this._routerRoot._route }
  })

  // 注册全局组件
  Vue.component('RouterView', View)
  Vue.component('RouterLink', Link)

  const strats = Vue.config.optionMergeStrategies

  // use the same hook merging strategy for route hooks
  // 定义 beforeRouteEnter， beforeRouteLeave， beforeRouteUpdate 的钩子合并策略
  // 合并策略是如果有不同的 beforeRouteEnter 回调函数，会将其推入到一个队列，依次执行。
  strats.beforeRouteEnter = strats.beforeRouteLeave = strats.beforeRouteUpdate = strats.created
}
```

我们可以看到安装 `Vue-Router` 的过程，其实就是通过 `Vue.mixin` 混入了 `beforeCreate`、`destroyed` 这两个生命周期的钩子。在前者的函数里面主要是初始化调用 router 的 init 方法，这个我们等下分析，并且将 `_route` 做成响应式数据，并且将 `$route` 的访问代理到 `_route`，这样只要对 `$route` 属性进行了求值的话， `_route` 改变了就会重新渲染视图。最后就是定义全局组件，能让我们在所有组件都使用 `router-view` 与 `router-link`。