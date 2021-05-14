<template>
  <div class="terminal" ref="draggableContainer">
    <div class="terminal__bar" @mousedown="dragMouseDown" >
      <span>TERMINAL</span>
    </div>
    <div class="terminal__output">
      <p v-for="(line, i) in output" :key="i">
        {{ line }}
      </p>
    </div>
    <input class="terminal__input" v-model="input" placeholder=">" @keyup.enter="submit">
  </div>
</template>

<script>
export default {
  name: "Terminal",
  data () {
    return {
      positions: {
        clientX: undefined,
        clientY: undefined,
        movementX: 0,
        movementY: 0
      },
      input: '',
      output: []
    }
  },
  methods: {
    dragMouseDown: function (event) {
      event.preventDefault()
      // get the mouse cursor position at startup:
      this.positions.clientX = event.clientX
      this.positions.clientY = event.clientY
      document.onmousemove = this.elementDrag
      document.onmouseup = this.closeDragElement
    },
    elementDrag: function (event) {
      event.preventDefault()
      this.positions.movementX = this.positions.clientX - event.clientX
      this.positions.movementY = this.positions.clientY - event.clientY
      this.positions.clientX = event.clientX
      this.positions.clientY = event.clientY
      // set the element's new position:
      this.$refs.draggableContainer.style.top = (this.$refs.draggableContainer.offsetTop - this.positions.movementY) + 'px'
      this.$refs.draggableContainer.style.left = (this.$refs.draggableContainer.offsetLeft - this.positions.movementX) + 'px'
    },
    closeDragElement () {
      document.onmouseup = null
      document.onmousemove = null
    },
    submit () {
      if (this.input.toLowerCase() === 'clear') {
        this.output = []
        return this.input = ''
      }
      if (
        this.input.toLowerCase() === 'ls' ||
        this.input.toLowerCase() === 'pwd' ||
        this.input.toLowerCase() === 'cd' ||
        this.input.toLowerCase() === 'cd ..' ||
        this.input.toLowerCase() === 'top' ||
        this.input.toLowerCase() === 'env' ||
        this.input.toLowerCase() === 'whoami'
      )
      {
        this.output.push(this.input)
        this.output.push("I know this really looks like a real terminal, but it's just a game.")
        return this.input = ''
      }
      this.output.push(this.input)
      this.output.push("> Unknown command")
      return this.input = ''
    }
  }
}
</script>

<style scoped lang="scss">
.terminal {
  z-index: 2;
  position: absolute;
  background: black;
  width: 500px;
  max-height: 300px;
  height: 300px;
  padding: 0 8px 40px;
  font-family: var(--font-primary);
  font-size: 22px;
  border: 3px solid white;
  border-radius: 2px;
  box-shadow: 4px 3px 3px 0px #222;
  top: 20px;
  left: 87px;
  &__bar {
    z-index: 3;
    cursor: move;
    background: rgb(92, 68, 68);
    color: white;
    width: 100%;
    height: 15px;
    padding: 0 8px;
    margin-left: -8px;
    font-size: 12px;
    display: flex;
    place-content: center;
    border-bottom: 2px solid white;
  }
  &__output {
    text-align: start;
    color: white;
    max-height: 200px;
    overflow-y: scroll;
    overscroll-behavior-y: contain;
    scroll-snap-type: y proximity;
    & p {
      margin: 0;
    }
    & p:last-child {
      scroll-snap-align: end;
    }
  }
  &__input {
    width: 100%;
    background: black;
    padding: 0;
    border: none;
    color: white;
    height: 22px;
    overflow-x: hidden;
    font-family: var(--font-primary);
    font-size: 22px;
    &:focus {
      outline: none;
    }
  }
}
</style>