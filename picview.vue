<template>
  <div class="picview">
    <a @click="seePic()" :style="{width:width , height: height }">
      <img :src="src">
    </a>
    <el-dialog
      class="picture"
      :visible.sync="picture"
      :close-on-click-modal="false"
      :fullscreen="true"
      @opened="scalePic"
    >
      <i @click="rotePic" class="xuzhuan" style="position:absolout"></i>
      <div ref="pic" class="main" @click="close($event)">
        <img
          v-show="isPicShow"
          :src="src"
          :width="picW"
          :height="picH"
          :style="{ transform: picTransform, top: picTop ,left:picLeft }"
          @mousedown.prevent="mousedown($event)"
          @mousemove.prevent="mousemove($event)"
          @mouseup.prevent="mouseup($event)"
        >
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      picture: false,
      scale: 1,
      roted: 0,
      picW: "",
      picH: "",
      isMoving: false,
      disX: 0,
      disY: 0,
      isPicShow: false,
      picTransform: "",
      picTop: 0,
      picLeft: 0
    };
  },
  props: {
    src: String,
    width: String,
    height: String
  },
  methods: {
    seePic() {
      this.picture = true;
    },
    scalePic() {
      const vm = this;

      vm.scale = 1;
      vm.roted = 0;
      vm.picTransform = "scale(1)";
      vm.countImg();
      window.onresize = function() {
        vm.countImg();
      };
      document.body.onmousewheel = function(event) {
        event = event || window.event;
        if (vm.picture == true) {
          if (event.deltaY > 0) {
            vm.scale = vm.scale > 0.2 ? vm.scale - 0.1 : vm.scale;
          } else {
            vm.scale += 0.1;
          }
          vm.picTransform =
            "scale(" + vm.scale + ") rotate(" + vm.roted + "deg)";
        }
      };
    },
    rotePic() {
      const vm = this;
      vm.roted = Number(vm.roted) + 90;
      vm.picTransform = "scale(" + vm.scale + ") rotate(" + vm.roted + "deg)";
    },
    close(e) {
      if (e.target === this.$refs.pic.childNodes[0]) {
        return;
      }
      this.picture = false;
      e.stopPropagation();
    },
    mousedown(e) {
      const vm = this;
      var oDiv = vm.$refs.pic.childNodes[0];
      vm.isMoving = true;
      vm.disX = e.clientX - oDiv.offsetLeft;
      vm.disY = e.clientY - oDiv.offsetTop;
    },
    mousemove(e) {
      const vm = this;
      if (
        vm.isMoving &&
        e.clientY > 0 &&
        e.clientY < vm.$refs.pic.clientHeight &&
        e.clientX > 0 &&
        e.clientX < vm.$refs.pic.clientWidth
      ) {
        vm.picTop = e.clientY - vm.disY + "px";
        vm.picLeft = e.clientX - vm.disX + "px";
      }
    },
    mouseup() {
      const vm = this;
      vm.isMoving = false;
      return false;
    },
    countImg() {
      const vm = this;

      let picW = vm.$refs.pic.childNodes[0].naturalWidth;
      let picH = vm.$refs.pic.childNodes[0].naturalHeight;
      let Width = vm.$refs.pic.offsetWidth;
      let Height = vm.$refs.pic.offsetHeight;
      if (Width / Height <= picW / picH) {
        vm.picW = Width;
        vm.picH = `${(Number(picH) * Width) / Number(picW)}`;
        vm.picTop = `${(Height - vm.picH) / 2}px`;
        vm.picLeft = 0;
        vm.isPicShow = true;
      } else {
        vm.picH = Height;
        vm.picW = `${(Number(picW) * Height) / Number(picH)}`;
        vm.picLeft = `${(Width - vm.picW) / 2}px`;
        vm.picTop = 0;
        vm.isPicShow = true;
      }
    }
  }
};
</script>
<style scoped>
.picview {
  user-select: none;
}
a {
  display: inline-block;
}
a img {
  width: 100%;
  height: 100%;
}
.main {
  width: 100%;
  height: 100%;
}
.main img {
  position: absolute;
}
.picture /deep/ .el-dialog__header {
  display: none;
}
.picture /deep/ .el-dialog__body {
  height: 100%;
  width: 100%;
  padding: 0;
}
.picture /deep/ .el-dialog {
  background: transparent;
  overflow: hidden;
}
.picture /deep/ .xuzhuan:after {
  content: "";
  position: absolute;
  width: 40px;
  height: 40px;
  right: 50%;
  margin-right: -20px;
  top: 90%;
  background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADQAAAA0CAMAAADypuvZAAAAAXNSR0IArs4c6QAAAIdQTFRFAgIC/Pz8AgIC+Pj4AgICDg4PAgICAAAAAgICAgICKior6+vrAgICGRkaBQUGra2t3d3dUVFRREREycnLd3d3np6eNTU1vb29AgICAgICAgICAgICAgICAgICkpKSs7OzX19f8fHx5ubm0tLSwsLDt7e34uLia2tsAgICAgICgYGBh4eH////tlVpFgAAACx0Uk5Tmf2D+lmdbwAQkKTzNp+a0uuwrOC8zKfZaGFLBB0GxtW09vDl3NbtuHYywcJ7kqSzAAACGElEQVRIx52W6ZaCMAyFWwrHUoRCWccFQRCVyfs/3zirTS0Dh/tP8DvGLDchmxUilmd861aOT4jvVO6WL4H4ziFIzo7PQIeKWFQd/oH4G5nQG5+C7j6ZlH+3qnux/Ct3/wodKzKj6mhCxxuZ1e2Iof0C5kHtEeSSRXJ16E4WavuEuL8U8vkfZNbUU2M/BOf4kgnPrPIvdDCQpmRAWdsyCoFMDOzwA+EKCUlpfKmvSZPlfQCsDnG1viGOHqoOhnfx8yFSsoVSoC/wL2iHmAH6Qn/QxJAiavcF6fMjOrjgcIiK4aT/L+cT0qOLckhDM83JGbIIxUc2W/19GxSvxRnpIFCBid5BnoTcUtEwhTHSe4noCRfBUNj6IGN60LcHpOWhgZO1ecTAlJ4JstH6Lqe1FYp6PT7/AWkvU5bZ+7sg2numhumxu9qqmze0lkuo+bPQiTJKaaqH58yGJ3oK9NmBDk75VCI8GUgPpVwr7hWkPRGR8nBxtTYq2kEs8QnUsN4J6nmIG6ORMNtPRXj1mEMYSShNKyGqHL2XIdQHqohBGlSRgoxexh0Zy2PiLijCpIO0eDUWbGFJDHH2NwhFHUAvbBaGzVKVwDqZJUo14ykGmodWszRsOcw6Ciw4n1sG7NJ4E7aMfOKRwzDJu3MQDOm7CicXwLpVs26prVqf6xb1upNg3fGx8sxZd1CtPN1+j8Tb95F4W3gkLtEHruE0r22jsPsAAAAASUVORK5CYII=);
  cursor: pointer;
  background-size: 100%;
  z-index: 9999;
  user-select: none;
}
</style>
