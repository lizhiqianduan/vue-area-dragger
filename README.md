# vue-area-dragger
基于Vue的区域拖拽组件

## 使用方法 超简单
```html
<template>
  <div id="app">
	<Dragger
		imgUrl="http://www.lizhiqianduan.com/wp-content/uploads/image/20180704/1530690513137706.png"
		@areamove="areamove"
		></Dragger>
  </div>
</template>

<script>
import Dragger from '@datagetter.cn/area-dragger/lib/AreaDragger';

export default {
	components: { Dragger },
	methods: {
		areamove () {
			console.log(areaPos,zoomRatio,imageSize);
		}
	}
}
</script>

```