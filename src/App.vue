<template>
  <div id="app">
    <div>
      <div style="padding-top:20px">
        <el-form label-width="100px">
          <el-form-item label="图片">
            <el-upload :show-file-list="false" :auto-upload="false" :on-change="uploadImage">
              <el-button type="">添加图片</el-button>
            </el-upload>
          </el-form-item>
          <el-form-item label="水印内容">
            <el-input v-model="form.text" placeholder="请输入水印内容"></el-input>
          </el-form-item>
          <el-form-item label="水印颜色">
            <el-color-picker
              v-model="form.coler"
              show-alpha
              :predefine="predefineColors">
            </el-color-picker>
          </el-form-item>
          <el-form-item label="水印大小">
            <div>
              <el-slider v-model="form.fontSize" :min="10" :max="100"></el-slider>
            </div>
          </el-form-item>
          <el-form-item label="水印密度">
            <div>
              <el-slider v-model="form.paddingTop" :min="10" :max="500"></el-slider>
            </div>
          </el-form-item>
        </el-form>
      </div>
      <div class="watermark">
        <canvas ref="watermark"/>
      </div>
    </div>
  </div>
</template>
<script>
import { Form, FormItem, Slider, ColorPicker, Input, Button, Upload } from 'element-ui'
export default {
  name: 'APP',
  components: {
    'el-form': Form, 'el-form-item': FormItem, 'el-slider': Slider, 'el-color-picker': ColorPicker, 'el-input': Input, 'el-button': Button, 'el-upload': Upload
  },
  data () {
    return {
      ctx: null,
      form: {
        text: '仅供注册QQ账号实用',
        fontSize: 12,
        paddingTop: 100,
        coler: 'rgba(255, 0, 0, 0.5)'
      },
      width: 500,
      height: 400,
      img: '',
      predefineColors: [
        '#ff4500',
        '#ff8c00',
        '#ffd700',
        '#90ee90',
        '#00ced1',
        '#1e90ff',
        '#c71585',
        'rgba(255, 69, 0, 0.68)',
        'rgb(255, 120, 0)',
        'hsv(51, 100, 98)',
        'hsva(120, 40, 94, 0.5)',
        'hsl(181, 100%, 37%)',
        'hsla(209, 100%, 56%, 0.73)',
        '#c7158577'
      ]
    }
  },
  computed: {
    textWidth () {
      return this.form.text.length * (this.form.fontSize + 6)
    }
  },
  watch: {
    form: {
      handler () {
        console.log('watch')
        this.drawImage()
      },
      deep: true
    }
  },
  mounted () {
    this.ctx = this.$refs.watermark.getContext('2d')
  },
  methods: {
    uploadImage (file, fileList) {
      const render = new FileReader()
      render.onload = (e) => {
        const img = new Image()
        img.src = e.target.result
        img.onload = () => {
          this.width = img.width
          this.height = img.height
          this.img = img
          this.drawImage()
        }
      }
      render.readAsDataURL(file.raw)
    },
    drawImage () {
      this.ctx.clearRect(0, 0, this.width, this.height)
      this.$refs.watermark.width = this.width
      this.$refs.watermark.height = this.height
      this.ctx.drawImage(this.img, 0, 0)
      this.ctx.save()
      this.ctx.translate(0, 0)
      this.ctx.rotate(-45 * Math.PI / 180)
      this.fillText(this.width, this.height)
    },
    fillText (width, height) {
      this.ctx.font = `${this.form.fontSize}px sans-serif`
      this.ctx.fillStyle = this.form.coler
      for (let rowIndex = 0; rowIndex <= Math.round(height * 2 / (this.form.fontSize + this.form.paddingTop)); rowIndex++) {
        for (let columnIndex = 0; columnIndex < Math.round(width * 2 / this.textWidth); columnIndex++) {
          const x = columnIndex * this.textWidth - width / 2 - (rowIndex % 2) * this.form.paddingTop * 10
          const y = rowIndex * (this.form.fontSize + this.form.paddingTop)
          this.ctx.fillText(this.form.text, x, y)
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
  max-width: 100%;
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
