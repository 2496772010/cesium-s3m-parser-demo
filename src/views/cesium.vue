<template>
  <div id="cesiumContainer"></div>
</template>
<script>
import S3MTilesLayer from "s3m_parser_es6/S3MTiles/S3MTilesLayer";
export default {
  name: "cesium",
  methods: {
    initCesium() {
      Cesium.Ion.defaultAccessToken="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI2NGZjOWQ5Zi1hNGRhLTQxODktOGQzNC05MGE4MGY4NWFiNzQiLCJpZCI6MTYzMTgsInNjb3BlcyI6WyJhc3IiLCJnYyJdLCJpYXQiOjE1NzAxNjg2MzV9.VV0jzIIN5Sc-H1mq9RIsbHDd3y-ZhORd63g62sxpX80"
      let viewer = new Cesium.Viewer("cesiumContainer", {
        animation: false,
        timeline: false,
        fullscreenButton: false,
        geocoder: false,
        baseLayerPicker: false,
      });
      let scene=viewer.scene
      window.cesiumViewer = viewer; //不要把cesium实例挂载到vue的data对象中(有性能问题)
      let layer = new S3MTilesLayer({
                    context : scene._context,
                    url: 'http://localhost:8809/data/%E4%B8%89%E7%BB%B4%E6%A8%A1%E5%9E%8B/Production_2/s3m1/ConfigS3M.scp'
                });

                scene.primitives.add(layer);

                layer.readyPromise.then(function() {
                    scene.camera.setView({
                        destination : Cesium.Cartesian3.fromDegrees(115.3399347463200000,24.2324583415620400, 15000.0),
                        orientation : {
                            heading : 5.213460518239332
                            //pitch : -0.5150671720144846
                        }
                    });
                }).otherwise(function(error) {
                    console.log(error);
                });
    },
    addDom() {
      window.cesiumViewer.imageryLayers.addImageryProvider(
        new Cesium.WebMapTileServiceImageryProvider({
          url: `http://t0.tianditu.gov.cn/img_w/wmts?tk=a89df02c93e5474e9ebeb81a32fcb487`, //记得换成自己的key
          layer: "img",
          style: "default",
          tileMatrixSetID: "w",
          format: "tiles",
          maximumLevel: 18
        })
      );
      window.cesiumViewer.imageryLayers.addImageryProvider(
        new Cesium.WebMapTileServiceImageryProvider({
          url: `http://t0.tianditu.gov.cn/cia_w/wmts?tk=a89df02c93e5474e9ebeb81a32fcb487`, //记得换成自己的key
          layer: "cia",
          style: "default",
          tileMatrixSetID: "w",
          format: "tiles",
          maximumLevel: 18
        })
      );
    },
    addNav() {
      window.cesiumViewer.cesiumWidget.creditContainer.style.display = "none";
    //   window.cesiumViewer.terrainProvider = cesiumTerrainProvider;
      window.cesiumViewer.extend(Cesium.viewerCesiumNavigationMixin, {});
    }
  },
  mounted() {
    this.initCesium();
    this.addNav()
    this.addDom();
  }
};
</script>
<style scoped>
#cesiumContainer {
  height: 100%;
}
</style>
