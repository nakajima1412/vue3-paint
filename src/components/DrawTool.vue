<template>
  <div>
    <h1>DrawTool</h1>
    <div id="canvas-area">
      <canvas
        id="myCanvas"
        v-bind:class="{ eraser: canvasMode === 'eraser' }"
        :width="width"
        :height="height"
        @mousedown="dragStart"
        @mouseup="dragEnd"
        @mouseout="dragEnd"
        @mousemove="draw"
      ></canvas>
    </div>
    <div id="tool-area">
      <button id="pen-button" @click="pen">ペン</button>
      <button id="eraser-button" @click="eraser">消しゴム</button>
      <button id="clear-button" @click="clear">クリア</button>
      <button id="download-button" @click="download">ダウンロード</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DrawTool',
  data() {
    return {
      canvasMode: 'pen',
      canvas: null,
      context: null,
      isDrag: false,
      width: 640,
      height: 800
    }
  },
  mounted() {
    this.canvas = document.querySelector('#myCanvas')
    this.context = this.canvas.getContext('2d')
    this.context.lineCap = 'round'
    this.context.lineJoin = 'round'
    this.context.lineWidth = 5
    this.context.strokeStyle = '#000000'
  },
  methods: {
    draw(e) {
      // 描画
      let x = e.layerX
      let y = e.layerY

      if (!this.isDrag) {
        return
      }

      this.context.lineTo(x, y)
      this.context.stroke()
    },
    dragStart(e) {
      // 描画開始（mousedown）
      let x = e.layerX
      let y = e.layerY

      this.context.beginPath()
      this.context.lineTo(x, y)
      this.context.stroke()

      this.isDrag = true
    },
    dragEnd() {
      // 描画終了（mouseup, mouseout）
      this.context.closePath()
      this.isDrag = false
    },
    clear() {
      this.context.clearRect(0, 0, this.canvas.width, this.canvas.height)
    },
    pen() {
      // ペンモード
      // カーソル変更
      this.canvasMode = 'pen'

      // 描画設定
      this.context.lineCap = 'round'
      this.context.lineJoin = 'round'
      this.context.lineWidth = 5
      this.context.strokeStyle = '#000000'
    },
    eraser() {
      // 消しゴムモード
      // カーソル変更
      this.canvasMode = 'eraser'

      // 描画設定
      this.context.lineCap = 'square'
      this.context.lineJoin = 'square'
      this.context.lineWidth = 30
      this.context.strokeStyle = '#FFFFFF'
    },
    // 画像ダウンロード
    download() {
      let link = document.createElement('a')
      link.href = this.canvas.toDataURL('image/png')
      link.download = 'canvas-' + new Date().getTime() + '.png'
      link.click()
    }
  }
}
</script>

<style scoped>
#myCanvas {
  border: 1px solid #000000;
}
.eraser {
  cursor: url(../assets/images/eraser.png) 15 15, auto;
}
</style>
