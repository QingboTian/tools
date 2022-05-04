<template>
  <div id="container"></div>
</template>
<script>
import AMapLoader from "@amap/amap-jsapi-loader";
import { shallowRef } from "@vue/reactivity";
import positionJson from "./position.json";
export default {
  setup() {
    const map = shallowRef(null);
    return {
      map,
    };
  },
  data() {
    return {
      //   map: null,
    };
  },

  methods: {
    initMap() {
      AMapLoader.load({
        key: "3e8fa81821d240b6a57b3223a276c850", // 申请好的Web端开发者Key，首次调用 load 时必填
        version: "2.0", // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
        plugins: [""], // 需要使用的的插件列表，如比例尺'AMap.Scale'等
      })
        .then((AMap) => {
          this.map = new AMap.Map("container", {
            //设置地图容器id
            // viewMode: "3D", //是否为3D地图模式
            zoom: 12, //初始化地图级别
            center: [116.397451, 39.909187], //初始化地图中心点位置
          });

          var marker = new AMap.Marker({
            position: new AMap.LngLat(116.397451, 39.909187),
            title: "天安门",
          });
          this.map.add(marker);
          this.initMarker(AMap);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    initMarker(AMap) {
      positionJson.data.forEach((item) => {
        var p = item.position.split(",");
        var marker = new AMap.Marker({
          position: new AMap.LngLat(p[0], p[1]),
          title: item.desc,
        });
        this.map.add(marker);
      });
    },
  },

  mounted() {
    //DOM初始化完成进行地图初始化
    this.initMap();
  },
};
</script>

<style scoped>
#container {
  height: 100%;
}
</style>