<template>
  <div :style="style" :class="className">
    Test-component-1
  </div>
</template>

<script>
import { mixinWidget } from 'src/mixin/Mixins'

export default {
  name: 'Test-component-1widget',
  mixins: [mixinWidget]

}
</script>

<style scoped>
div {
  border: solid 1px red;
}
</style>
