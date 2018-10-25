# Javascript高阶 - Vue_Plugins

## 目录
- [编写插件](#编写插件)
- [使用插件](#使用插件)

### 编写插件

常见的插件类型:
1. 添加一些全局方法或属性。例如 ```vue-custom-element```
2. 添加一个或多个全局资源(asset)：指令(directives)/过滤器(filters)/过渡(transitions) 等。例如 ```vue-touch```
3. 通过全局 mixin，添加一些组件选项。例如 ```vue-router```
4. 添加一些 Vue 实例方法，通过把这些方法添加到 ```Vue.prototype``` 上实现
5. 一个可以提供 API 的库(library)，与此同时也是以上功能的组合。例如 ```vue-router```

Vue.js 插件应该暴露一个 ```install``` 方法：
```
// 在调用时，将 Vue 构造函数作为第一个参数传入
// 以及将一个可选的选项作为第二个参数传入
MyPlugin.install = function (Vue, options) {
  // 1. 添加全局方法或属性
  Vue.myGlobalMethod = function () {
    // 一些逻辑……
  }

  // 2. 添加一个全局资源(asset)
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // 一些逻辑……
    }
    ...
  })

  // 3. 注入一些组件选项
  Vue.mixin({
    created: function () {
      // 一些逻辑……
    }
    ...
  })

  // 4. 添加一个实例方法
  Vue.prototype.$myMethod = function (methodOptions) {
    // 一些逻辑……
  }
}
```

### 使用插件

Vue.use 会自动阻止多次使用同一个插件，所以对于同一个插件的多次调用，将只安装一次
```
通过调用全局方法 Vue.use() 使用插件：
// 调用 `MyPlugin.install(Vue)`
Vue.use(MyPlugin)

可以根据情况，传入一些可选的选项：
Vue.use(MyPlugin, { someOption: true })
```

```
// Vue.js 官方提供的一些插件（例如 vue-router），如果检测到 Vue 是可访问的全局变量，这些插件会自动调用 Vue.use()
if (inBrowser && window.Vue) {
  window.Vue.use(VueRouter)
}

// 然而在例如 CommonJS 的模块环境中，你应该始终显式地调用 Vue.use()
// 在使用由 Browserify 或 webpack 这些模块打包工具，提供的 CommonJS 模块环境时
var Vue = require('vue')
var VueRouter = require('vue-router')

// 不要忘记调用此方法
Vue.use(VueRouter)
```
