<template>
  <div class="terminal-input-container">
    <div class="terminal-input-prefix">/></div>

    <span class="terminal-input-text">{{ inputData.substring(0, CaretIndex) }}</span>

    <div class="terminal-input-caret">|</div>

    <span class="terminal-input-text">{{ inputData.substring(CaretIndex) }}</span>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'

@Options({
  name: 'TerminalInput'
})

export default class Terminal extends Vue {
  inputData = ''
  inputCaret = 0
  inputStatus = {
    shift: false,
    ctrl: false
  }

  get CaretIndex () : number {
    return this.inputData.length + this.inputCaret
  }

  handleKeyDown (evt : KeyboardEvent) : void {
    switch (evt.code) {
      case 'Backspace':
        this.inputData = this.inputData.substring(0, this.CaretIndex - 1) + this.inputData.substring(this.CaretIndex)
        break
      case 'Delete':
        this.inputData = this.inputData.substring(0, this.CaretIndex) + this.inputData.substring(this.CaretIndex + 1)
        if (this.inputCaret < 0) {
          this.inputCaret += 1
        }
        break
      // Caret Control
      case 'ArrowLeft':
        if (this.CaretIndex > 0) {
          this.inputCaret -= 1
        }
        break
      case 'ArrowRight':
        if (this.inputCaret < 0) {
          this.inputCaret += 1
        }
        break
      // Input Status
      case 'ControlLeft':
        this.inputStatus.ctrl = true
        break
      default:
        console.debug(evt.code)
    }
  }

  handleKeyPress (evt : KeyboardEvent) : void {
    if (evt.code === 'Enter') { return }
    if (!this.inputStatus.ctrl) {
      this.inputData += evt.key
    }
  }

  handleKeyUp (evt : KeyboardEvent) : void {
    switch (evt.code) {
      case 'ControlLeft':
        this.inputStatus.ctrl = false
        break
      default:
        console.debug(evt.code)
    }
  }

  handlePaste (evt : Event) : void {
    const clipboardData = (evt as ClipboardEvent).clipboardData || (window as any).clipboardData
    console.log(clipboardData) // 查看clipboardData
    const text = clipboardData.getData('text/plain')
    this.inputData += text
  }

  handleSelect (evt : Event) : void {
    console.log(evt)
  }

  mounted () : void {
    window.addEventListener('keydown', this.handleKeyDown)
    window.addEventListener('keypress', this.handleKeyPress)
    window.addEventListener('keyup', this.handleKeyUp)
    window.addEventListener('paste', this.handlePaste)
    window.addEventListener('select', this.handleSelect)
  }
}
</script>

<style lang="scss" scoped>
.terminal-input-container {
  line-height: 25px;
  color: white;
  font-size: 18px;

  .terminal-input-prefix {
    display: inline-block;
    margin-right: 10px;
    user-select: none;
  }

  .terminal-input-caret {
    display: inline-block;
    animation: twinkling 1s infinite ease-in-out;
    user-select: none;
  }

  .terminal-input-text {
    cursor: default;
    word-wrap: break-word;
    word-break: normal;
  }
}

@keyframes twinkling{
  0%{opacity: 0;}
  50%{opacity: 0;}
  60%{opacity: 1;}
  100%{opacity: 1;}
}

</style>
