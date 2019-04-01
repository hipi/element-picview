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
  cursor: all-scroll;
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
  background-image: url(data:image/png;base64,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);
  cursor: pointer;
  background-size: 100%;
  z-index: 9999;
  user-select: none;
}
</style>

