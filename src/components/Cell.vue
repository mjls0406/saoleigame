<template>
  <td
    :class="{
      boom: cellData.isShowBoom,
      'mine-clear': cellData.isClear,
      marked: cellData.isMarked
    }"
    @mousedown="cellClick"
    :style="{ width: size.w + 'px', height: size.h + 'px' }"
    class="cell"
  >
    <template v-if="cellData.isBoom"> </template>
    <template v-else>
      <span
        :style="{ color: getNumColor(cellData.data) }"
        v-if="cellData.data != 0 && cellData.isClear"
        >{{ cellData.data }}</span
      >
    </template>
  </td>
</template>

<script>
import EventBus from "../eventBus";
export default {
  name: "cell",
  data() {
    return {};
  },
  methods: {
    getNumColor(num) {
      switch (num) {
        case 1:
          return "#fff";
        case 2:
          return "#c6a";
        case 3:
          return "#08a";
        case 4:
          return "#0a2";
        case 5:
          return "#a20";
        case 6:
          return "#4a0";
        default:
          return "#f90";
      }
    },
    cellClick(e) {
      //点右键 标记
      if (this.cellData.isClear) {
        return;
      }

      if (e.button === 2) {
        EventBus.$emit("click-cell");

        this.$set(this.cellData, "isMarked", !this.cellData.isMarked);

        return;
      }
      if (e.button === 0) {
        if (this.cellData.isBoom) {
          //游戏结束
          EventBus.$emit("boom-end");
          console.log("boom");
        } else {
          if (this.cellData.isMarked) {
            return;
          }
          EventBus.$emit("click-cell");
          //让当前单元格显示数字
          //  this.$set(this.cellData,"isClear",true);
          //  console.log('clear');
          ////把要清理的坐标index发送给父容器
          this.$emit("clearboom", this.cellData.cellIndex);
        }
      }
    }
  },
  props: {
    cellData: {
      type: Object,
      required: true
    },
    size: {
      type: Object,
      required: false,
      default: function() {
        return {
          w: 30,
          h: 30
        };
      }
    }
  }
};
</script>

<style lang="scss" scoped>
td {
  text-align: center;
  vertical-align: middle;
}
.mine-clear {
  background-color: #333 !important;
}
.marked {
  background-image: url(/flag.png) !important;
  background-size: cover;
}
.boom {
  position: relative;
  &::before {
    content: "";
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-image: url(/bz.png);
    background-size: cover;
  }
}
</style>
