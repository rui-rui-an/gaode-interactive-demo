<template>
  <div id="container">
    <slot></slot>
  </div>
</template>

<script>
import Vue from 'vue'
export default {
  data () {
    return {
      map: '',
      // arr: [{ lat: 116.405285, long: 39.905989 }, { lat: 116.405285, long: 39.909689 }, { lat: 116.405285, long: 39.915989 }, { lat: 116.405285, long: 39.908989 }]
    }
  },
  provide: function () {
    return {
      addLayer: this.addLayer,
      removeOlver: this.removeOlver,
      addOverlay: this.addOverlay,
      getZoom: this.getZoom,
      drawPolyLine: this.drawPolyLine,
      closePolyLine: this.closePolyLine,
      clearOverGroup: this.clearOverGroup,
      changeToLittle: this.changeToLittle,
      removeAllMarkers: this.removeAllMarkers,
      addMoveMarker: this.addMoveMarker,
      mapRightMenu: this.mapRightMenu,
      mapClick: this.mapClick,
      mapClickOff: this.mapClickOff,
      addOverGroup: this.addOverGroup,
      clearMarkerLayer: this.clearMarkerLayer,
      isRoom: this.isRoom,
      initClusterLoad: this.initClusterLoad,
      clusterAddMarkers: this.clusterAddMarkers,
      clusterDelMarkers: this.clusterDelMarkers,
      clearOverlay: this.clearOverlay
    }
  },
  components: {
  },
  mounted () {
    Vue.prototype.$map = new AMap.Map('container', {
      zoom: 15,//级别
      center: [116.397428, 39.90923],//中心点坐标
      resizeEnable: true,
      averageCenter: true
      // mapStyle: 'amap://styles/1938caab0264493fa2a847243b88e511',
    });
    this.map = Vue.prototype.$map
    this.mapSetCenter() // 监听设置中心事件
    //  = this.map
    // this.createpolyline()
    // this.createCircle()
    // this.createMarker()
    // this.createDefinite()
    // this.createMassMarks()
    // this.createMarkerClusterer()

  },
  methods: {
     mapSetCenter(fn = new Function()) {
      this.bus.$on('handleCenter', (position, zoom = 15) => {
        this.$nextTick(() => {
          this.map.setCenter(position)
          this.map.setZoom(zoom)
          fn()
        })
      })
    },
    removeOlver (overlay) {
      this.$nextTick(() => {
        if (overlay) {
          this.map.remove(overlay)
        }
      })
    },
    addOverlay (overlay) {
      this.$nextTick(() => {
        // console.log(this.map);
        this.map && this.map.add(overlay)
      })
    },
    addLayer () { },
    // 绘制曲线
    drawPolyLine () {
      //构造折线编辑对象，并开启折线的编辑状态
      if (this.mouseTool) {
        this.mouseTool.rectangle()
      } else {
        AMap.plugin(['AMap.MouseTool'], () => {
          this.mouseTool = new AMap.MouseTool(this.map)

          //用鼠标工具画多边形
          let drawPolygon = this.mouseTool.rectangle()
          this.mouseTool.on('draw', e => {
            const res = this.map.getAllOverlays().slice(-1)
            this.bus.$emit('drawRectangle', {
              obj: this.mouseTool,
              data: e.obj.getPath()
            })
            setTimeout(() => {
              this.map.remove(res)
            }, 300)
          })
        })
      }
    },
    // 取消多边形
    closePolyLine () {
      this.mouseTool && this.mouseTool.close(true)
    },
    // 将点添加到群组
    addOverGroup (marker) {
      if (!this.overGroup) {
        // 如果没有初始化，则初始化
        this.overGroup = new AMap.OverlayGroup()
        this.overGroup.setMap(this.map)
      }
      this.overGroup.addOverlay(marker)
    },
    // 清除覆盖物群组
    clearOverGroup () {
      if (this.overGroup) {
        this.overGroup.clearOverlays()
      }
    },
    changeToLittle () {
      this.map?.setCenter(this.centrePos)
      this.map?.setZoom(13)
    },
    removeAllMarkers () {
      this.$nextTick(() => {
        this.map && this.map.clearMap()
      })
    },
    addMoveMarker () { },
    //监听鼠标右键事件
    mapRightMenu (fn) {
      this.map?.on('rightclick', e => {
        fn(e)
      })
    },
    //监听鼠标右键事件
    mapRightMenu (fn) {
      this.map?.on('rightclick', e => {
        fn(e)
      })
    },
    // 鼠标点击事件
    mapClick (fn) {
      this.map && this.map?.on('click', e => fn(e))
    },

    mapClickOff () {
      this.map && this.map?.off('click', e => fn(e))
    },
    clearMarkerLayer () { },
    // 将点添加到群组
    addOverGroup (marker) {
      if (!this.overGroup) {
        // 如果没有初始化，则初始化
        this.overGroup = new AMap.OverlayGroup()
        this.overGroup.setMap(this.map)
      }
      this.overGroup.addOverlay(marker)
    },
    /**
    * 聚合标注初始化
    * @param <Array> 实例化的 AMap 对象，但还没被 添加到 地图中
    */
    initClusterLoad (list, type) {
      this.clusterTimer && clearTimeout(this.clusterTimer)
      this.clusterTimer = setTimeout(() => {
        AMap.plugin(['AMap.MarkerClusterer'], () => {
          // 设置聚合样式
          this.initClusterRender(list, type)
          // 初始化生成聚合数据点
          this.setCluster(this.map, list)
        })
      }, 1000)
    },
    // 聚合操作 添加
    clusterAddMarkers (list) {
      this.curCluster.addMarkers(list)
    },
    // 聚合操作 删除
    clusterDelMarkers (list) {
      this.curCluster.removeMarkers(list)
    },
    clearOverlay () {
      this.map && this.map?.clearMap()
    },
    // 地图销毁事件
    descroyMap () {
      this.map && this.map?.destroy()
    }
  }
}
</script>

<style lang="less" scoped>
#container {
  width: 100%;
  height: 800px;
}

::v-deep .marker-route {
  width: 60px;
  height: 30px;
  text-align: center;
  line-height: 30px;
  background-color: red;
}
</style>