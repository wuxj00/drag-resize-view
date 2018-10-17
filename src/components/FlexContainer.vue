<template>
  <div class="cmcc" v-bind:style="getStyle()">
    <div class="cmcc-row">
      <div class="cmcc-top cmcc-left"></div>
      <div class="cmcc-top cmcc-center cmcc-flex"></div>
      <div class="cmcc-top cmcc-right"></div>
    </div>
    <div class="cmcc-row cmcc-flex">
      <div class="cmcc-middle cmcc-left"></div>
      <div class="cmcc-middle cmcc-center cmcc-flex"
        :draggable="true"></div>
      <div class="cmcc-middle cmcc-right"></div>
    </div>
    <div class="cmcc-row">
      <div class="cmcc-bottom cmcc-left"></div>
      <div class="cmcc-bottom cmcc-center cmcc-flex"></div>
      <div class="cmcc-bottom cmcc-right"></div>
    </div>
  </div>
</template>
<script lang="ts">
import { Vue, Component, Prop } from 'vue-property-decorator';

@Component
export default class EditHtml extends Vue {

  @Prop({ default: 150 }) private w?: number;
  @Prop({ default: 150 }) private h?: number;

  private getStyle() {
    return {
      height: this.h + 'px',
      width: this.w + 'px',
    };
  }

}
</script>
<style lang="scss" scoped>
.cmcc {
  min-width: 100px;
  min-height: 100px;
  background-color: lightgray;
  display: flex;
  flex-flow: column nowrap;
  &-top{
    height: 5px;
    cursor: n-resize;
  }
  &-bottom{
    height: 5px;
    cursor: s-resize;
  }
  &-left{
    width: 5px;
    cursor: w-resize;
  }
  &-right{
    width: 5px;
    cursor: e-resize;
  }

  &-row {
    display: flex;
    flex-flow: row nowrap;
  }
  &-flex{
    flex:1 1 auto;
  }
}
 .cmcc-top.cmcc-left{
  cursor: nw-resize;
}
.cmcc-left.cmcc-bottom{
  cursor: sw-resize;
}
.cmcc-right.cmcc-top{
  cursor: ne-resize;
}
.cmcc-right.cmcc-bottom{
  cursor: se-resize;
}
</style>

