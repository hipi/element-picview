<template>
    <div>
        <a @click="pictureTrue(src)" :style="{width:width , height: height }">
            <img :src="src">
        </a>
        <el-dialog class="picture" size="full" v-model="picture" :before-close="whenClose" @open="whenOpen" :close-on-click-modal="false">
            <i @click="rote" class="xuzhuan" style="position:absolout"></i>
            <div ref="pic" class="main" @click="close($event)">

                <img :src="pictureSrc" alt="" :width="picW" :height="picH" @click="clickimg($event)">
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
    name: "picview",
    data() {
        return {
            picture: false,
            scale: 1,
            pictureSrc: "",
            picW: "",
            picH: ""
        };
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
                if (vm.picview.picture == true) {
                    if (event.deltaY > 0) {
                        vm.picview.scale =
                            vm.picview.scale > 0.2
                                ? vm.picview.scale - 0.1
                                : vm.picview.scale;
                    } else {
                        vm.picview.scale += 0.1;
                    }
                    vm.$refs.pic.childNodes[0].style.transform =
                        "scale(" +
                        vm.picview.scale +
                        ") rotate(" +
                        vm.picview.roted +
                        "deg)";
                }
            };
            /* 拖拽 */
            setTimeout(function() {
                if (vm.picview.picture) {
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
                            if (
                                en.clientY > 0 &&
                                en.clientY < vm.$refs.pic.clientHeight &&
                                en.clientX > 0 &&
                                en.clientX < vm.$refs.pic.clientWidth
                            ) {
                                oDiv.style.top = en.clientY - disY + "px";
                                oDiv.style.left = en.clientX - disX + "px";
                            }
                        };
                        document.onmouseup = function() {
                            document.onmousemove = null;
                            if (oDiv.releaseCapture) {
                                oDiv.releaseCapture();
                            }
                        };
                        return false; //阻止默认行为（如果页面中有文字，则会默认拖动文字），ie8及一下不行
                    };
                }
            }, 0);
        },
        rote() {
            vm.picview.roted = Number(vm.picview.roted) + 90;
            vm.$refs.pic.childNodes[0].style.transform =
                "scale(" +
                vm.picview.scale +
                ") rotate(" +
                vm.picview.roted +
                "deg)";
        },
        pictureTrue(src) {
            vm.picview.pictureSrc = src;
            vm.picview.picture = true;
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
            if (Width / Height <= picW / picH) {
                vm.picview.picW = Width;
                vm.picview.picH = `${Number(picH) * Width / Number(picW)}`;
                vm.$refs.pic.childNodes[0].style.top = `${(Height -
                    vm.picview.picH) /
                    2}px`;
                vm.$refs.pic.childNodes[0].style.left = 0;
            } else {
                vm.picview.picH = Height;
                vm.picview.picW = `${Number(picW) * Height / Number(picH)}`;
                vm.$refs.pic.childNodes[0].style.left = `${(Width -
                    vm.picview.picW) /
                    2}px`;
                vm.$refs.pic.childNodes[0].style.top = 0;
            }
        },
        whenOpen() {
            setTimeout(function() {
                if (vm.$refs.pic.childNodes[0]) {
                    vm.picview.scale = 1;
                    vm.picview.roted = 0;
                    vm.$refs.pic.childNodes[0].style.transform = "scale(1)";
                    vm.countImg();
                    window.onresize = function() {
                        vm.countImg();
                    };
                }
            }, 0);
        },
        close(e) {
            vm.picview.picture = false;
            e.stopPropagation();
        },
        clickimg(e) {
            e.stopPropagation();
        }
    },
    watch: {},
    mounted() {
        vm = this;
    }
};
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
.picture .xuzhuan:after {
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
