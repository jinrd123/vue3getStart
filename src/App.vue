<script lang="ts">
import HelloWorld from './components/HelloWorld.vue'
export default {
  name: 'App',
  // 如果不用setup语法糖，引入的组件需要在这里“注册“，setup中定义的变量与函数需要显示return，麻烦一点
  components: {
    HelloWorld
  },
  setup() {
    const msg: string = "jrd";
    return {
      msg
    }
  }
}
</script>

<template>
  <HelloWorld :msg="msg" />
</template>

<style scoped>
</style>
