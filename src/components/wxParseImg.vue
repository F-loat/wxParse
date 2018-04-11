<template>
  <image
    class="img"
    :mode="node.imageMode"
    :class="node.classStr"
    :style="fitstyleStr"
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
        newstyleStr: ''
    };
  },
  props: {
      node: {},
      padding: {}
  },
  mounted() {
      this.getSysWH();
  },
    computed: {
      fitstyleStr: function(){
        if (this.$data.newstyleStr != ''){
          return this.$data.newstyleStr;
        }else{
          this.node.styleStr;
        }
      }
    },
  methods: {
    getSysWH: function() {
      var that = this;
      //获取当前设备屏幕宽度和高度, 写在这里只是为了方便调试, 最好是写到 wxParse.vue 再传入
      wx.getSystemInfo({
        success: function(res) {
          that.$data.realWindowWidth = res.windowWidth;
          that.$data.realWindowHeight = res.windowHeight;
        }
      });
    },
    wxParseImgTap(e) {
      const { src } = e.target.dataset;
      if (!src) return;
      wx.previewImage({
        current: src, // 当前显示图片的http链接
        urls: this.node.imageUrls // 需要预览的图片http链接列表
      });
    },
    /**
     * 图片视觉宽高计算函数区
     **/
    wxParseImgLoad(e) {
      const { src } = e.target.dataset;
      if (!src) return;
      var that = this;
      if (typeof src != "undefined" && src != "" && src != null) {
        this.calMoreImageInfo(e, that);
      }
    },
    // 假循环获取计算图片视觉最佳宽高
    calMoreImageInfo: function(e, that) {
      var temImages = that.node;
      var recal = this.wxAutoImageCal(e.mp.detail.width, e.mp.detail.height,that); 
      var index = temImages.index;
      this.$data.newstyleStr = "height:" + recal.imageheight + "px; width:" + recal.imageWidth + "px; padding: 0 " + this.padding.imagePadding + "px;";
    },

    // 计算视觉优先的图片宽高
    wxAutoImageCal: function(originalWidth, originalHeight, that) {
      //获取图片的原始长宽
      var windowWidth = 0, windowHeight = 0;
      var autoWidth = 0, autoHeight = 0;
      var results = {};
      var padding = this.padding.imagePadding;
      windowWidth = that.$data.realWindowWidth - 2 * padding;
      windowHeight = that.$data.realWindowHeight;
      //判断按照那种方式进行缩放
      // console.log("windowWidth" + windowWidth);
      if (originalWidth > windowWidth) {
        //在图片width大于手机屏幕width时候
        autoWidth = windowWidth;
        // console.log("autoWidth" + autoWidth);
        autoHeight = autoWidth * originalHeight / originalWidth;
        // console.log("autoHeight" + autoHeight);
        results.imageWidth = autoWidth;
        results.imageheight = autoHeight;
      } else {
        //否则展示原来的数据
        results.imageWidth = originalWidth;
        results.imageheight = originalHeight;
      }
      return results;
    }
  }
};
</script>
