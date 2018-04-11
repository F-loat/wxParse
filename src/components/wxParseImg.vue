<template>
  <image
    class="img"
    :mode="node.imageMode"
    :class="node.classStr"
    :style="node.styleStr"
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
      realWindowHeight: 0
    };
  },
  props: {
    node: {}
  },
  mounted() {
      this.getSysWH();
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
      //因为无法获取view宽度 需要自定义padding进行计算，暂时设置为0
      //padding 可以从wxParse.vue 的 transData.view = { imagePadding } 获取
      var recal = this.wxAutoImageCal(
        e.mp.detail.width,
        e.mp.detail.height,
        that
      );

      //修改自适应设备屏幕宽度主要在这里
      //原版的代码是修改Appdata的值,达到图片宽度高度动态变化的效果, 这里是修改props所以第一次加载不生效
      //下面都是和高度宽度有关的, 还没有看具体是哪一项生效
      //这样修改只会在第二次加载时会生效
      //在开发者工具,清除缓存,第一次加载时这些设置不能生效,切换页面才起作用
      e.mp.detail.width = recal.imageWidth;
      e.mp.detail.height = recal.imageheight;
      e.target.width = recal.imageWidth;
      e.target.height = recal.imageheight;
      that.node.attr.style[0] = "height:" + recal.imageheight + "px";
      that.node.attr.style[1] = "width:" + recal.imageWidth + "px";
      that.node.styleStr = "height:" + recal.imageheight + "px; width:" + recal.imageWidth + "px";
      that.node.width = recal.imageWidth;
      that.node.height = recal.imageheight;
    },

    // 计算视觉优先的图片宽高
    wxAutoImageCal: function(originalWidth, originalHeight, that) {
      //获取图片的原始长宽
      var windowWidth = 0, windowHeight = 0;
      var autoWidth = 0, autoHeight = 0;
      var results = {};
      // var padding = that.data[bindName].view.imagePadding;
      //因为无法获取view宽度 需要自定义padding进行计算，暂时设置为0
      //padding 可以从wxParse.vue 的 transData.view = { imagePadding } 获取
      var padding = 0;
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
}
</script>
