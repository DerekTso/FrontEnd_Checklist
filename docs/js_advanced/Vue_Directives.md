# Javascript高阶 - Vue_Directives

## 目录
- [自定义指令](#自定义指令)
- [指令定义对象的钩子函数(全部可选)](#指令定义对象的钩子函数全部可选)
- [指令钩子函数参数](#指令钩子函数参数)
- [对象字面量(Object Literals)](#对象字面量object-literals)

### 自定义指令

```
// 注册一个名为 `v-focus` 的全局自定义指令
Vue.directive('focus', {
  // 当绑定的元素插入到 DOM 时调用此函数……
  inserted: function (el) {
    // 元素调用 focus 获取焦点
    el.focus()
  }
})
```

```
// 注册一个局部指令
directives: {
  focus: {
    // 指令定义对象
    inserted: function (el) {
      el.focus()
    }
  }
}

// 在任何元素上使用新增的 v-focus 属性
<input v-focus>
```

### 指令定义对象的钩子函数(全部可选)

```
bind：
在指令第一次绑定到元素时调用，只会调用一次
可以在此钩子函数中，执行一次性的初始化设置

inserted：
在已绑定的元素插入到父节点时调用（只能保证父节点存在，不一定存在于 document 中）

update：
在包含指令的组件的 VNode 更新后调用，但可能之前其子组件已更新
指令的值可能更新或者还没更新，然而可以通过比较绑定的当前值和旧值，来跳过不必要的更新

componentUpdated：
在包含指令的组件的 VNode 更新后调用，并且其子组件的 VNode 已更新

unbind：
在指令从元素上解除绑定时调用，只会调用一次
```

### 指令钩子函数参数

```
// 指令钩子函数可传入以下参数：

el：指令绑定的元素，可以用于直接操作 DOM
除了 el 之外的其他参数，都应该是只读的，并且永远不要去修改它们

binding：一个对象，包含以下属性：

name：指令的名称，不包括 v- 前缀

value：向指令传入的值
例如，在 v-my-directive="1 + 1" 中，传入的值是 2

oldValue：之前的值，只在 update 和 componentUpdated 钩子函数中可用
无论值是否发生变化，都可以使用

expression：指令绑定的表达式(expression)，以字符串格式
例如，在 v-my-directive="1 + 1" 中，表达式是 "1 + 1"

arg：向指令传入的参数，如果有的话
例如，在 v-my-directive:foo 中，参数是 "foo"

modifiers：一个对象，包含修饰符，如果有的话
例如，在 v-my-directive.foo.bar 中，修饰符是 { foo: true, bar: true }

vnode：由 Vue 编译器(Vue’s compiler)生成的虚拟 Node 节点(virtual node)

oldVnode：之前的虚拟 Node 节点(virtual node)，只在 update 和 componentUpdated 钩子函数中可用
```

```
<div id="hook-arguments-example" v-demo:foo.a.b="message"></div>

Vue.directive('demo', {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify
    el.innerHTML =
      'name: '       + s(binding.name) + '<br>' +
      'value: '      + s(binding.value) + '<br>' +
      'expression: ' + s(binding.expression) + '<br>' +
      'argument: '   + s(binding.arg) + '<br>' +
      'modifiers: '  + s(binding.modifiers) + '<br>' +
      'vnode keys: ' + Object.keys(vnode).join(', ')
  }
})

new Vue({
  el: '#hook-arguments-example',
  data: {
    message: 'hello!'
  }
})
```

### 对象字面量(Object Literals)

```
<div v-demo="{ color: 'white', text: 'hello!' }"></div>

Vue.directive('demo', function (el, binding) {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text)  // => "hello!"
})
```
