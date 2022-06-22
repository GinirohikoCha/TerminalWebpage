<template>
  <div class="terminal-container">

  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'

@Options({
})

export default class Terminal extends Vue {
}
</script>

<style lang="scss" scoped>
.terminal-container {
  width: 100%;
  height: 100%;
  background-color: black;
}
</style>
