<template>
  <h1 ref="h1Element">使用props数据：{{ props.id }}</h1>
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
  <Child ref="childComponent"/>
</template>

<script setup lang="ts"> // 一般都给script标签加上这个setup，这是个方便开发的语法糖，在里面写逻辑等价于写在setup函数里面，并且里面声明的变量和函数都不用显示return（setup函数需要return之后模版中才可以使用）并且import语句也直接写在顶层即可
import {PropType, computed, nextTick, reactive, ref} from "vue";
import Child from './Child.vue';    // 使用组件直接import一下就可以在模版中使用了，也不用返回啥的
// import type { IProps } from '../types/HelloWorld';
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
// const props = defineProps<{msg: string}>(); // defineProps相当于返回一个proxy对象，里面的属性就是这个组件接收的所有props变量（App组件中传了一个msg：<HelloWorld :msg="msg" />），所以不管在模版中还是js中，都直接用props.<属性名>的形式使用props属性即可
// console.log('props:', props.msg);

// ————————-- 总结一下 ————————————
/**
 * 1. react中使用useState同时获得响应式变量以及修改这个变量的方法，但是vue中ref、reactive以及props都是单纯的定义的响应式变量，他们不跟任何set方法绑定，想修改他们在js中随意修改即可
 * 2. js中修改ref变量，需要先.value一下，普通数据类型只能用ref声明(复杂数据类型也用ref创建就行)
 * 3. 如果使用的是<script setup>这个setup语法糖，那么逻辑直接写在这里面即可，定义的变量和函数都可以在模版中使用；如果使用的是普通的<setup>那就参考App.vue组件里的写法，模版中想使用的变量和函数需要return
 * 4. 模版中给某个dom元素动态添加class或者style的话，直接:class/:style即可，也就是说这里的冒号类似于react里面的{}，表示后面的引号""里面的属性是一个变量，而非静态值
 * 5. 动态添加class时类名后面是布尔值，表示这个类有没有；动态添加样式时后面是具体的样式值（看上面最后一个div）
 * 6. plus：defineProps返回的props对象不能解构（解构一个proxy对象，丢失响应式），就只能props.xxx去使用
 */

// typescript实践
// —————— ref ———————
const number = ref(0); // 对于基础数据类型ref，ts默认推导即可，ref(0)等价于ref<number>，后续只可用number类型修改此变量；当然值类型的ref也可以手动定义复杂的泛型
console.log(number);
const h1Element = ref<HTMLHeadingElement | null>(null); // 对于dom元素类型的ref，泛型就填HTMLxxxxElement与null的联合类型并初始值给null即可
const childComponent = ref<InstanceType<typeof Child> | null>(null); // 对于组件类型的ref，泛型填InstanceType<typeof 组件> | null，并初始值给null（这样就可以提供组件属性或者方法的类型提示）
console.log('h1Element:', h1Element.value, childComponent.value?.count); // 输出null+报错(null.count不存在)，setup函数中使用dom元素或者组件类型的ref.value都无法访问到值，需要等到后续逻辑中才可正确访问（因为先执行setup后模板渲染）
nextTick(() => {
  console.log('childComponent count:', childComponent.value!.count); // 模板已挂载，可以确定组件ref已经能正确访问组件本身
})
// —————— reactive ———————
interface User {
  name?: string;
  age?: number;
}
const user = reactive<User>({}); // 如果一开始对象没有值初始化为{}，那就给泛型属性加上可选操作符?即可
console.log(user);
// ——————— computed ————————
// computed变量虽然用函数去定义，但我们只在乎其返回值的类型，一般来说默认推导即可
const doubleNumber = computed(() => {
  return number.value * 2;
})
console.log(doubleNumber.value);
// —————— defineProps ——————
// 首先defineProps为内置函数，无需import，调用即返回props对象（类似于一个reactive对象）
// defineProps有两种定义方式：一种与ts无关，为vue内置的校验对象方式，属于运行时校验；第二种就是利用defineProps的泛型与withDefaults函数提供类型校验，属于编译时校验
// 第一种：defineProps的参数——校验对象，它里面的类型校验以及属性可选性校验等都是vue本身提供的，而非ts等其它生态，所以我们写校验对象时要按照vue的既定语法
// 校验对象里的校验规则的力度仅限于开发环境下的警告，不会引起报错
interface IParents {
  motherName: string;
  fatherName: string;
}
const props = defineProps({
  id: {
    type: [Number, String], // 这里的Number与String都不是ts中的类型，而是defineProps的校验中类型限制就要求传递构造函数，如果是单个类型就直接写Number即可，如果是多个类型（类似于ts中的联合类型），就把多个构造函数写在数组里
    required: true
  },
  age: {
    type: Number,
    default() { // 默认值可为值或者函数类型
      return 18;
    },
  },
  sex: {
    type: String,
    validator(value) { // 自定义校验规则
      return ['man', 'women'].includes(value as string);
    }
  },
  parents: {
    type: Object as PropType<IParents>, // 如果props值是对象或者数组，可以用vue提供的内置类型工具PropType<xxx>来进一步缩小Object的类型
  }
})
// 第二种：编译时的ts类型校验
// 局限性：在vue@3.2.x版本中，类型接口IProps必须定义在组件内，不能由外部导入
// interface IProps {
//   id: number;
// }
// const props = withDefaults(defineProps<IProps>(), {id: 123}); // withDefaults第一个参数即为带有泛型的defineProps（入参为空即可），第二个参数即为默认值
console.log(props.id);


</script>

<style>
.active {
  border: 4px solid red;
}
</style>