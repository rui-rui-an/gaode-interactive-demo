<template>
  <div class="InfoWindow" v-if="show">
    <div class="InfoWindow-box">
      <div class="header"><span>{{info.placeName}}</span><span @click="closeEvent" class="close">X</span></div>
      <div class="content">
        <ul>
          <li v-for="(item,index) in info.location" :key="index">{{item}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props:{
    show:{
      type:Boolean,
      default:false
    },
    info:{
      type:Object,
    }
  },
  methods:{
    closeEvent(){
      // console.log(111);
      // this.$emit('update:show',false)
      this.$emit('windowClose')
    }
  }
}
</script>

<style lang="less" scoped>
.InfoWindow {
  position: absolute;
  right: 0;
  z-index: 888;
  width: 200px;
  height: calc(100vh - 140px);
  background-color: rgb(214, 198, 198);
  .InfoWindow-box{
    padding: 15px;
  }
  .header{
    // text-align: right;
    display: flex;
    justify-content: space-between;
    align-items: center;
    .close{
      cursor: pointer;
    }
  }
}
</style>