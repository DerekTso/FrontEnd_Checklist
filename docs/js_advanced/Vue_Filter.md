# Javascript高阶 - Vue_Filter

## 目录
- [过滤器](#过滤器)
- [在组件的选项中定义局部过滤器](#在组件的选项中定义局部过滤器)
- [在创建 Vue 示例之前，定义一个全局的过滤器](#在创建-vue-示例之前定义一个全局的过滤器)
- [链式调用过滤器](#链式调用过滤器)
- [接收多个参数的过滤器函数](#接收多个参数的过滤器函数)

### 过滤器

1. 在 Vue.js 中，可以定义过滤器(filter)，常用于格式化文本
2. 过滤器可以在两种场景中使用：双花括号插值(mustache interpolation)和 ```v-bind``` 表达式
```
<!-- in mustaches -->
{{ message | capitalize }}

<!-- in v-bind -->
<div v-bind:id="rawId | formatId"></div>
```

### 在组件的选项中定义局部过滤器

```
filters: {
  capitalize: function (value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
}
```

### 在创建 Vue 示例之前，定义一个全局的过滤器

```
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})

new Vue({
  // ...
})
```

### 链式调用过滤器

```
// 表达式 message 的值将作为参数传入到 filterA 中
// 然后将 filterA 的结果传递到 filterB 中
{{ message | filterA | filterB }}
```

### 接收多个参数的过滤器函数

```
// 其中 message 的值作为第一个参数
// 普通字符串 'arg1' 作为第二个参数
// 表达式 arg2 取值后的值作为第三个参数
{{ message | filterA('arg1', arg2) }}
```
