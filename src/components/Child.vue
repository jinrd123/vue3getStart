<template>
    <div>
        child组件
        {{ count }}
    </div>
</template>

<script setup lang="ts">
import {ref} from "vue";
const count = ref(0);
defineExpose({
    count
})

</script>

<style lang="scss" scoped>

</style>