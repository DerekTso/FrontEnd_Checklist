# Javascript高阶 - Vue_Mixin

## 目录
- [mixin概念](#mixin概念)
- [选项合并(option merging)](#选项合并option-merging)
    - [对 data 对象进行浅合并，优先使用组件的 data](#对-data-对象进行浅合并优先使用组件的-data)
    - [对具有相同名称的钩子函数的合并，它们会被依次调用](#对具有相同名称的钩子函数的合并它们会被依次调用)
    - [值为对象的选项（methods, components 和 directives）会被合并到相应的选项对象上，优先使用组件选项对象中键值对](#值为对象的选项methods-components-和-directives会被合并到相应的选项对象上优先使用组件选项对象中键值对)
- [全局 mixin(global mixin)](#全局-mixinglobal-mixin)
- [自定义选项的合并策略(custom option merge strategies)](#自定义选项的合并策略custom-option-merge-strategies)
### mixin概念

1. mixins是一种分发Vue组件中 可复用功能 的一种灵活方式
2. mixins是一个JavaScript对象，可以包含组件中的任意选项，比如Vue实例中生命周期的各个钩子函数，也可以是data、components、methods或directives等
```
// 定义一个 mixin 对象
var myMixin = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('来自 mixin 对象的 hello！')
    }
  }
}

// 定义一个使用以上 mixin 对象的组件
var Component = Vue.extend({
  mixins: [myMixin]
})

var component = new Component() // => "来自 mixin 对象的 hello！"
```

### 选项合并(option merging)

#### 对 data 对象进行浅合并，优先使用组件的 data

```
var mixin = {
  data: function () {
    return {
      message: 'hello',
      foo: 'abc'
    }
  }
}

new Vue({
  mixins: [mixin],
  data: function () {
    return {
      message: 'goodbye',
      bar: 'def'
    }
  },
  created: function () {
    console.log(this.$data)
    // => { message: "goodbye", foo: "abc", bar: "def" }
  }
})
```

#### 对具有相同名称的钩子函数的合并，它们会被依次调用
```
// mixin 对象中的同名钩子函数先调用，组件自身的钩子函数后调用
var mixin = {
  created: function () {
    console.log('mixin 对象的钩子函数被调用')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('组件的钩子函数被调用')
  }
})

// => "mixin 对象的钩子函数被调用"
// => "组件的钩子函数被调用"
```

#### 值为对象的选项（methods, components 和 directives）会被合并到相应的选项对象上，优先使用组件选项对象中键值对
```
// 注意，在 Vue.extend() 中，Vue 也使用了与此相同的合并策略
var mixin = {
  methods: {
    foo: function () {
      console.log('foo')
    },
    conflicting: function () {
      console.log('from mixin')
    }
  }
}

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () {
      console.log('bar')
    },
    conflicting: function () {
      console.log('from self')
    }
  }
})

vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "from self"
```

### 全局 mixin(global mixin)

```
// 请少量且小心谨慎地使用全局 mixin
// 应该只将全局 mixin，用于自定义选项的处理逻辑
// 也可以在插件中使用全局 mixin，然后通过调用插件来复用组件功能，以避免应用程序中的重复部分

// 为自定义选项 `myOption` 注入一个处理函数
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

new Vue({
  myOption: 'hello!'
})
// => "hello!"
```

### 自定义选项的合并策略(custom option merge strategies)

```
// 如果想要定制自定义选项的合并逻辑，则需要向 Vue.config.optionMergeStrategies 添加一个函数
Vue.config.optionMergeStrategies.myOption = function (toVal, fromVal) {
  // return mergedVal
}

// 对于大多数基于对象(object-based)的选项，可以使用 methods 的合并策略
var strategies = Vue.config.optionMergeStrategies
strategies.myOption = strategies.methods

// 高级用法示例
const merge = Vue.config.optionMergeStrategies.computed
Vue.config.optionMergeStrategies.vuex = function (toVal, fromVal) {
  if (!toVal) return fromVal
  if (!fromVal) return toVal
  return {
    getters: merge(toVal.getters, fromVal.getters),
    state: merge(toVal.state, fromVal.state),
    actions: merge(toVal.actions, fromVal.actions)
  }
}
```
