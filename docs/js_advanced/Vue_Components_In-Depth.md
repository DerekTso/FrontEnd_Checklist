# Javascript高阶 - Vue_Components_In-Depth

## 目录
- [事件名称 (event names)](#事件名称-(event-names))
- [定制组件 v-model](#定制组件-v-model)
- [slot](#slot)
- [动态组件](#动态组件)
- [异步组件](#异步组件)

### 事件名称 (event names)

1. 触发的事件名称必须与监听时的名称完全匹配
2. DOM 模板中的 ```v-on``` 事件监听器会自动转换为小写（这是因为 HTML 属性名称不区分大小写）
3. 建议使用串联式命名 (kebab-cased) 来命名事件名称
```
this.$emit('my-event')
<my-component v-on:my-event="doSomething"></my-component>
```

### 定制组件 v-model

1. 在一个组件中，```v-model``` 默认使用 ```value``` 作为 prop，以及默认使用 ```input``` 作为监听事件
2. 然而，对于某些类型的 input 元素（例如 checkbox 和 radio），由于这些类型的 input 元素本身具有 不同用法，可能会占用 ```value``` 特性
3. 在这种情况下，使用组件的 ```model``` 选项可以避免冲突
```
Vue.component('base-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean
  },
  template: `
    <input
      type="checkbox"
      v-bind:checked="checked"
      v-on:change="$emit('change', $event.target.checked)"
    >
  `
})
```
```
// 现在，在这个组件中使用 v-model：
<base-checkbox v-model="lovingVue"></base-checkbox>
```
4. ```lovingVue``` 的值就会传递给 ```checked``` prop
5. 当 ```<base-checkbox>``` 内部触发一个 ```change``` 事件，并且传递一个新值，```lovingVue``` 属性就会进行更新
6. 仍然需要在组件 ```props``` 选项中声明 ```checked``` prop 属性

### slot

1. 命名插槽 (named slot)
```
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>

<base-layout>
  <h1 slot="header">这里是一个页面标题</h1>

  <p>main 内容的一个段落。</p>
  <p>main 内容的另一个段落。</p>

  <p slot="footer">这里是一些联系信息</p>
</base-layout>

// 最终渲染的 HTML 是：
<div class="container">
  <header>
    <h1>这里是一个页面标题</h1>
  </header>
  <main>
    <p>main 内容的一个段落。</p>
    <p>main 内容的另一个段落。</p>
  </main>
  <footer>
    <p>这里是一些联系信息</p>
  </footer>
</div>
```
2. 默认插槽内容 (default slot content)
```
<button type="submit">
  <!-- 默认内容 Submit -->
  <slot>Submit</slot>
</button>
```
3. 作用域插槽 (scoped slots)
```
// 一个简单的 <todo-list> 组件
// 将 todo item 的内容，包裹到一个 <slot> 元素中

<ul>
  <li
    v-for="todo in todos"
    v-bind:key="todo.id"
  >
    <!-- 我们为每个 todo 提供一个 slot 元素， -->
    <!-- 然后，将 `todo` 对象作为 slot 元素的一个 prop 传入。 -->
    <slot v-bind:todo="todo">
      <!-- 这里是回退内容(fallback content) -->
      {{ todo.text }}
    </slot>
  </li>
</ul>

// 在我们引用 <todo-list> 组件的位置，将 todo items 插槽内容稍作修改
// 定义为一个 <template>，并且通过 slot-scope 特性访问子组件数据

<todo-list v-bind:todos="todos">
  <!-- 将 `slotProps` 作为插槽内容所在作用域(slot scope)的引用名称 -->
  <template slot-scope="slotProps">
    <!-- 为 todo items 定义一个模板， -->
    <!-- 通过 `slotProps` 访问每个 todo 对象。 -->
    <span v-if="slotProps.todo.isComplete">✓</span>
    {{ slotProps.todo.text }}
  </template>
</todo-list>

// 或者可以使用 ES2015 解构 来对表达式进行解构

<todo-list v-bind:todos="todos">
  <template slot-scope="{ todo }">
    <span v-if="todo.isComplete">✓</span>
    {{ todo.text }}
  </template>
</todo-list>
```

### 动态组件

1. 使用 ```is``` 特性，在标签页式界面中切换组件
```
<component v-bind:is="currentTabComponent"></component>
```
2. 有时在这些组件之间切换，需要保留切换前的状态，或者需要考虑重新渲染造成的性能问题，而尽量避免重新渲染
3. 可以使用一个 ```<keep-alive>``` 元素将动态组件包裹起来
```
<!-- Inactive components will be cached! -->
<keep-alive>
  <component v-bind:is="currentTabComponent"></component>
</keep-alive>
```

### 异步组件

1. 为了简化异步按需加载组件机制，Vue 能够将组件定义为一个工厂函数 (factory function)
2. 此函数可以异步地解析 (resolve) 组件定义对象 (component definition)
3. Vue 只在真正需要渲染组件时，才会去触发工厂函数，并且将解析后的结果缓存，用于将来再次渲染
```
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    // 将组件定义对象(component definition)传递给 resolve 回调函数
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```
4. 使用异步组件，比较推荐的方式是配合 webpack 代码分离功能
```
Vue.component('async-webpack-example', function (resolve) {
  // 这个特殊的 require 语法
  // 将指示 webpack 自动将构建后的代码，
  // 拆分到不同的 bundle 中，然后通过 Ajax 请求加载。
  require(['./my-async-component'], resolve)
})
```
5. 还可以在工厂函数中返回一个 ```Promise```，所以配合 webpack 2 + ES2015 语法
```
Vue.component(
  'async-webpack-example',
  // `import` 函数返回一个 Promise.
  () => import('./my-async-component')
)
```
6. 当使用 局部注册 时，也可以直接提供一个返回 ```Promise``` 的函数
```
new Vue({
  // ...
  components: {
    'my-component': () => import('./my-async-component')
  }
})
```
7. 异步组件工厂函数，还可以返回一个以下格式的对象
```
const AsyncComponent = () => ({
  // 加载组件（最终应该返回一个 Promise）
  component: import('./MyComponent.vue'),
  // 异步组件加载中(loading)，展示为此组件
  loading: LoadingComponent,
  // 加载失败，展示为此组件
  error: ErrorComponent,
  // 展示 loading 组件之前的延迟时间。默认：200ms。
  delay: 200,
  // 如果提供 timeout，并且加载用时超过此 timeout，
  // 则展示错误组件。默认：Infinity。
  timeout: 3000
})
```
