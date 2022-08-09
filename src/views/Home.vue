<template>
  <div class="home">
    <Amap>
      <RouteMarker
        :list="list"
        :lineList="lineList"
        @elementClick="elementClick"
      ></RouteMarker>
      <InfoWindow :show.sync="showInfoWindow" :info="infoWindowContent" @windowClose="windowClose"></InfoWindow>
      <LeftTrack ref="LeftTrack" :list="lineList" @itemClick="itemClick"></LeftTrack>
    </Amap>
  </div>
</template>

<script>
import Amap from '@/components/aMap/map.vue'
import RouteMarker from '@/components/router-marker'
import InfoWindow from './component/InfoWindow.vue'
import LeftTrack from './component/LeftTrack.vue'
export default {
  components: {
    Amap,
    RouteMarker,
    InfoWindow,
    LeftTrack
  },
  data () {
    return {
      list: [],
      subList:[],
      lineList: [],
      showInfoWindow: false,
      infoWindowContent: {}
    }
  },
  created () {
    this.getListData()
  },
  computed: {
    map () {
      return this.$map
    },
  },
  methods: {
    itemClick (item, index) {
      // console.log(item);
      let temptracksListArr = JSON.parse(JSON.stringify(this.subList))
      let tempIndex = this.getIndex(this.list, item)
      // console.log(temptracksListArr[tempIndex].status);
      temptracksListArr[tempIndex].status = 'active'
      this.list = temptracksListArr
      this.elementClick(item, true)
    },
    windowClose(){
      this.list = this.subList
       this.$refs.LeftTrack.currentIndex = -1
      this.showInfoWindow = false
    },
    getListData () {
      setTimeout(() => {
        this.lineList = [{
          location: [116.405285, 39.905989],
          title: '13',
          status: '',
          placeName:'北京花园',
          placeId: '123'
        }, {
          location: [116.415585, 39.909689],
          title: '68',
          status: '',
          placeName:'望京SOHO',
          placeId: '68'
        }, {
          location: [116.409385, 39.908989],
          title: '79',
          status: '',
          placeName:'腾讯大厦',
          placeId: '789'
        }, {
          location: [116.404385, 39.904989],
          title: '222',
          status: '',
          placeName:'万达大厦',
          placeId: '222'
        }, {
          location: [116.403385, 39.906989],
          title: '55',
          status: '',
          placeName:'广发银行',
          placeId: '55'
        }, {
          location: [116.406385, 39.906989],
          title: '6',
          status: '',
          placeName:'浦发银行',
          placeId: '7'
        }, {
          location: [116.408885, 39.903989],
          title: '11',
          status: '',
          placeName:'五角大楼',
          placeId: '111'
        }, {
          location: [116.405585, 39.90189],
          title: '88',
          status: '',
          placeName:'去哪儿',
          placeId: '88'
        }, {
          location: [116.409385, 39.908989],
          title: '79',
          status: '',
          placeName:'腾讯大厦',
          placeId: '789'
        }]
        this.list = this.unique(this.lineList)
        this.subList = JSON.parse(JSON.stringify(this.list))
        if (this.map) {
          // 根据经纬度点 定位中心点
          this.bus.$emit('handleCenter', this.list[0].location, 15)
          // 下面的是openlayers设置中心点
          // this.bus.$emit('setViewCenter', {
          //   position: this.list[0].location,
          //   zoom: 9,
          //   isMark: false
          // })
        }
      }, 500)
    },
     unique (arr) {
      // arr = arr.reverse()
      let tempArr = JSON.parse(JSON.stringify(arr))
      let newArr = tempArr.reverse()
      const res = new Map();
      return newArr.filter((arr) => !res.has(arr.placeId) && res.set(arr.placeId, 1))
    },
    elementClick (info, bol) {
      // 查找关于该停车点的该车辆的进出信息
      // 点击时处理右边的显示和隐藏
      let temptracksListArr = JSON.parse(JSON.stringify(this.subList))
      let tempIndex = this.getIndex(this.list, info)
      // console.log(temptracksListArr[tempIndex]);
      temptracksListArr[tempIndex].status = this.activeColor(temptracksListArr[tempIndex])
      this.list = temptracksListArr
      // 打开右边窗口
      this.showInfoWindow = true
      this.infoWindowContent = info
      // 找到左边的index ，控制左边的背景色，下面的代码，如果是代码控制的点击就不执行，如果是用户点击的则执行
      if (!bol) {
        let tempIndex2 = this.getIndex(this.lineList, info)
        this.$refs.LeftTrack.currentIndex = tempIndex2
        // 找到右边的东西，然后
        this.$nextTick(() => {
          let targetDom = document.querySelector('.left-track .bgc')
          let scrollDom = document.querySelector('.left-track .content')
          scrollDom.scrollTop = targetDom.offsetTop
        })
      }
    },
    activeColor (item) {
      if (this.lineList.length > 1 && this.lineList[0].placeId === item.placeId && item.placeId === this.lineList[this.lineList.length - 1].placeId) {
        return 'activeStartAndEnd'
      }
      if (this.list.length && this.list[0].placeId === item.placeId) {
        return 'activeStart'
      }
      if (item.placeId === this.lineList[0].placeId) {
        return 'activeEnd'
      }
      return 'activeBy'
    },
    getIndex (Arr, obj) {
      return Arr.findIndex((item) => {
        return item.placeId === obj.placeId
      })
    },
  }
}
</script>

<style lang="less" scoped>
.home {
}
</style>