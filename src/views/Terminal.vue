<template>
  <div class="terminal-container">
    <div class="terminal-panel">
      <div
        style="position: relative; color: white; font-family: '微软雅黑',serif; font-size: 18px"
        v-for="log in logs"
        :key="log">
        {{ log.prefix + log.message }}
      </div>

      <terminal-input @enter="enter"></terminal-input>
    </div>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'
import TerminalInput from './TerminalInput.vue'

interface Log {
  prefix: string
  message : string
}

@Options({
  components: {
    TerminalInput
  }
})

export default class Terminal extends Vue {
  logs : Log[] = []

  enter (log : Log) : void {
    this.logs.push(log)
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
