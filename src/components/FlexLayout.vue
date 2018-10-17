<template>
    <grid-layout
        :layout='layout'
        :col-num='colNum'
        :row-height='(height - (margin[0] + 2) * rowNum) / rowNum'
        :is-draggable='true'
        :is-resizable='true'
        :is-mirrored='false'
        :vertical-compact='true'
        class="cmcc"
        v-bind:style="getWrapStyle()"
        @layout-updated="layoutUpdatedEvent"
        :margin='margin'
        :max-rows="rowNum"
        :use-css-transforms='true'>
      <grid-item v-for='(item, index) in layout'
        :x='item.x'
        :y='item.y'
        :w='item.w'
        :h='item.h'
        :key = 'index'
        :i='item.i'
        @resize="resizeEvent"
        @move="moveEvent"
        @resized="resizedEvent"
        @moved="movedEvent">
          {{item.i}}
      </grid-item>
    </grid-layout>
</template>
<script lang='ts'>
import { Vue, Component, Prop } from 'vue-property-decorator';
import VueGridLayout from 'vue-grid-layout';
import { LayoutItem } from '@/interfaces/LayoutItem.ts';
import { Loading } from 'element-ui';

@Component({
  components: {
    VueGridLayout,
  },
})
export default class FlexLayout extends Vue {

  @Prop({ default: () => [10, 10] }) private margin?: [number, number];
  @Prop({ default: 6 }) private colNum?: number;
  @Prop({ default: 3 }) private rowNum?: number;

  @Prop({ default: 800 }) private width?: number;
  @Prop({ default: 600 }) private height?: number;

  private layout: LayoutItem[] = [
    {
      x: 0,
      y: 0,
      w: 1,
      h: 1,
      i: '0',
    },
  ];

  public setLayoutData(layout: LayoutItem[] = []): any {
    this.layout = layout;
  }

  public getLayoutData(): LayoutItem[] {
    const cols: number = this.colNum as number;
    const rows: number = this.rowNum as number;
    return this.layout.filter((item) => {
      const { x, y, w, h } = item;
      return (x + w <= cols && y + h <= rows);
    });
  }

  private created() {
    this.initDefaultLayoutGrid();
  }

  private initDefaultLayoutGrid() {
    const layout: LayoutItem[] = [];
    const cols: number = this.colNum as number;
    const rows: number = this.rowNum as number;
    for (let row = 0; row < rows; row ++) {
      for (let col = 0; col < cols; col ++) {
        layout.push({
          x: col,
          y: row,
          w: 1,
          h: 1,
          i: `${row}-${col}`,
        });
      }
    }
    this.setLayoutData(layout);
  }
  private getWrapStyle() {
    return {
      height: this.height + 'px',
      width: this.width + 'px',
    };
  }

  private mapLayoutToUnit(layoutView: LayoutItem[]): LayoutItem[] {
    const smallUnits: LayoutItem[] = [];
    layoutView.forEach((unit) => {
      const { x, y, w, h, i } = unit;
      if ( w > 1 || h > 1) {
        for (let row = 0; row < w; row ++ ) {
          for (let col = 0; col < h; col ++ ) {
            smallUnits.push({
              x: x + row,
              y: y + col,
              w: 1,
              h: 1,
              i: `${i}-${row}-${col}`,
            });
          }
        }
      } else {
        smallUnits.push(Object.assign({}, unit));
      }
    });
    return smallUnits;
  }
  private getEmptyUnits(unitViews: LayoutItem[]): LayoutItem[] {
    const emptyUnits: LayoutItem[] = [];
    const cols: number = this.colNum as number;
    const rows: number = this.rowNum as number;

    for (let row = 0; row < rows; row ++) {
      const rowData: LayoutItem[] = unitViews.filter((unit) => unit.y === row).sort((a: LayoutItem, b: LayoutItem) => a.x - b.x);
      if (rowData.length !== cols) {
        let compareIndex = 0;
        for (let col = 0; col < cols; col ++) {
          const unit = rowData[compareIndex];
          if (!unit || (unit.x !== col)) {
            emptyUnits.push({
              x: col,
              y: row,
              w: 1,
              h: 1,
              i: `${row}-${col}-empty`,
            });
          } else {
            compareIndex = compareIndex + 1;
          }
        }
      }
    }
    return emptyUnits;
  }
  private layoutUpdatedEvent(newLayout: LayoutItem[]) {
    const avaLayoutData = this.getLayoutData();
    const smallUnits = this.mapLayoutToUnit(avaLayoutData);
    const emptyUnits = this.getEmptyUnits(smallUnits);
    if ((newLayout.length !== avaLayoutData.length)
      || (emptyUnits.length !== 0)) {
      this.$nextTick(() => {
        this.setLayoutData(avaLayoutData.concat(emptyUnits));
      });
    }
  }

  private moveEvent(i: any, newX: any, newY: any) {
    // console.log("MOVE i=" + i + ", X=" + newX + ", Y=" + newY);
  }
  private resizeEvent(i: any, newH: any, newW: any, newHPx: any, newWPx: any) {
    // console.log("RESIZE i=" + i + ", H=" + newH + ", W=" + newW + ", H(px)=" + newHPx + ", W(px)=" + newWPx);
  }
  private movedEvent(i: any, newX: any, newY: any) {
    // console.log("MOVED i=" + i + ", X=" + newX + ", Y=" + newY);
    // return;
  }
  private resizedEvent(i: any, newH: any, newW: any, newHPx: any, newWPx: any) {
    // console.log("RESIZED i=" + i + ", H=" + newH + ", W=" + newW + ", H(px)=" + newHPx + ", W(px)=" + newWPx);
  }
}
</script>
<style lang='scss' scoped>
.cmcc {
  width: 100%;
  height: 100%;
  display: inline-block;
  border: solid 1px red;
  &-toolbar {
    position: absolute;
    top: 30px;
    right: 30px;
  }
}
.vue-grid-item {
  border: solid 1px grey;
  overflow: hidden;
}
</style>
