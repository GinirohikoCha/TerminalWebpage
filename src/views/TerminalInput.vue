<template>
  <div class="terminal-input-container">
    <div class="terminal-input-prefix">{{ inputPrefix }}</div>

    <span class="terminal-input-text">{{ inputData.substring(0, CaretIndex) }}</span>

    <div class="terminal-input-caret">|</div>

    <span class="terminal-input-text">{{ inputData.substring(CaretIndex) }}</span>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'
import { Emit } from 'vue-property-decorator'

@Options({
  name: 'TerminalInput'
})

export default class Terminal extends Vue {
  inputPrefix = '/>　'/* 全角空格 */
  inputData = ''
  inputCaret = 0
  inputStatus = {
    shift: false,
    ctrl: false
  }

  // Computed
  get CaretIndex () : number {
    return this.inputData.length + this.inputCaret
  }

  // Methods
  input (str : string) : void {
    if (this.inputCaret === 0) {
      this.inputData += str
    } else {
      this.inputData = this.inputData.substring(0, this.CaretIndex) + str + this.inputData.substring(this.CaretIndex)
    }
  }

  handleKeyDown (evt : KeyboardEvent) : void {
    switch (evt.code) {
      case 'Backspace':
        this.inputData = this.inputData.substring(0, this.CaretIndex - 1) + this.inputData.substring(this.CaretIndex)
        break
      case 'Delete':
        this.inputData = this.inputData.substring(0, this.CaretIndex) + this.inputData.substring(this.CaretIndex + 1)
        if (this.inputCaret < 0) { this.inputCaret += 1 }
        break
      case 'Enter':
        this.enter()
        break
      // Caret Control
      case 'ArrowLeft':
        if (this.CaretIndex > 0) { this.inputCaret -= 1 }
        break
      case 'ArrowRight':
        if (this.inputCaret < 0) { this.inputCaret += 1 }
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
    if (evt.code === 'Space') {
      this.input('　')/* 全角空格 */
      return
    }
    if (!this.inputStatus.ctrl) {
      this.input(evt.key)
    }
  }

  handleKeyUp (evt : KeyboardEvent) : void {
    switch (evt.code) {
      case 'ControlLeft':
        this.inputStatus.ctrl = false
        break
    }
  }

  handlePaste (evt : Event) : void {
    const clipboardData = (evt as ClipboardEvent).clipboardData || (window as any).clipboardData
    const text = clipboardData.getData('text/plain')
    this.input(text)
  }

  // Emits
  @Emit()
  enter () : Record<string, string> {
    const data = this.inputData
    this.inputData = ''
    this.inputCaret = 0
    return { prefix: this.inputPrefix, message: data }
  }

  // Hooks
  mounted () : void {
    window.addEventListener('keydown', this.handleKeyDown)
    window.addEventListener('keypress', this.handleKeyPress)
    window.addEventListener('keyup', this.handleKeyUp)
    window.addEventListener('paste', this.handlePaste)
  }

  unmounted () : void {
    window.removeEventListener('keydown', this.handleKeyDown)
    window.removeEventListener('keypress', this.handleKeyPress)
    window.removeEventListener('keyup', this.handleKeyUp)
    window.removeEventListener('paste', this.handlePaste)
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
