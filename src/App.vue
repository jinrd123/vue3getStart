<script lang="ts">
import HelloWorld from './components/HelloWorld.vue'
import Parent from "./components/Parent.vue"
export default {
  name: 'App',
  // 如果不用setup语法糖，引入的组件需要在这里“注册“，setup中定义的变量与函数需要显示return，麻烦一点
  components: {
    HelloWorld,
    Parent
  },
  setup() {
    const msg: string = "jrd";
    const callBack = (data: any) => {
      console.log(data);
    }
    return {
      msg,
      callBack,
    }
  }
}
</script>

<template>
  <HelloWorld 
    :id="123" 
    :parents="{motherName: 'lijing', fatherName: 'jinenbin'}"
    @event1="callBack"
    @event2="callBack"
  />
  <Parent/>
</template>

<style scoped>
</style>
