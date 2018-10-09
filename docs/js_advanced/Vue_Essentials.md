# Javascript高阶 - Vue_Essentials

## 目录
- [Installation](#installation)
- [Hello Vue!](#hello-vue)
- [v-bind 指令](#v-bind-指令)
- [v-if 指令](#v-if-指令)
- [v-else-if / v-else](#v-else-if-/-v-else)
- [使用 key 控制元素是否可复用](#使用-key-控制元素是否可复用)
- [v-if 和 v-show](#v-if-和-v-show)
- [带有 v-if 的 v-for](#带有-v-if-的-v-for)
- [使用 v-for 遍历组件](#使用-v-for-遍历组件)
- [修饰符 (Modifiers)](#修饰符-(Modifiers))
- [使用 JavaScript 表达式](#使用-JavaScript-表达式)
- [computed 缓存 vs method 方法](#computed-缓存-vs-method-方法)
- [computed 属性中设置 setter](#computed-属性中设置-setter)
- [computed 属性和 watch 属性](#computed-属性和-watch-属性)
- [v-model 指令](#v-model-指令)
- [组件 (component)](#组件-(component))
- [创建一个 Vue 实例](#创建一个-Vue-实例)
- [响应式系统](#响应式系统)
- [数组变化检测](#数组变化检测)
- [对象变化检测](#对象变化检测)
- [生命周期钩子函数](#生命周期钩子函数)
- [class 和 style 绑定](#class-和-style-绑定)
- [props](#props)
- [使用 events 向父组件发送消息](#使用-events-向父组件发送消息)
- [使用 slots 进行内容分发](#使用-slots-进行内容分发)
- [动态组件](#动态组件)
- [DOM 模板解析注意事项](#DOM-模板解析注意事项)

### Installation

```
npm install -g @vue/cli
vue --version
vue create hello-world

# Sass
npm install -D sass-loader node-sass

# Less
npm install -D less-loader less

# Stylus
npm install -D stylus-loader stylus

```

### Hello Vue!

```
// 文本插值: text interpolation
<div id="app">
  {{ message }}
</div>

var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```
1. 打开你的浏览器的 JavaScript 控制台，然后设置 ```app.message``` 的值，你将看到上面的示例所渲染的 DOM 元素会相应地更新。
2. 通过使用 ```v-once``` 指令，执行一次性插值，在数据改变时，插值内容不会随之更新，这也将影响到同一节点上的其他所有绑定
```<span v-once>这里的值永远不会改变：{{ msg }}</span>```

### v-bind 指令

```
<div id="app-2">
  <span v-bind:title="message">
    鼠标悬停此处几秒，
    可以看到此处动态绑定的 title！
  </span>
</div>

var app2 = new Vue({
  el: '#app-2',
  data: {
    message: '页面加载于 ' + new Date().toLocaleString()
  }
})
```
1. 指令的作用就是将元素的属性与 Vue 实例的 data 属性保持关联更新
2. 不能在 Vue 模板中的 HTML 属性上使用双花括号语法 (mustache)
3. 而是应该使用 ```v-bind``` 指令
```
<div v-bind:id="dynamicId"></div>
<button v-bind:disabled="isButtonDisabled">Button</button>
```
4. v-bind 简写
```
<!-- 完整语法 -->
<a v-bind:href="url"> ... </a>

<!-- 简写 -->
<a :href="url"> ... </a>
```

### v-if 指令

```
<div id="app-3">
  <span v-if="seen">现在你可以看到我</span>
</div>

var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```
1. 在控制台输入 ```app3.seen = false```，你应该会看到 span 消失


### v-else-if / v-else

1. 由于 ```v-if``` 是一个指令，因此必须将其附加到一个单独的元素上
2. 我们可以将 ```<template>``` 元素，作为多个元素的无形容器 (invisible wrapper)
3. 然后在这个容器上使用 ```v-if```。最终渲染结果将不会包含 ```<template>``` 元素
```
<template v-if="ok">
  <h1>标题</h1>
  <p>段落 1</p>
  <p>段落 2</p>
</template>
```
```
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  非 A/B/C
</div>
```

### 使用 key 控制元素是否可复用

```
<template v-if="loginType === 'username'">
  <label>用户名</label>
  <input placeholder="请输入用户名">
</template>
<template v-else>
  <label>邮箱</label>
  <input placeholder="请输入邮箱">
</template>
```
1. 切换 ```loginType``` 不会清除用户已经输入的内容
2. 这是由于两个模板使用的是相同的元素，所以 ```<input>``` 并不会被替换
3. 替换的只是元素的 ```placeholder```

```
<template v-if="loginType === 'username'">
  <label>用户名</label>
  <input placeholder="请输入用户名" key="username-input">
</template>
<template v-else>
  <label>邮箱</label>
  <input placeholder="请输入邮箱" key="email-input">
</template>
```
1. 这些 ```input``` 将会在每次切换时从头重新渲染
2. 因为它们添加了一个具有不同值的 ```key``` 属性
3. ```<label>``` 元素仍然被有效地复用，因为它们没有 ```key``` 属性

### v-if 和 v-show

```
<h1 v-show="ok">Hello!</h1>
```
1. 具有 ```v-show``` 的元素会始终渲染并保留在 DOM 中。```v-show``` 只会切换元素的 ```display``` 这个 CSS 属性
2. ```v-show``` 无法用于 ```<template>``` 元素，也不能和 ```v-else``` 配合使用
3. ```v-if``` 是惰性的：如果在初始渲染时条件为 false，它不会执行任何操作
4. 如果需要频繁切换，推荐使用 ```v-show```，如果条件在运行时改变的可能性较少，推荐使用 ```v-if```

### v-for 指令

```
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>

var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: '学习 JavaScript' },
      { text: '学习 Vue' },
      { text: '创建激动人心的代码' }
    ]
  }
})
```
1. 在控制台，输入 ```app4.todos.push({ text: '新的 item' })```，你会看到列表中追加了一个新的 item
2. ```v-for``` 还支持可选的第二个参数，作为当前项的索引
```
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>

var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```
3. 也可以使用 ```v-for``` 来遍历对象的属性
```
<ul id="v-for-object" class="demo">
  <li v-for="(value, key) in object">
    {{ key }}: {{ value }}
  </li>
</ul>

new Vue({
  el: '#v-for-object',
  data: {
    object: {
      firstName: 'John',
      lastName: 'Doe',
      age: 30
    }
  }
})
```
4. 在使用 ```v-for``` 时，尽可能提供一个 ```key```, 需要使用 ```v-bind``` 将其与动态值绑定在一起(这里使用简写)
```
<div v-for="item in items" :key="item.id">
  <!-- content -->
</div>
```
5. 显示过滤 / 排序结果
```
<li v-for="n in even(numbers)">{{ n }}</li>

data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
methods: {
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```
6. 在 ```<template>``` 上使用 ```v-for```，来渲染多个元素块
```
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

### 带有 v-if 的 v-for

1. ```v-for``` 的优先级高于 ```v-if```
2. ```v-if``` 将分别在循环中的每次迭代上运行
3. 当你只想将 某些 项渲染为节点时，这会非常有用
4. 以下只渲染 ```todos``` 中未完成的项
```
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```
5. 如果是根据条件跳过执行循环，可以将 ```v-if``` 放置于包裹元素上（或放置于 ```<template>``` 上）
```
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>No todos left!</p>
```

### 使用 v-for 遍历组件

1. 当对组件使用 ```v-for``` 时，必须设置 ```key``` 属性
```
<my-component v-for="item in items" :key="item.id"></my-component>
```
2. 将组件外部的迭代数据传入组件，需要额外使用 ```props```
```
<div id="todo-list-example">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Add a todo</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="E.g. Feed the cat"
    >
    <button>Add</button>
  </form>
  <ul>
    <li
      // 注意 is="todo-item" 属性。这在 DOM 模板中是必需的
      // 因为在 <ul> 中，只有 <li> 是有效元素
      // 这与调用 <todo-item> 的实际结果相同，但是却可以解决浏览器潜在的解析错误
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo.id"
      v-bind:title="todo.title"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>

Vue.component('todo-item', {
  template: '\
    <li>\
      {{ title }}\
      <button v-on:click="$emit(\'remove\')">Remove</button>\
    </li>\
  ',
  props: ['title']
})

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      {
        id: 1,
        title: 'Do the dishes',
      },
      {
        id: 2,
        title: 'Take out the trash',
      },
      {
        id: 3,
        title: 'Mow the lawn'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
```

### v-on 指令

```
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">翻转 message</button>
</div>

var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
```
1. 使用 ```v-on``` 指令 来增加事件监听器，触发事件后会调用 Vue 实例中 methods 下定义的方法
2. v-on 简写
```
<!-- 完整语法 -->
<a v-on:click="doSomething"> ... </a>

<!-- 简写 -->
<a @click="doSomething"> ... </a>
```
3. 需要访问原始 ```DOM``` 事件对象时，可以使用特殊的 ```$event``` 变量将它传递给一个方法
```
<button v-on:click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>

methods: {
  warn: function (message, event) {
    if (event) event.preventDefault()
    alert(message)
  }
}
```

### 修饰符 (Modifiers)

1. 修饰符 (modifier) 是以 ```.``` 表示的特殊后缀，表明应当以某种特殊方式绑定指令
2. 事件修饰符 (Event Modifiers)
```
<!-- 停止点击事件冒泡 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重新载入页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以链式调用 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时，使用事件捕获模式 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只有在 event.target 是元素自身时，才触发处理函数。 -->
<!-- 也就是说，event.target 是子元素时，不触发处理函数 -->
<div v-on:click.self="doThat">...</div>

<!-- 点击事件将只会触发一次 -->
<!-- 其他只能处理 DOM 事件的修饰符不同，.once 修饰符还可以用于处理组件事件 -->
<a v-on:click.once="doThis"></a>

<!-- 滚动事件的默认行为（滚动）将立即发生， -->
<!-- 而不是等待 `onScroll` 完成后才发生， -->
<!-- 以防在滚动事件的处理程序中含有 `event.preventDefault()` 调用 -->
<!-- 不要将 .passive 和 .prevent 放在一起使用，因为 .prevent 将被忽略 -->
<div v-on:scroll.passive="onScroll">...</div>
```
3. 按键修饰符 (Key Modifiers)
```
<!-- 只在 `keyCode` 是 13 时，调用 `vm.submit()` -->
<input v-on:keyup.13="submit">

<!-- 和上面的示例相同 -->
<input v-on:keyup.enter="submit">

<!-- 也可用于简写语法 -->
<input @keyup.enter="submit">

// 按键修饰符别名
.enter
.tab
.delete (捕获 “删除” 和 “退格” 按键)
.esc
.space
.up
.down
.left
.right

```
4. 系统辅助按键 (System Modifier Keys)
```
// 系统辅助按键修饰符别名
.ctrl
.alt
.shift
.meta
// 在 Macintosh 键盘中，meta 对应是 command 键 (⌘)
// 在 Windows 键盘中，meta 对应是 windows 键 (⊞)

<!-- Ctrl + C -->
<input @keyup.ctrl.67="clear">
// 在使用 keyup 事件时，在事件触发时，辅助按键必须是按下状态
// 也就是说，只有在按下 ctrl 键，同时释放一个按键时，才会触发 keyup.ctrl 事件
// 如果你单独释放 ctrl 键，则无法触发事件
// 如果你确实需要这样的行为，请使用 keyCode 替代 ctrl：keyup.17

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">做一些操作</div>
```
5. ```.exact``` 修饰符 (.exact Modifier)
```
<!-- 如果 Alt 键或 Shift 键与  Ctrl 键同时按下，也会触发事件 -->
<button @click.ctrl="onClick">A</button>

<!-- 只在 Ctrl 按键按下，其他按键未按下时，触发事件 -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- 只在没有系统辅助按键按下时，触发事件 -->
<button @click.exact="onClick">A</button>
```
6. 鼠标按键修饰符 (Mouse Button Modifiers)
```
// 些修饰符会限制处理函数，仅响应特定的鼠标按键触发的事件
.left
.right
.middle
```

### 使用 JavaScript 表达式

```
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

<div v-bind:id="'list-' + id"></div>

{{ message.split('').reverse().join('') }}
```

### computed 属性

```
<div id="example">
  <p>初始 message 是："{{ message }}"</p>
  <p>计算后的翻转 message 是："{{ reversedMessage }}"</p>
</div>

var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 一个 computed 属性的 getter 函数
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})
```

### computed 缓存 vs method 方法

```
// 在表达式中通过调用 method 方法的方式
// 也能够实现与 computed 属性相同的结果

<p>翻转 message 是："{{ reverseMessage() }}"</p>

// 在组件中
methods: {
  reverseMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```
1. 每个 computed 属性，只有在它所依赖的数据发生变化时，才会重新取值 (re-evaluate)
2. 只要 message 没有发生变化，多次访问 ```computed``` 属性 reversedMessage，将会立刻返回之前计算过的结果，而不必每次都重新执行函数
3. 如下的 ```computed``` 属性永远不会更新，因为 ```Date.now()``` 不是一个响应式的依赖数据
```
computed: {
  now: function () {
    return Date.now()
  }
}
```
4. 每当触发重新渲染 (re-render) 时，```method``` 调用方式将总是再次执行函数

### computed 属性中设置 setter

```
computed: {
  fullName: {
    // getter 函数
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter 函数
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

### computed 属性和 watch 属性

```
<div id="watch-example">
  <p>
    问一个答案是 yes/no 的问题：
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>

<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: '你要先提出问题，我才能给你答案！'
  },
  watch: {
    // 只要 question 发生改变，此函数就会执行
    question: function (newQuestion, oldQuestion) {
      this.answer = '等待输入停止……'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    // _.debounce 是由 lodash 提供的函数，
    // 在运行特别消耗性能的操作时，
    // 可以使用 _.debounce 来限制频率。
    // 在下面这种场景中，我们需要限制访问 yesno.wtf/api 的频率，
    // 等到用户输入完毕之后，ajax 请求才会发出。
    // 想要了解更多关于 _.debounce 函数（以及与它类似的 _.throttle）的详细信息，
    // 请访问：https://lodash.com/docs#debounce
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer: function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = '问题通常需要包含一个中文问号。;-)'
        return
      }
      this.answer = '思考中……'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = '错误！API 无法处理。' + error
        })
    }
  }
})
</script>
```

### v-model 指令

```
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>

var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```
1. ```v-model``` 指令使表单输入和应用程序状态之间双向绑定
2. ```v-model``` 会忽略所有表单元素中 ```value```, ```checked``` 或 ```selected``` 属性上初始设置的值，而总是将 Vue 实例中的 ```data``` 作为真实数据来源
3. 因此你应该在 JavaScript 端的组件 ```data``` 选项中声明这些初始值
4. 修饰符 (modifiers)
```
<!-- 在触发 "change" 事件后同步，而不是在触发 "input" 事件后更新 -->
<input v-model.lazy="msg" >

<!-- 将用户的输入，自动转换为 Number 类型 -->
<input v-model.number="age" type="number">

<!-- 将用户的输入，自动过滤掉首尾空格 -->
<input v-model.trim="msg">
```

### 组件 (component)

```
// 定义一个被命名为 todo-item 的新组件
Vue.component('todo-item', {
  template: '<li>这是一个 todo 项</li>'
})

// 现在你可以在另一个组件模板中组合使用它
<ol>
  <!-- 创建一个 todo-item 组件的实例 -->
  <todo-item></todo-item>
</ol>

// 但是这样会将每个 todo 项都为渲染相同的文本
// 我们应该要将数据从父作用域传到子组件，使它可以接受一个 prop
Vue.component('todo-item', {
  // todo-item 组件现在接受一个 "prop"，
  // 类似于一个自定义属性。
  // 此 prop 名为 todo。
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})

// 使用 v-bind 指令将 todo 传入每个重复的组件中
<div id="app-7">
  <ol>
    <!--
      现在我们为每个 todo-item 提供了 todo 对象，
      其中它的内容是动态的。
      我们还需要为每个组件提供一个 "key"
    -->
    <todo-item
      v-for="item in groceryList"
      v-bind:todo="item"
      v-bind:key="item.id">
    </todo-item>
  </ol>
</div>

Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})

var app7 = new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { id: 0, text: '蔬菜' },
      { id: 1, text: '奶酪' },
      { id: 2, text: '其他人类食物' }
    ]
  }
})
```
1. 组件 (component)，是具有 name 名称的可复用 Vue 实例，每次使用组件时，都会创建出一个新的组件实例
2. 它们接收的选项，和在 new Vue 时候的选项相同，例如 ```data```, ```computed```, ```watch```, ```methods``` 和生命周期钩子
3. 唯一的例外是，类似 ```el``` 这样，根实例上特有 (root-specific) 的选项
4. 组件的 ```data``` 选项必须是一个函数，以便每个实例都可以维护「函数返回的数据对象」的彼此独立的数据副本：
```
data: function () {
  return {
    count: 0
  }
}
```
5.1 全局注册方式 (global registration)
```
Vue.component('my-component-name', { /* ... */ })
// 全局注册的组件，可以在之后（通过 new Vue）创建的 Vue 根实例的模板中引用

// 如果你使用一个类似 webpack 的模块构建系统，全局注册所有组件
// 意味着就算你不引用某个组件，它仍然会打包到最终的构建 bundle 中
// 这会增加 JavaScript 的体积，让用户下载多余的代码
```
5.2 局部注册方式 (local registration)
```
// 将组件定义为纯 JavaScript 对象
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }
var ComponentC = { /* ... */ }

// 然后，在 components 选项中，定义你需要用到的组件
new Vue({
  el: '#app'
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})

// 局部注册的组件在子组件中无法访问
// 如果想要在 ComponentB 中访问 ComponentA，你必须使用
var ComponentA = { /* ... */ }

var ComponentB = {
  components: {
    'component-a': ComponentA
  },
  // ...
}

// 或者，使用 ES2015 模块，在 ComponentB.vue 中
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  },
  // ...
}
```
6. 串联式命名 (kebab-case)
```
Vue.component('my-component-name', { /* ... */ })
// 使用串联式命名 (kebab-case) 定义一个组件，在引用其自定义元素时
// 你必须也使用串联式命名(kebab-case)，例如 <my-component-name>
```
7. Pascal 式命名 (PascalCase)
```
Vue.component('MyComponentName', { /* ... */ })
// 使用 Pascal 式命名 (PascalCase) 定义一个组件，在引用其自定义元素时
// 两种方式都可以使用。也就是说 <my-component-name> 和 <MyComponentName> 都是可以接受的
// 然而要注意，直接在 DOM 中（即，非字符串模板）引用自定义元素
// 串联式命名 (kebab-case) 是唯一有效的命名方式
```

### 创建一个 Vue 实例

```
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $watch 是一个实例方法
vm.$watch('a', function (newValue, oldValue) {
  // 此回调函数将在 `vm.a` 改变后调用
})
```

### 响应式系统

```
1. 每当 data 对象发生变化，都会触发视图重新渲染
2. 如果实例已经创建，那么只有那些 data 中的原本就已经存在的属性，才是响应式的
3. 如果在实例创建之后，新添加的属性更新后，不会触发任何视图更新

var data = { a: 1 }
var vm = new Vue({
  data: data
})

vm.a == data.a // => true

// 设置实例上的属性，
// 也会影响原始数据
vm.a = 2
data.a // => 2

// 反之亦然
data.a = 3
vm.a // => 3

// 使用 Object.freeze() 来防止已有属性被修改
var obj = {
  foo: 'bar'
}
Object.freeze(obj)
new Vue({
  el: '#app',
  data: obj
})

<div id="app">
  <p>{{ foo }}</p>
  <!-- 这将不再更新 `foo`! -->
  <button v-on:click="foo = 'baz'">点我修改</button>
</div>
```

### 数组变化检测

Vue 无法检测到以下数组变动：
1. 当你使用索引直接设置一项时，例如 ```vm.items[indexOfItem] = newValue```
2. 当你修改数组长度时，例如 ```vm.items.length = newLength```
```
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x' // 不是响应的
vm.items.length = 2 // 不是响应的
```
解决方法：
```
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)

// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)

// vm.$set
vm.$set(vm.items, indexOfItem, newValue)

// 解决第 2 个问题
vm.items.splice(newLength)
```

### 对象变化检测

1. Vue 不允许在已经创建的实例上，动态地添加新的根级响应式属性 (root-level reactive property)
2. 然而，可以使用 ```Vue.set(object, key, value)``` 方法，将响应式属性添加到嵌套的对象上
```
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})
// 可以向嵌套的 userProfile 对象，添加一个新的 age 属性：
Vue.set(vm.userProfile, 'age', 27)
```
3. 也可以通过如下方式，添加新的响应式属性
```
vm.userProfile = Object.assign({}, vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

### 生命周期钩子函数

1. 所有的钩子函数在调用时，其 ```this``` 上下文都会指向调用它的 Vue 实例
2. 不要在选项属性或者回调函数中使用箭头函数（例如，```created: () => console.log(this.a)``` 或 ```vm.$watch('a', newValue => this.myMethod())```）
3. 因为箭头函数会绑定父级上下文，所以 this 不会按照预期指向 Vue 实例，经常会产生一些错误，例如 ```Uncaught TypeError: Cannot read property of undefined``` 或者 ```Uncaught TypeError: this.myMethod is not a function```

![js_advanced_vue_lifecycle](../../images/js_advanced_vue_lifecycle.png)

### class 和 style 绑定

1. ```v-bind:class``` 指令也可以和普通 class 属性共存
```
<div class="static"
     v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>

data: {
  isActive: true,
  hasError: false
}
```
2. 外部引用 data
```
<div v-bind:class="classObject"></div>

data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```
3. 将 class 和 style 与某个 ```computed``` 属性绑定在一起
```
<div v-bind:class="classObject"></div>

data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```
4. 向 ```v-bind:class``` 传入一个数组，来与 class 列表对应
```
<div v-bind:class="[activeClass, errorClass]"></div>

data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```
```
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```
```
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```
5. 在组件中使用
```
// 当你在 自定义组件 中使用 class 属性，这些 class 会被添加到组件的根元素上
// 根元素上已经存在的 class 不会被覆盖

Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})

<my-component class="baz boo"></my-component>

最终渲染的 HTML 就是：
<p class="foo bar baz boo">Hi</p>
```
```
<my-component v-bind:class="{ active: isActive }"></my-component>

当 isActive 值是 truthy，最终渲染的 HTML 就是：
<p class="foo bar active">Hi</p>
```
6. ```v-bind:style``` 的对象语法是一个 JavaScript 对象
```
<div v-bind:style="styleObject"></div>

data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

### props

```
Vue.component('blog-post', {
  // 在 JavaScript 中使用驼峰式(camelCase)
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})

<!-- 在 HTML 中使用串联式(kebab-case) -->
<blog-post post-title="hello!"></blog-post>
```
```
// 传递一个 Number 类型值
<blog-post v-bind:likes="post.likes"></blog-post>
// 传递一个 Boolean 类型值
<blog-post v-bind:is-published="post.isPublished"></blog-post>
// 传递一个 Array 类型值
<blog-post v-bind:comment-ids="post.commentIds"></blog-post>
// 传递一个 Object 类型值
<blog-post v-bind:author="post.author"></blog-post>

// 注意，JavaScript 中的对象和数组都是通过引用 (reference) 传递的
// 因此，如果 prop 是一个数组或对象，则在子组件内部改变对象或数组本身，仍然会影响到父组件状态
```
```
// 传递一个对象的所有属性
post: {
  id: 1,
  title: '我的 Vue 旅程'
}
<blog-post v-bind="post"></blog-post>
等价于：
<blog-post
  v-bind:id="post.id"
  v-bind:title="post.title"
></blog-post>
```
```
// prop 验证
Vue.component('my-component', {
  props: {
    // 基本类型(base type)的检查（`null` 表示接受所有类型）
    propA: Number,
    // 多种可能的类型
    propB: [String, Number],
    // 必须传递，且 String 类型
    propC: {
      type: String,
      required: true
    },
    // Number 类型，有一个默认值
    propD: {
      type: Number,
      default: 100
    },
    // Object 类型，有一个默认值
    propE: {
      type: Object,
      // Object/Array 类型，
      // 默认必须返回一个工厂函数
      default: function () {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator: function (value) {
        // 值必须是这些字符串中的一个
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    }
  }
})
// 注意，props 会在组件实例创建之前进行验证
// 因此在 default 或 validator 这些验证函数中
// 还无法访问到实例上的属性（像 data, computed 这些）
```
```
// 类型检查
type 可以是以下原生构造函数之一：

String
Number
Boolean
Array
Object
Date
Function
Symbol

// 除了以上这些，type 还可以是一个自定义构造函数
// 通过 instanceof 对 props 值进行类型推断

function Person (firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}

Vue.component('blog-post', {
  props: {
    author: Person
  }
})
// 以验证 author prop 的值，是由 new Person 创建出来的
```
```
// 替换 / 合并现有的特性 (replacing/merging with existing attributes)

// 假设这是 bootstrap-date-input 组件的模板：
<input type="date" class="form-control">

// 我们可能需要向组件添加一个特定的 class 类名：
<bootstrap-date-input
  data-date-picker="activated"
  class="date-picker-theme-dark"
></bootstrap-date-input>

// 对于大多数特性，传给组件的值将会替换掉组件自身设置的值
// 例如，向组件传入 type="text"，将会替换掉组件自身设置的 type=”date”
// 但是，class 和 style 特性会略微智能，这两个值会被 合并 而非替换
// 而 class 最终的值是：form-control date-picker-theme-dark
```

### 使用 events 向父组件发送消息

```
Vue.component('blog-post', {
  props: ['post'],
  template: `
    <div class="blog-post">
      <h3>{{ post.title }}</h3>
      <button v-on:click="$emit('enlarge-text', 0.1)">
        放大文本
      </button>
      <div v-html="post.content"></div>
    </div>
  `
})
// 在父实例中监听这个事件时，可以通过 $event 来访问这次发送事件的值
<blog-post
  ...
  v-on:enlarge-text="postFontSize += $event"
></blog-post>

// 或者，如果事件处理函数是一个方法
<blog-post
  ...
  v-on:enlarge-text="onEnlargeText"
></blog-post>

// 然后，这个值会被传入到方法中，作为第一个参数：
methods: {
  onEnlargeText: function (enlargeAmount) {
    this.postFontSize += enlargeAmount
  }
}
```

### 使用 slots 进行内容分发

```
Vue.component('alert-box', {
  template: `
    <div class="demo-alert-box">
      <strong>Error!</strong>
      <slot></slot>
    </div>
  `
})

<alert-box>
  发生一些错误。
</alert-box>
// 会被渲染为: Error!发生一些错误。

```

### 动态组件

```
// 有时，在组件之间进行动态切换非常有用，例如在标签式界面中
// 通过向 Vue 的 <component> 元素传入 is 特性，可以实现以上效果

<!-- Component changes when currentTabComponent changes -->
<component v-bind:is="currentTabComponent"></component>
```

### DOM 模板解析注意事项

1. ```<ul>```, ```<ol>```, ```<table>``` 和 ```<select>``` 这些元素，会对于出现在其内部的元素有所限制
2. 其内部只可以出现 ```<li>```, ```<tr>``` 和 ```<option>``` 这些元素
3. 由于这种 HTML 运行机制，在这些受限制的元素内部使用组件，会导致一些问题
4. 以下例子中自定义组件 ```<blog-post-row>``` 会被当作无效内容，提升到 table 元素之外，从而导致最终渲染输出后的错误
```
<table>
  <blog-post-row></blog-post-row>
</table>
```
5. ```is``` 特性提供了一种解决方案
```
<table>
  <tr is="blog-post-row"></tr>
</table>
```
