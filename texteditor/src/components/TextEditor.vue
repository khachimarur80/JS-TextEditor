<template>
  <div id="container">
    <div style="user-select: none">{{ activeLine || "null" }}</div>
    <div class="text" @keydown="textKeydown" @mousedown="textMouseDown" @mouseup="textMouseUp" contenteditable>
      <div 
        :class="['line', activeLine==i ? 'active' : '']" 
        v-for="(line, i) in text.lines" 
        :key="i">
        <div class="line-count" contenteditable="false">
          {{ i }}
        </div>
        <div 
          class="line-contents" 
          :ref="'line_' + i"
          :data-index="i"
          >
          {{ line.text }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  class File {
    constructor() {
      this.lines = []
    }
  }
  
  class Line {
    constructor() {
      this.text = ""
    }
  }

  export default {
    name: 'TextEditor',

    data: () => ({
      text: [],
      activeLine: null,
    }),

    methods: {
      placeCaretAtEnd(lineIndex) {
        const lineContents = this.$refs['line_' + lineIndex][0];

        if (lineContents) {
          const range = document.createRange();
          const sel = window.getSelection();
          range.setStart(lineContents, lineContents.childNodes.length);
          range.collapse(true);
          sel.removeAllRanges();
          sel.addRange(range);
          lineContents.focus();
        }
      },
      textKeydown(event) {
        if (event.key == 'ArrowUp') {
          this.activeLine = Math.max(this.activeLine-1, 0)
        }
        else if (event.key == 'ArrowDown') {
          this.activeLine = Math.min(this.activeLine+1, this.text.lines.length-1)
        }
        else if (event.key == 'Enter') {
          event.preventDefault()
          let newLine = new Line
          this.text.lines.push(newLine)
          this.$nextTick(()=>{
            this.activeLine += 1
            this.placeCaretAtEnd(this.activeLine);
          })
        }
      },
      textMouseDown(event) {
        if (this.text.lines.length == 0) {
          let newLine = new Line
          this.text.lines.push(newLine)

          this.activeLine = 0
        }
        else if (event.target.classList.contains('line-contents')) {
          //Only focus when clicking on a line.
          console.log(event.target)
          let lineIndex = parseInt(event.target.getAttribute('data-index'))

          if (typeof lineIndex == "number") {
            this.activeLine = lineIndex
          }
          else {
            this.activeLine = null
          }
        }
        else {
          //Make the activeLine the last line, when the clicking outside of a line 
          // and nothing else is focused.
          if (typeof this.activeLine != "number") {
            this.activeLine = this.text.lines.length - 1
          }
          else {
          //If something is focused blur the activeLine
            event.preventDefault()
            this.activeLine = null
          }
        }
      },
      textMouseUp() {
        console.log(document.activeElement)
        if (typeof this.activeLine != "number") {
          document.querySelector('.text').blur()
          window.getSelection().removeAllRanges()
        }
      }
    },

    mounted() {
      this.text = new File
    },
  }
</script>

<style>
  #container {
    height: 100vh;
    width: 100h;
    padding: 50px;
  }
  .text {
    height: 100%;
    width: 100%;
    display: flex;
    flex-direction: column;
    border: 1px solid red;
  }
  .text:read-write:focus {
    outline: none;
  }
  .line {
    display: flex;
    justify-content: center;
    align-items: center;
    border-left: 1px solid white;
  }
  .line-contents {
    flex: 1;
    outline: none;
    background: #efefef;
    font-size: 14px;
    min-height: 22px;
  }
  .active {
    border-color: red;
  }
  .line-count {
    width: 20px;
    display: flex;
    justify-content: flex-end;
    user-select: none;
  }
</style>