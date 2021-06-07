<template>
	<div class="cropper-root" :style="{ width:wrapWidth+'px', height: wrapHeight + 'px' }">
		<div
			class="background"
			:style="{
				backgroundImage: 'url(' + imgUrl + ')',
				width: imageSize.width * zoomRatio + 'px',
				height: imageSize.height * zoomRatio + 'px'
			}"
			id="background"
		>
			<div
				class="calculator"
				@mousedown.self="mousedown"
				id="sx-drag-selector"
				:style="{
					left: left + 'px',
					top: top + 'px',
					width: moveWidth + 'px',
					height: moveHeight + 'px'
				}"
			>
			</div>
		</div>
	</div>
</template>

<script>

export default {
  name: "AreaDragger",
  props: {
    imgUrl: String,
    // 选框的高宽
    moveWidth: {
      type:Number,
      default:100
    },
    moveHeight: {
      type:Number,
      default:100
    },

    wrapWidth: {
      type:Number,
      default:200
    },
    wrapHeight: {
      type:Number,
      default:200
    }
  },
  data() {
    return {
      winHeight: window.innerHeight,

      //背景图缩放比例
      zoomRatio: 1,

      //选框位置
      left: 0,
      top: 0,

      // 鼠标拖动开始的位置
      startX: 0,
      startY: 0,

      // 鼠标拖动开始时，选框的位置
      startLeft: 0,
      startTop: 0,

      // image大小
      imageSize: {
        width:0,
        height:0
      }
    };
  },
  async mounted() {
    let self = this;
    // 获取图片尺寸
    let res = await this.getImageSize(self.imgUrl);
    if (!res) this.$message.error("获取图片尺寸失败！");
    this.imageSize = res;
    
    if (this.wrapWidth > this.imageSize.width && this.wrapHeight <= this.imageSize.height) {
      this.zoomRatio = this.wrapWidth / this.imageSize.width;
      console.log("图片宽度超出，高度未超出", this.zoomRatio);
    }
    if (this.wrapWidth <= this.imageSize.width && this.wrapHeight > this.imageSize.height) {
      this.zoomRatio = this.wrapHeight / this.imageSize.height;
      console.log("图片高度超出，宽度未超出", this.zoomRatio);
    }

    if (this.wrapWidth <= this.imageSize.width && this.wrapHeight <= this.imageSize.height) {
      this.zoomRatio = this.wrapHeight / this.imageSize.height;
      console.log("图片超出容器", this.zoomRatio);
    }

    if (this.wrapWidth > this.imageSize.width && this.wrapHeight > this.imageSize.height) {
      this.zoomRatio =
        this.wrapWidth / this.imageSize.width > this.wrapHeight / this.imageSize.height
          ? this.wrapWidth / this.imageSize.width
          : this.wrapHeight / this.imageSize.height;
      console.log("图片均未超出", this.zoomRatio);
    }
    // console.log
  },
  methods: {
    /**
     * 获取当前区域的位置信息
     */
    getAreaPosition() {
      let emitData = {
        x: parseInt(this.left / this.zoomRatio),
        y: parseInt(this.top / this.zoomRatio),
        width: parseInt(this.moveWidth / this.zoomRatio),
        height: parseInt(this.moveHeight / this.zoomRatio),
      };
      console.log(emitData);
      return emitData;
      // this.$emit("onCropEnd", emitData);
    },

    getZoomRatio(){
      return this.zoomRatio;
    },



    mousedown(e) {
      console.log("mousedown", e, this);

      this.startX = e.clientX;
      this.startY = e.clientY;
      this.startLeft = this.left;
      this.startTop = this.top;

      var drag = document.getElementById("sx-drag-selector");
      e = e || window.event;
      if (typeof drag.setCapture != "undefined") {
        drag.setCapture();
      }
      document.onmousemove = this.mousemove;
      document.onmouseup = this.mouseup;
    },
    mouseup() {
      var drag = document.getElementById("sx-drag-selector");
      document.onmousemove = null;
      document.onmouseup = null;
      if (typeof drag.releaseCapture != "undefined") {
        drag.releaseCapture();
      }
    },
    mousemove(e) {
      let self = this;
      e = e || window.event;
      var diffX = e.clientX - this.startX;
      var diffY = e.clientY - this.startY;
      // console.log(diffX, diffY);

      var left = this.startLeft + diffX;
      var top = this.startTop + diffY;

      let backWidth = self.imageSize.width * self.zoomRatio;
      let backHeight = self.imageSize.height * self.zoomRatio;

      if (left < 0) {
        left = 0;
      } else if (left > parseInt(backWidth) - parseInt(self.moveWidth)) {
        left = parseInt(backWidth) - parseInt(self.moveWidth);
      }
      if (top < 0) {
        top = 0;
      } else if (top > parseInt(backHeight) - parseInt(self.moveHeight)) {
        top = parseInt(backHeight) - parseInt(self.moveHeight);
      }
      // console.log(left, top);
      self.left = left;
      self.top = top;
      this.$emit('areamove',this.getAreaPosition(),this.getZoomRatio(),this.imageSize)
    },

    /**
     * 获取图片尺寸
     */
    async getImageSize(url) {
      let self = this;
      var img = new Image();
      img.src = url;
      return new Promise((resolve, reject) => {
        img.onerror = function () {
          resolve(false);
          return false;
        };
        img.onload = function () {
          console.log(img.width + " " + img.height);
          self.imageSize.width = img.width;
          self.imageSize.height = img.height;
          img.onload = null; //避免重复加载
          resolve({width:self.imageSize.width,height:self.imageSize.height});
        };
      });
    },
  },
};
</script>

<style scoped lang="stylus">
.cropper-root {
	width: 100%;
	height: 100%;
	background-color: rgb(0, 0, 0);
	z-index: 99999;
}

.calculator {
	position: absolute;
	display: inline-block;
	width: 200px;
	height: 200px;
	cursor: move;
	background: rgba(255, 0, 0, 0.5);
	left: 0;
	top: 0;
}

.background {
	background-repeat: no-repeat;
	background-size: 100% 100%;
	margin: 0 auto;
	position: relative;
	margin-top: 40px;
}

.buttons {
	width: 100%;
	text-align: center;
	margin-top: 20px;
}

.buttons button {
	width: 50px;
	height: 30px;
	display: inline-block;
	margin-left: 10px;
	background-color: rgb(24, 144, 255);
	outline: none;
	border: none;
	border-radius: 5px;
	color: white;
	cursor: pointer;

	&:active {
		background-color: rgb(55, 144, 255);
	}
}
</style>
