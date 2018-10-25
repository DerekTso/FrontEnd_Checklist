# Javascript高阶 - Vue_Component_Communication

## 目录
- [父组件向子组件传值：通过props数组](#父组件向子组件传值通过props数组)
- [子组件向父组件传值：自定义事件，this.$emit发送信息](#子组件向父组件传值自定义事件thisemit发送信息)
- [非父子关系的两个组件传值：eventbus/Vuex](#非父子关系的两个组件传值eventbus/Vuex)

### 父组件向子组件传值：通过props数组

```
//父组件 App.vue
<template>
  <div id="app">
    <!-- the router outlet, where all matched components would be viewed -->
    <router-link v-bind:to="'/'">Home</router-link>
    <router-link v-bind:to="'/about'">About</router-link>
    <router-view></router-view>
    <hello msg-father="xxx"></hello>
  </div>
</template>

<script>
  import hello from  './components/hello'
  export default {
    name: 'app',
    components:{
      hello
    }
  }
</script>

<style>
</style>


//子组件 ./components/Hello.vue
//通过props接收信息
<template>
  <div class="hello">
      <p>{{msgFather}}</p>
  </div>
</template>

<script>
export default {
  name: 'hello',
  data () {
    return {
    }
  },
  props:['msgFather']
}
</script>

<style scoped>

</style>
```

### 子组件向父组件传值：自定义事件，this.$emit发送信息

```
//子组件
<template>
  <div class="hello">
    <input type="text" v-model="inputValue" @keypress.enter="enter">
    <p>{{mes}}</p>
  </div>
</template>

<script>
export default {
  props:['mes'],
  data: function () {
    return {
      inputValue: ''  
    }
  },
  methods: {
    enter () {
      this.$emit("sendInputValue", this.inputValue);
    }
  }
}
</script>


//父组件
<template>
　　<div>
　　　　<p>{{message}}</p>
   　　 <accept-and-refuse :mes=loginJson.animal  @sendInputValue='showChildMsg'></accept-and-refuse>
　　</div>
</template>

<script>
import AcceptAndRefuse from '@/components/public/AcceptAndRefuse'
    
export default {
  data() {
    return {
      message:'hello message',
      loginJson:{
  "animal":"dog"
      }

  },
  mounted(){
  },
  methods: {
    showChildMsg(msg) {
message = msg;
    }
  },
  components:{
    AcceptAndRefuse
  }
}
</script>

<style>

</style>
```

### 非父子关系的两个组件传值：eventbus/Vuex

```
// 创建eventBus.js文件
import Vue from 'vue';  
export default new Vue();

// 在组件 A 中触发事件
bus.$emit('id-selected', 1);

// 在组件 B 中监听事件
bus.$on('id-selected', function (id) {
  // ...
}

// 官方推荐的 eventbus 解决方案的缺陷在于
// 在数据传递过程中，两个组件必须都已经被渲染过
// 如果组件 A 通过 vue-router 跳转到组件 B，在组件 A 销毁后才开始渲染组件 B，此时，组件 B 中的监听没有被触发
// 为了解决这个问题，可以使用 vuex (有 store 中的 state 来进行状态管理)
```
