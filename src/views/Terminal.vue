<template>
  <div class="terminal-container">
    <div class="terminal-panel">
      <div style="position: relative; color: white; font-family: '微软雅黑',serif" v-for="log in logs" :key="log">{{ log }}</div>

      <terminal-input @enter="aaa"></terminal-input>
    </div>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'
import TerminalInput from './TerminalInput.vue'

@Options({
  components: {
    TerminalInput
  }
})

export default class Terminal extends Vue {
  logs : string[] = []

  aaa (a : string) : void {
    this.logs.push(a)
  }
}
</script>

<style lang="scss" scoped>
.terminal-container {
  position: relative;
  width: 100%;
  height: 100%;
  background-color: black;
  overflow: auto;

  .terminal-panel {
    padding: 20px;
  }
}
</style>
