<template>
  <h1>使用props数据：{{ props.msg }}</h1>
  <!-- js中定义的函数也直接在模版中使用即可 -->
  <!-- 模版中用js里定义的变量就用插值语法{{ <变量名> }} -->
  <button @click="addCount">count is: {{ count }}</button>
  <div>age: {{ obj.name }}</div>
  <div 
    :style="{
      height: '100px',
      width: '100px',
      background: obj.age == 24 ? 'green' :'red',
    }"
    :class="{
      active: obj.age == 24 ? true : false,
    }"
  >动态添加属性</div>
</template>

<script setup lang="ts"> // 一般都给script标签加上这个setup，这是个方便开发的语法糖，在里面写逻辑等价于写在setup函数里面，并且里面声明的变量和函数都不用显示return（setup函数需要return之后模版中才可以使用）并且import语句也直接写在顶层即可
import {reactive, ref} from "vue";
// import childComponent from './childComponent';    使用组件直接import一下就可以在模版中使用了，也不用返回啥的
// ———————————  变量与函数定义 ———————————
// 用ref创建普通数据类型的响应式变量，模版中直接写变量名即可使用，如上模版中count（而不是count.value）；但是js中操作ref变量需要 <变量名.value> （触发ref对象的get行为）
const count = ref(0); 
// 这里定义了函数，上面模版中直接用即可（button的点击回调）
const addCount = () => { 
  count.value = count.value + 1;
};
// 复杂数据类型一般也用ref包裹即可，reactive用的比较少(也可能是个人习惯) plus: reactive返回的是一个proxy对象，所以vue可以监听这个proxy对象属性的变化，但监听不到这个对象本身的变化，用ref就能监听本身与属性
const obj = ref<{name: string; age: number}>({ 
  name: "jrd", // 模版中使用name就直接obj.name (相当于模版中使用ref变量都不用.value)
  age: 24,
})
// 与上面obj的区别就是js中操作reactive对象的属性也不用.value，而是直接objReactive.age即可
const objReactive = reactive<{name: string; age: number}>({
  name: "jrd",
  age: 24,
})
console.log(objReactive.age);
// ------------ props的使用 ------------
const props = defineProps<{msg: string}>(); // defineProps相当于返回一个proxy对象，里面的属性就是这个组件接收的所有props变量（App组件中传了一个msg：<HelloWorld :msg="msg" />），所以不管在模版中还是js中，都直接用props.<属性名>的形式使用props属性即可
console.log(props.msg);

// ————————-- 总结一下 ————————————
/**
 * 1. react中使用useState同时获得响应式变量以及修改这个变量的方法，但是vue中ref、reactive以及props都是单纯的定义的响应式变量，他们不跟任何set方法绑定，想修改他们在js中随意修改即可
 * 2. js中修改ref变量，需要先.value一下，普通数据类型只能用ref声明(复杂数据类型也用ref创建就行)
 * 3. 如果使用的是<script setup>这个setup语法糖，那么逻辑直接写在这里面即可，定义的变量和函数都可以在模版中使用；如果使用的是普通的<setup>那就参考App.vue组件里的写法，模版中想使用的变量和函数需要return
 * 4. 模版中给某个dom元素动态添加class或者style的话，直接:class/:style即可，也就是说这里的冒号类似于react里面的{}，表示后面的引号""里面的属性是一个变量，而非静态值
 * 5. 动态添加class时类名后面是布尔值，表示这个类有没有；动态添加样式时后面是具体的样式值（看上面最后一个div）
 * 6. plus：defineProps返回的props对象不能解构（解构一个proxy对象，丢失响应式），就只能props.xxx去使用
 */
</script>

<style>
.active {
  border: 4px solid red;
}
</style>