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
import AreaDragger from '../lib/AreaDragger';

export default {
	components: { AreaDragger },
	methods: {
		areamove (areaPos,zoomRatio,imageSize) {
			console.log(areaPos,zoomRatio,imageSize);
		}
	}
}
</script>