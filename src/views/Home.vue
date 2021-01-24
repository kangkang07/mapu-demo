<template>
  <div class="container" ref="map">
   
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import {MapView, Layer, Polygon} from '../../lib/map-u'
const h3 = require('h3-js')

let mapView: MapView
let layer: Layer

@Component({
  components: {
  },
})
export default class Home extends Vue {
  mounted() {
    // // 创建地图实例
    // var map = new AMap.Map(this.$refs.map, {
    //   center:[117.000923,36.675807],
    //   zoom:11
    // })

    // 创建 mapview 实例
    mapView = new MapView()
    // 初始化
    mapView.init((this.$refs.map as HTMLElement), {
      center:[117.000923,36.675807],
      zoom:11
    }).then(()=>{
       // 创建图层
      layer = new Layer()
      mapView.addChildren(layer)

      // 制造数据
      let polygons = this.makePolygons()

      polygons.forEach((poly, index)=>{

        // 创建六边形
        let polygon = new Polygon(poly, {
          fillColor: 'rgba(0,0,111, 0.4)',
          strokeWidth:1,
          strokeColor: 'rgba(0,0,111,0.7)'
        })
        // 绑定额外数据
        polygon.dataset.index = index
        // 绑定事件
        polygon.on('click',e=>{
          alert('点击六边形 index:' + index)
        })

        // 添加到图层上
        layer.addChildren(polygon)
    })
    })
   

  }

  // 制造六边形数据
  makePolygons(){
    // 将一块区域转换成 h3 index
    let indexes = h3.polyfill([
      [35,116],
      [35,118],
      [37,118],
      [37,116],
    
    ], 7)
    // console.log(indexes)
    let polygons = indexes.map(index=>{
      // h3 的经纬度和高德的是反着的，要 reverse
      return h3.h3ToGeoBoundary(index).map(p=>p.reverse())
    })
    // console.log(polygons)
    return polygons
  }
}
</script>
<style lang="scss" scoped>
.container{
  width: 100vw;
  height: 100vh;
}
</style>
