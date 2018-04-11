<!--**
 * author: F-loat <chaimaoyuan@foxmail.com>
 *
 * github地址: https://github.com/F-loat/mpvue-wxParse
 *
 * for: Mpvue框架下 微信小程序富文本解析
 */-->

<template>
<!--基础元素-->
<div class="wxParse">
  <block v-for="node of wxParseData.nodes" :key="node.index">
    <wxParseTemplate :node="node" :padding="wxParseData.view" />
  </block>
</div>
</template>

<script>
import HtmlToJson from './libs/html2json';
import wxParseTemplate from './components/wxParseTemplate0'

export default {
  name: 'wxParse',
  props: {
    content: {
      type: String,
      default() {
        return '<div class="color:red;">数据不能为空</div>'
      }
    },
    imageMode: {
      type: String,
      default() {
        return 'aspectFit'
      }
    },
    imagePadding: {
      type: Number,
      default() {
        return 0
      }
    },
    debug: {
      type: Boolean,
      default() {
        return false
      }
    }
  },
  components: {
    wxParseTemplate
  },
  computed: {
    wxParseData() {
      const { content, imageMode, imagePadding, debug } = this
      const transData = HtmlToJson(content, imageMode, debug);
      if (debug) console.log(JSON.stringify(transData, ' ', ' '));
      transData.view = { imagePadding };
      return transData;
    }
  }
}

</script>

<style>
@import url("./wxParse.css");
</style>
