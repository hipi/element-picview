<template>
    <div>
        <a @click="pictureTrue(src)" :style="{width:width , height: height }">
            <img :src="src" alt="">
        </a>
        <el-dialog class="picture" size="full" id="picture" v-model="picture" top="8%" :close-on-click-modal="false">
            <div id="pic" class="main">
                <img ref="pics" :src="pictureSrc" alt="">
            </div>
        </el-dialog>
    </div>
</template>
<script>
let vm;
export default {
    name: 'picview',
    data() {
        return {
            picture: false,
            scale: 1,
            pictureSrc: '',
        }
    },
    props: {
        src: String,
        width: String,
        height: String
    },
    methods: {
        pic() {
            document.body.onmousewheel = function (event) {
                event = event || window.event;

                if (vm.picture == true) {
                    if (event.deltaY > 0) {
                        vm.scale = vm.scale > 0.2 ? vm.scale - 0.1 : vm.scale;
                    } else {
                        vm.scale += 0.1;
                    }
                    $('#pic img').css("transform", "scale(" + vm.scale + ")")
                };
            };
            /* 拖拽 */
            setTimeout(function () {
                if (vm.picture) {
                    var oDiv = vm.$refs.pics;

                    oDiv.onmousedown = function (en) {
                        var ev = ev || event;
                        var disX = en.clientX - oDiv.offsetLeft;
                        var disY = en.clientY - oDiv.offsetTop;
                        if (oDiv.setCapture) {
                            oDiv.setCapture();
                        }
                        document.onmousemove = function (en) {
                            var ev = ev || event;
                            var left = $("#pic").offset().left;
                            if (en.clientY > 0 && en.clientY < $("#pic").height() && en.clientX > 0 && en.clientX < $("#pic").width()) {
                                oDiv.style.top = en.clientY - disY + 'px';
                                oDiv.style.left = en.clientX - disX + 'px';
                            }
                        }
                        document.onmouseup = function () {
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
            /* 还原 */
            vm.scale = 1;
            $('#pic img').css("transform", "scale(1)");
            $('#pic img').css("top", "0");
            $('#pic img').css("left", "0");
            vm.pic();
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
            cursor: move;
            width: 100%;
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
