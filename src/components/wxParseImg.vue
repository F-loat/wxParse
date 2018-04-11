<template>
  <image
    class="img"
    :mode="node.imageMode"
    :class="node.classStr"
    :style="fitStyleStr"
    :data-src="node.attr.src"
    :src="node.attr.src"
    @tap="wxParseImgTap"
    @load="wxParseImgLoad"
    />
</template>

<script>
export default {
  name: 'wxParseImg',
  data() {
    return {
      realWindowWidth: 0,
      realWindowHeight: 0,
      newStyleStr: ''
    }
  },
  props: {
    node: {}
  },
  mounted() {
    this.getSysWH()
  },
  computed: {
    fitStyleStr() {
      return this.newStyleStr || this.node.styleStr
    }
  },
  methods: {
    getSysWH() {
      //获取当前设备屏幕宽度和高度, 写在这里只是为了方便调试, 最好是写到 wxParse.vue 再传入
      wx.getSystemInfo({
        success: (res) => {
          this.realWindowWidth = res.windowWidth
          this.realWindowHeight = res.windowHeight
        }
      })
    },
    wxParseImgTap(e) {
      const { src } = e.target.dataset
      if (!src) return
      wx.previewImage({
        current: src, // 当前显示图片的http链接
        urls: this.node.imageUrls // 需要预览的图片http链接列表
      })
    },
    /**
     * 图片视觉宽高计算函数区
     **/
    wxParseImgLoad(e) {
      const { src } = e.target.dataset
      if (!src) return
      if (typeof src !== 'undefined' && src !== '' && src !== null) {
        this.calMoreImageInfo(e)
      }
    },
    // 假循环获取计算图片视觉最佳宽高
    calMoreImageInfo(e) {
      const { width, height } = e.mp.detail
      const recal = this.wxAutoImageCal(width, height)
      const { imageheight, imageWidth } = recal
      const { imagePadding } = this.node
      this.newStyleStr = `height: ${imageheight}px; width: ${imageWidth}px; padding: 0 ${imagePadding}px;`
    },
    // 计算视觉优先的图片宽高
    wxAutoImageCal(originalWidth, originalHeight) {
      //获取图片的原始长宽
      var windowWidth = 0, windowHeight = 0
      var autoWidth = 0, autoHeight = 0
      var results = {}
      var padding = this.node.imagePadding
      windowWidth = this.realWindowWidth - 2 * padding
      windowHeight = this.realWindowHeight
      //判断按照那种方式进行缩放
      // console.log('windowWidth' + windowWidth)
      if (originalWidth > windowWidth) {
        //在图片width大于手机屏幕width时候
        autoWidth = windowWidth
        // console.log('autoWidth' + autoWidth)
        autoHeight = autoWidth * originalHeight / originalWidth
        // console.log('autoHeight' + autoHeight)
        results.imageWidth = autoWidth
        results.imageheight = autoHeight
      } else {
        //否则展示原来的数据
        results.imageWidth = originalWidth
        results.imageheight = originalHeight
      }
      return results
    }
  }
}
</script>
