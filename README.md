# mpvue-wxParse 适用于 Mpvue 的微信小程序富文本解析组件

> 支持 Html、Markdown 转 Wxml 可视化，修改自: [wxParse](https://github.com/icindy/wxParse)

[![npm package](https://img.shields.io/npm/v/mpvue-wxparse.svg)](https://npmjs.org/package/mpvue-wxparse)
[![npm downloads](http://img.shields.io/npm/dm/mpvue-wxparse.svg)](https://npmjs.org/package/mpvue-wxparse)


## 扫码体验
![小程序码](./static/qrcode.jpg)


## 属性

| 名称              | 类型           | 默认值        | 描述              |
| -----------------|--------------- | ------------- | ---------------- |
| content          | String         | —             | 渲染内容          |
| noData           | String         | 数据不能为空   | 空数据时的渲染展示 |
| image            | Object         | 见下文        | 图片相关参数       |
| debug            | Boolean        | false         | 调试开关          |

### image 对象具体属性

| 名称              | 类型           | 默认值        | 描述                |
| -----------------|--------------- | ------------- | ------------------ |
| mode             | String         | 'aspectFit'   | 图片裁剪、缩放的模式 |
| padding          | Number         | 0             | 图片内边距          |
| lazyLoad         | Boolean        | false         | 图片懒加载          |


## 基本使用方法

* 安装

``` bash
npm i mpvue-wxparse
```

* 使用

``` vue
<template>
  <div>
    <wxParse :content="article" />
  </div>
</template>

<script>
import wxParse from 'mpvue-wxparse'

export default {
  components: {
    wxParse
  },
  data () {
    return {
      article: '<div>我是HTML代码</div>'
    }
  }
}
</script>
```


## 渲染 Markdown

> 先将 markdown 转换为 html 即可

``` bash
npm install marked
```

``` js
import marked from 'marked'
import wxParse from 'mpvue-wxparse'

export default {
  components: {
    wxParse
  },
  data () {
    return {
      article: marked(`#hello, markdown!`)
    }
  }
}
```


## 常见问题

* 打包时出错 `ERROR in static/js/vendor.js from UglifyJs`

参照以下配置使 babel 处理 mpvue-wxparse，或直接关闭 UglifyJs 插件

``` js
// webpack.base.conf.js
{
  test: /\.js$/,
  include: [
    resolve('src'),
    resolve('node_modules/mpvue-wxparse')
  ],
  use: [
    'babel-loader',
    {
      loader: 'mpvue-loader',
      options: {
        checkMPEntry: true
      }
    }
  ]
}
```


## 感谢

[@stonewen](https://github.com/stonewen)| [@Daissmentii](https://github.com/Daissmentii)        | [@wuyanwen](https://github.com/wuyanwen)           | [@vcxiaohan](https://github.com/vcxiaohan)
