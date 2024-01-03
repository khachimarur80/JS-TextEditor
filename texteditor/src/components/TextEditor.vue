<template>
  <div id="container">
    <div class="text" @keydown="textKeydown" @mousedown="textMouseDown" contenteditable>
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
        this.activeLine = parseInt(event.target.getAttribute('data-index'))
      }
    },

    mounted() {
      this.text = new File

      let line = new Line

      this.text.lines.push(line)
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
    background: #ddd;
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