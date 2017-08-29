<template>
    <div>
        <a @click="pictureTrue(src)" :style="{width:width , height: height }">
            <img :src="src">
        </a>
        <el-dialog class="picture" size="full" v-model="picture" top="8%" :before-close="whenClose" @open="whenOpen" :close-on-click-modal="false">
            <div ref="pic" class="main">
                <img :src="pictureSrc" alt="" :width="picW" :height="picH">
            </div>
        </el-dialog>
    </div>
</template>
<script>
/* 
* 图片查看器
* @param  src  width height
*/
let vm;
export default {
    name: 'picview',
    data() {
        return {
            picture: false,
            scale: 1,
            pictureSrc: '',
            picW: '',
            picH: '',
        }
    },
    props: {
        src: String,
        width: String,
        height: String
    },
    methods: {
        pic() {
            document.body.onmousewheel = function(event) {
                event = event || window.event;
                if (vm.picture == true) {
                    if (event.deltaY > 0) {
                        vm.scale = vm.scale > 0.2 ? vm.scale - 0.1 : vm.scale;
                    } else {
                        vm.scale += 0.1;
                    }
                    vm.$refs.pic.childNodes[0].style.transform = "scale(" + vm.scale + ")";
                };
            };
            /* 拖拽 */
            setTimeout(function() {
                if (vm.picture) {
                    var oDiv = vm.$refs.pic.childNodes[0];
                    oDiv.onmousedown = function(en) {
                        var ev = ev || event;
                        var disX = en.clientX - oDiv.offsetLeft;
                        var disY = en.clientY - oDiv.offsetTop;
                        if (oDiv.setCapture) {
                            oDiv.setCapture();
                        }
                        document.onmousemove = function(en) {
                            var ev = ev || event;
                            if (en.clientY > 0 && en.clientY < vm.$refs.pic.clientHeight && en.clientX > 0 && en.clientX < vm.$refs.pic.clientWidth) {
                                oDiv.style.top = en.clientY - disY + 'px';
                                oDiv.style.left = en.clientX - disX + 'px';
                            }
                        }
                        document.onmouseup = function() {
                            document.onmousemove = null;
                            if (oDiv.releaseCapture) {
                                oDiv.releaseCapture()
                            }
                        }
                        return false; //阻止默认行为（如果页面中有文字，则会默认拖动文字），ie8及一下不行
                    }
                }
            }, 0)
        },
        pictureTrue(src) {
            vm.pictureSrc = src;
            vm.picture = true;
            vm.pic();
        },
        whenClose(done) {
            done();
        },
        countImg() {
            let picW = vm.$refs.pic.childNodes[0].naturalWidth;
            let picH = vm.$refs.pic.childNodes[0].naturalHeight;
            let Width = vm.$refs.pic.offsetWidth;
            let Height = vm.$refs.pic.offsetHeight;
            if (picW >= picH) {
                vm.picW = Width;
                vm.picH = `${Number(picH) * Width / Number(picW)}`;
                vm.$refs.pic.childNodes[0].style.top = `${(Height - vm.picH) / 2}px`;
                vm.$refs.pic.childNodes[0].style.left = 0;
            } else {
                vm.picH = Height;
                vm.picW = `${Number(picW) * Height / Number(picH)}`;
                vm.$refs.pic.childNodes[0].style.left = `${(Width - vm.picW) / 2}px`;
                vm.$refs.pic.childNodes[0].style.top = 0;
            }
        },
        whenOpen() {
            setTimeout(function() {
                if (vm.$refs.pic.childNodes[0]) {
                    vm.scale = 1;
                    vm.$refs.pic.childNodes[0].style.transform = "scale(1)";
                    vm.countImg();
                    window.onresize = function() {
                        vm.countImg();
                    }
                }
            }, 0)
        }
    },
    watch: {
    },
    mounted() {
        vm = this;
    }
}
</script>
<style lang="less" scoped>
a {
    display: block;
    img {
        width: 100%;
        height: 100%;
    }
}

.picture {
    .main {
        position: relative;
        left: 0;
        right: 0;
        bottom: 0;
        top: 0;
        height: 100%;
        margin-top: -20px;
        overflow: hidden;
        img {
            cursor: move; // width: 100%;
            border-radius: 8px;
            position: absolute;
        }
    }
}
</style>
<style lang="less">
.picture {
    .el-dialog {
        background: transparent;
    }
    .el-dialog__body {
        height: 100%;
    }
    .el-dialog__headerbtn {
        z-index: 99;
        position: absolute;
        right: 6px;
        background: transparent;
        top: 6px;
    }
}
</style>
