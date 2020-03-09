<template>
  <div class="wrapper" @mouseup="onMouseUp" @mousemove.prevent="onMouseMove">
    <span>支持滚轮事件与拖动</span>
    <img
      ref="img"
      class="main-image"
      @click.stop
      @mousewheel="onMouseWheel"
      @mousedown.prevent="onImageMouseDown"
      @load="onImgLoad"
      :style="{
        margin: `${y}px 0 0 ${x}px`,
        transform: `translate(-50%,-50%)scale(${scale})rotate(${rotateDeg}deg)`
      }"
      :src="previewUrl"
    />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

interface VueElement extends Vue {
   width: number;
   height: number;
}

let mousedown = false;
let imageMouseDown = false;
let sLeft = 0;
let sTop = 0;

@Component({})
export default class Drag extends Vue {
  private scale = 1;
  private x = 0;
  private y = 0;
  private w = 0;
  private h = 0;
  private rotateDeg = 0;
  private previewUrl =
    "https://zhiyi-web.oss-cn-hangzhou.aliyuncs.com/20200302/7895e670eb2c3ae6528c0fb1080fe1fc8a5a5e2d9_66.jpg";

  private initZoom() {
    this.scale = 0.9;
    this.x = 0;
    this.y = 0;
    this.w = 0;
    this.h = 0;
    this.onImgLoad();
  }

  private zoom(val: number) {
    mousedown = true;
    this.beginZoom(val);
  }

  private beginZoom(val: number) {
    this.scale += this.scale * val * 0.05;
    requestAnimationFrame(() => {
      if (mousedown) {
        this.zoom(val);
      }
    });
  }

  private onImageMouseDown(e: DragEvent) {
    imageMouseDown = true;
    sLeft = e.screenX - this.x;
    sTop = e.screenY - this.y;
  }

  private onImgLoad() {
    this.w = -(this.$refs.img as VueElement).width / 2;
    this.h = -(this.$refs.img as VueElement).height / 2;
  }

  private onMouseUp() {
    imageMouseDown = false;
    mousedown = false;
  }

  private onMouseMove(e: DragEvent) {
    if (imageMouseDown) {
      this.x = e.screenX - sLeft;
      this.y = e.screenY - sTop;
    }
  }

  private onMouseWheel(e: WheelEvent) {
    this.beginZoom(-e.deltaY / 75);
  }
}
</script>
<style lang="scss" scoped>
.wrapper {
  position: fixed;
  z-index: 1000;
  height: 100vh;
  width: 100vw;
  top: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.2);
}
img {
  cursor: pointer;
  position: absolute;
  left: 50%;
  top: 50%;
  max-width: 50%;
  max-height: 50%;
}
</style>
