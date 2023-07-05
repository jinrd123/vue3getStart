<template>
    <div>
        <button @click="addChildCount">修改子组件里的数据</button>
        <Child ref="child"/>
    </div>
</template>

<script setup lang="ts">
import Child from "./Child.vue";
import {ref, onMounted} from "vue";
const child = ref();
onMounted(() => {
    console.log("child组件：", child.value.count);
})
// 也就是说父组件想控制子组件的状态（或者调用子组件的方法），直接通过 ref 获取子组件对象，然后其属性和方法都可以直接访问
// 但是如果使用的是setup语法糖的话需要子组件defineExpose暴露出来属性和方法（这个暴露相当于把属性和方法挂载到组件实例上？，因为count被defineExpose了，所以child.value.count可以直接访问？我猜的，但应该是这样了）
const addChildCount = () => {
    child.value.count ++;
}
</script>

<style lang="scss" scoped>

</style>