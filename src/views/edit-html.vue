<template>
  <div class='cmcc'>
    <div class='cmcc-toolbar'>
      <el-button-group round>
        <el-button type='primary' icon='el-icon-plus'></el-button>
        <el-button type='primary' icon='el-icon-edit'></el-button>
        <el-button type='primary' icon='el-icon-delete'></el-button>
        <el-button type='primary' icon='el-icon-document'
          @click="saveLayoutView"></el-button>
      </el-button-group>
    </div>
    <flex-layout ref="flexlayout"></flex-layout>
  </div>
</template>
<script lang='ts'>
import { Vue, Component, Prop } from 'vue-property-decorator';
import FlexLayout from '@/components/FlexLayout.vue';

@Component({
  components: {
    'flex-layout': FlexLayout,
  },
})
export default class EditHtml extends Vue {

  public $refs!: {
    flexlayout: FlexLayout,
  };

  private saveLayoutView() {
    const layoutData = this.$refs.flexlayout.getLayoutData();
    this.$refs.flexlayout.setLayoutData(layoutData);
  }
}
</script>
<style lang='scss' scoped>
.cmcc {
  width: 100%;
  height: 100%;
  &-toolbar {
    position: absolute;
    top: 30px;
    right: 30px;
  }
}
</style>

