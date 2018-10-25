# Javascript高阶 - Vue_Render

## 目录
- [virtual DOM](#virtual-dom)
- [createElement 参数](#createelement-参数)
- [在 render 函数中实现 v-if/v-for 功能](#在-render-函数中实现-v-ifv-for-功能)
- [在 render 函数中实现 v-model 功能](#在-render-函数中实现-v-model-功能)
- [slot](#slot)

### virtual DOM

在下面两种场景中，Vue 会自动保持页面更新，在 ```blogTitle``` 修改时，也同样能够及时更新

```
// 在 Vue 模板中，在页面中添加需要用到的 HTML
<h1>{{ blogTitle }}</h1>
```

```
// 或者通过一个 render 函数
render: function (createElement) {
  return createElement('h1', this.blogTitle)
}
```

1. Vue 通过实现一个 ```virtual DOM```，来跟踪那些「真正需要对 DOM 所做的修改」
2. ```createElement``` 实际返回的并非一个真正的 DOM 元素
3. 应该将其命名为 ```createNodeDescription```，包含「哪些节点应该由 Vue 渲染在页面上」的相关描述信息，也包括所有子节点的相关描述
4. 以上这个节点描述称为 “virtual node”，通常缩写为 VNode，组件树中的所有 VNode 必须是唯一的
5. ”virtual DOM” 就是由 Vue 组件树构建出来的，被称为整个 VNodes 树

### createElement 参数

```
// @returns {VNode}
createElement(
  // {String | Object | Function}
  // HTML 标签名称，或组件选项对象，
  // 或一个最终解析(resolve)为 HTML 标签名称或组件选项对象的异步函数，
  // 必选参数
  'div',

  // {Object}
  // 一个数据对象，
  // 和模板中用到的属性相对应，
  // 可选参数
  {
    // 和 `v-bind:class` 的 API 相同，
    // 也接收一个字符串、对象，或字符串对象构成的数组
    class: {
foo: true,
bar: false
    },
    // 和 `v-bind:style` 的 API 相同，
    // 也接收一个字符串、对象，或字符串对象构成的数组
    style: {
color: 'red',
fontSize: '14px'
    },
    // 普通的 HTML 属性
    attrs: {
id: 'foo'
    },
    // 组件 props
    props: {
myProp: 'bar'
    },
    // DOM 属性
    domProps: {
innerHTML: 'baz'
    },
    // 事件处理程序嵌套在 `on` 字段下，
    // 然而不支持在 `v-on:keyup.enter` 中的修饰符。
    // 因此，你必须手动检查
    // 处理函数中的 keyCode 值是否为 enter 键值。
    on: {
click: this.clickHandler
    },
    // 仅对于组件，
    // 用于监听原生事件，而不是组件内部
    // 使用 `vm.$emit` 触发的事件。
    nativeOn: {
click: this.nativeClickHandler
    },
    // 自定义指令。
    // 注意，由于 Vue 会追踪旧值，
    // 所以不能对`绑定`的`旧值`设值
    directives: [
{
name: 'my-custom-directive',
value: '2',
expression: '1 + 1',
arg: 'foo',
modifiers: {
    bar: true
}
}
    ],
    // 作用域插槽(scoped slot)的格式如下
    // { name: props => VNode | Array<VNode> }
    scopedSlots: {
default: props => createElement('span', props.text)
    },
    // 如果此组件是另一个组件的子组件，
    // 需要为插槽(slot)指定名称
    slot: 'name-of-slot',
    // 其他特殊顶层(top-level)属性
    key: 'myKey',
    ref: 'myRef'
  },

  // {String | Array}
  // children VNode组成，或使用 `createElement()` 生成，
  // 或使用字符串的text VNode，
  // 可选参数
  [
    'Some text comes first.',
    createElement('h1', 'A headline'),
    createElement(MyComponent, {
      props: {
someProp: 'foobar'
      }
    })
  ]
)
```

### 在 render 函数中实现 v-if/v-for 功能

```
<ul v-if="items.length">
  <li v-for="item in items">{{ item.name }}</li>
</ul>
<p v-else>No items found.</p>
```

```
props: ['items'],
render: function (createElement) {
  if (this.items.length) {
    return createElement('ul', this.items.map(function (item) {
      return createElement('li', item.name)
    }))
  } else {
    return createElement('p', 'No items found.')
  }
}
```

### 在 render 函数中实现 v-model 功能

```
props: ['value'],
render: function (createElement) {
  var self = this
  return createElement('input', {
    domProps: {
      value: self.value
    },
    on: {
      input: function (event) {
self.$emit('input', event.target.value)
      }
    }
  })
}
```

### slot

```
// 使用this.$slots访问静态插槽内容作为 VNode 数组：
render: function (createElement) {
  // <div><slot></slot></div>
  return createElement('div', this.$slots.default)
}

// 使用 this.$scopedSlots 访问作用域插槽作为返回 VNodes 的函数:
props: ['message'],
render: function (createElement) {
  // `<div><slot :text="message"></slot></div>`
  return createElement('div', [
    this.$scopedSlots.default({
      text: this.message
    })
  ])
}

// 使用 render 函数传递作用域插槽到子组件
// 使用 VNode 数据中的 scopedSlots 关键字：

render: function (createElement) {
  return createElement('div', [
    createElement('child', {
      // pass scopedSlots in the data object
      // in the form of { name: props => VNode | Array<VNode> }
      scopedSlots: {
default: function (props) {
  return createElement('span', props.text)
}
      }
    })
  ])
}
```
