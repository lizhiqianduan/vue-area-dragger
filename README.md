# vue-area-dragger
基于Vue的区域拖拽组件

## 安装
```
npm i @datagetter.cn/area-dragger
```
## 开源地址
https://github.com/lizhiqianduan/vue-area-dragger

## 参数/方法
| 参数/方法 | 描述 |
| -- | -- |
| imgUrl | 背景图地址 非必填 |
| moveWidth | 拖拽区宽 非必填 默认100|
| moveHeight | 拖拽区高 非必填 默认100|
| wrapWidth | 容器高 非必填 默认200|
| wrapHeight | 容器高 非必填 默认200|
| getAreaPosition() | 获取区域的位置 |
| getZoomRatio() | 获取背景的缩放比例 |
| async getImageSize() | 获取图片的尺寸 |
| slot[name=content] | vue插槽 |
| v-on:areamove | 区域移动时，触发的vue事件 |

## 使用方法 超简单
```html
<template>
  <div>
      <!-- 可通过imgUrl设置图片 -->
	<AreaDragger
		imgUrl="http://datagetter.cn:9000/datagetter.cn/public/common/datagetter_qrcode.png"
		@areamove="areamove"
		>
        <template slot="content">这里可自定义内容</template>
    </AreaDragger>
  </div>
</template>

<script>
import AreaDragger from '@datagetter.cn/area-dragger/lib/AreaDragger';

export default {
	components: { AreaDragger },
	methods: {
		areamove (areaPos,zoomRatio,imageSize) {
			console.log(areaPos,zoomRatio,imageSize);
		}
	}
}
</script>
```