<template>
  <div class="left-track">
    <div class="left-track-box">
      <div class="header">头部内容就不写了，重点是下面的</div>
      <div class="content">
        <el-timeline>
          <el-timeline-item
            v-for="(item, index) in list"
            :key="index"
            @click.native="itemClick(item, index)"
            :class="{ bgc: isClick && currentIndex === index }"
            :timestamp="item.placeName"
            placement="top"
          >
            <!-- <ul>
              <li
                v-for="(timestamp, timestampIndex) in item.timestamp"
                :key="timestampIndex"
              >
                {{ timestamp }}
              </li>
            </ul> -->
            <div class="text">{{item.location}}</div>
          </el-timeline-item>
        </el-timeline>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    list: {
      type: Array,
    },
    isClick: {
      type: Boolean,
      default: true
    }
  },
  data () {
    return {
      currentIndex: -1
    }
  },
  methods: {
    closeEvent () {
      // console.log(111);
      // this.$emit('update:show',false)
      this.$emit('windowClose')
    },
    itemClick (activity, index) {
      if (this.isClick) {
        this.currentIndex = index
        this.$emit('itemClick', activity, index)
      }
    },
  }
}
</script>

<style lang="less" scoped>
.left-track {
  position: absolute;
  left: 0;
  z-index: 888;
  width: 250px;
  height: calc(100vh - 140px);
  background-color: rgb(214, 198, 198);
  .left-track-box {
    height: 100%;
    padding: 15px;
  }
  .header {
    height: 500px;
    background-color: #0094ff;
  }
  .content {
    position: relative;
    background-color: #2f3d55;
    height: calc(100% - 550px);
    overflow: scroll;
    // color: cornsilk;
    ul,
    li {
      padding: 0;
      margin: 0;
      list-style: none;
    }
  }
  .text{
    // color: cornsilk;
  }
  /deep/.el-timeline-item__node {
  background-color: #8799bf;
  left: 0px;
  top: 0px;
}
/deep/.el-timeline-item__timestamp {
  color: #c0d0e7;
}
/deep/.el-timeline-item__content {
  color: #8799bf;
}
 /deep/ .el-timeline-item__wrapper{
    cursor: pointer;
  }
.bgc{
  /deep/ .el-timeline-item__wrapper{
    cursor: pointer;
    background: rgba(107, 116, 132, .6);
  }
}
}
</style>