# taroqrcode.vue

这个项目源自于[qrcode.vue](https://github.com/scopewu/qrcode.vue)
# 目前仅支持源码方式引入，请参考example
一款 Vue.js 二维码组件.

[![Build Status](https://travis-ci.org/scopewu/qrcode.vue.svg?branch=master)](https://travis-ci.org/scopewu/qrcode.vue)
[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/scopewu/qrcode.vue/blob/master/LICENSE)

## 快速开始

快速添加 `qrcode.vue` 组件到项目中

```bash
npm install --save qrcode.vue # yarn add qrcode.vue
```

```
dist/
|--- qrcode.vue.cjs.js         // CommonJS
|--- qrcode.vue.esm.js         // ES module
|--- qrcode.vue.browser.js     // UMD for browser or require.js or CommonJS
|--- qrcode.vue.browser.min.js // UMD Minimum size
```

## 使用

e.g.

```javascript
import { createApp } from 'vue'
import QrcodeVue from 'qrcode.vue'

createApp({
  data: {
    value: 'https://example.com',
  },
  template: '<qrcode-vue :value="value"></qrcode-vue>',
  components: {
    QrcodeVue,
  },
}).mount('#root')
```

或者，在独有单文件扩展 `*.vue` 中使用：

```html
<template>
  <qrcode-vue :value="value" :size="size" level="H" />
</template>
<script>
  import QrcodeVue from 'qrcode.vue'

  export default {
    data() {
      return {
        value: 'https://example.com',
        size: 300,
      }
    },
    components: {
      QrcodeVue,
    },
  }
</script>
```

## Component props

### `value`

- 类型：`string`
- 默认值：`''`

二维码的内容值。

### `size`

- 类型：`number`
- 默认值：`100`

二维码大小。

### `render-as`

- 类型：`string('canvas' | 'svg')`
- 默认值：`canvas`

生成二维码的 HTML 标签，可选 `canvas` 或 `svg`。其中 `svg` 可以用于 SSR。

### `margin`

- 类型：`number`
- 默认值：`0`

定义空白区的宽度应该是多少。

### `level`

- 类型：`string('L' | 'M' | 'Q' | 'H')`
- 默认值：`H`

二维码的容错能力等级，取值为 'L', 'M', 'Q', 'H' 之一。了解更多，[维基百科：QR_code](https://en.wikipedia.org/wiki/QR_code#Error_correction)。

### `background`

- 类型：`string`
- 默认值：`#ffffff`

二维码背景颜色。

### `foreground`

- 类型：`string`
- 默认值：`#000000`

二维码前景颜色。

### `class`

- 类型：`string`
- 默认值：`''`

传递给二维码根元素的类名。

## 软件许可

copyright &copy; 2021 scopewu, license by [MIT](https://github.com/scopewu/qrcode.vue/blob/master/LICENSE)
