<template>
  <div id="app">
    <div>
      <div class="watermark">
        <canvas ref="watermark"/>
      </div>
    </div>
  </div>
</template>
<script>
import test from './assets/test.jpeg'
export default {
  name: 'APP',
  data () {
    return {
      ctx: null,
      text: '仅供注册QQ账号实用',
      fontSize: 12,
      paddingTop: 20,
      coler: 'rgba(255, 0, 0, 0.5)'
    }
  },
  computed: {
    textWidth () {
      return this.text.length * (this.fontSize + 6)
    }
  },
  mounted () {
    this.drawImage()
  },
  methods: {
    drawImage () {
      const img = new Image()
      img.src = test
      img.onload = () => {
        this.$refs.watermark.width = img.width
        this.$refs.watermark.height = img.height
        this.ctx = this.$refs.watermark.getContext('2d')
        this.ctx.drawImage(img, 0, 0)
        this.ctx.save()
        this.ctx.translate(0, 0)
        this.ctx.rotate(-45 * Math.PI / 180)
        this.fillText(img.width, img.height)
      }
    },
    fillText (width, height) {
      this.ctx.font = `${this.fontSize}px sans-serif`
      this.ctx.fillStyle = this.coler
      for (let rowIndex = 0; rowIndex <= Math.round(height * 2 / (this.fontSize + this.paddingTop)); rowIndex++) {
        for (let columnIndex = 0; columnIndex < Math.round(width * 2 / this.textWidth); columnIndex++) {
          const x = columnIndex * this.textWidth - width / 2 - (rowIndex % 2 + 3) * 200
          const y = rowIndex * (this.fontSize + this.paddingTop)
          this.ctx.fillText(this.text, x, y)
        }
      }
    }
  }
}
</script>
<style lang="postcss" scoped>
.home{
}
.watermark{
  margin: 0 auto;
  width: 100%;
  height: 400px;
  overflow: auto;
}
</style>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
