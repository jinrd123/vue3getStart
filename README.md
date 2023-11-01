直接看`src/components`里的`HellowWorld.vue`组件即可，直接看里面的`<script>`部分，跟着注释过一遍就好了hh



关于ts + vue3中provide / inject的使用：

# provide / inject（跨组件传值）

## 1. key是Symbol

新建src/constant/index.ts

```js
import type { InjectionKey } from 'vue';

export const key = Symbol() as InjectionKey<string>;
<!-- 父组件使用provide提供值 -->
<script setup lang="ts">
import { provide } from 'vue';
import { key } from '@/constant/index';
provide(key, '123'); //提供改变响应式对象的方法
</script>

<!-- 子组件使用inject取值 -->
<script setup lang="ts">
import { inject } from 'vue';
import { key } from '@/constant/index';

const string = inject(key);
</script>
```

## 2. key是字符串

inject返回的类型是 unknown，需要通过泛型参数显式声明

```js
<!-- 父组件提供provide -->
<script setup lang="ts">
import { ref, provide } from 'vue';
const state = ref(0);
const handlerState = () => {
  state.value = 1;
};
provide('info', state); //提供响应式对象
provide('func', handlerState); //提供改变响应式对象的方法
</script>

<!-- 子组件使用inject取值 -->
<script setup lang="ts">
import { inject } from 'vue';

//通过泛型参数显式声明
const state = inject<number>('info');
const func = inject<() => void>('func');
</script>
```

## 3. undefined问题

由于无法保证provide会提供这个值，因此inject通过泛型参数显示声明了类型，还会多个undefined类型

1. 提供默认值，可消除undefined

```js
const state = inject<number>('info', 20);
```

1. 使用类型断言，告诉编辑器这个值一定会提供

```js
const state = inject('info') as number;
```



# HTML标签对应的内置类型

详见`@/types/HTMLElementTagNameMapType.ts`：

如果要使用`a`标签的类型：

~~~typescript
// 映射集合接口
interface HTMLElementTagNameMap {
    "a": HTMLAnchorElement;
	// ...
}

// 类似对象取值，从映射集合接口中获取a标签类型
type TagAType = HTMLElementTagNameMap['a']; // HTMLAnchorElement
~~~

