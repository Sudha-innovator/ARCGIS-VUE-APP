<template>
  <div></div>
</template>

<script>
import { loadModules } from 'esri-loader';

export default {
  name: 'web-map',
  mounted() {
    // lazy load the required ArcGIS API for JavaScript modules and CSS
    loadModules(['esri/Map', 'esri/views/MapView', 'esri/layers/FeatureLayer'], { css: true })
    .then(([ArcGISMap, MapView, FeatureLayer]) => {

        const clusterConfig = {
            type: "cluster",
            // clusterRadius: "50px", // Indicates an area comprising screen space 100px in length from the center of the cluster
            popupTemplate: {
                content: " This cluster represents {cluster_count} fire points.", //{cluster_count} is an aggregate field containing the number of featrues   
            }
        };

        var layer = new FeatureLayer({
            url: "https://services7.arcgis.com/WSiUmUhlFx4CtMBB/arcgis/rest/services/FIRMS_Active_Fire_Points_Australia_Fires/FeatureServer/0",
            outFields: ["*"],
            renderer: {
                type: "simple",
                symbol: {
                    type: "simple-marker",
                    color: "red",
                    size: 10,
                },
            },
            featureReduction: clusterConfig,

        });
      const map = new ArcGISMap({
        basemap: 'gray-vector',
        layers: [layer]
      });

      this.view = new MapView({
        container: this.$el,
        map: map,
        center: [144, -24],
        zoom: 3
      });
    });
  },
  beforeDestroy() {
    if (this.view) {
      // destroy the map view
      this.view.container = null;
    }
  }
};

</script>

<style scoped>
div {
  padding: 0;
  margin: 0;
  width: 80%;
  height: 80%;
}
</style>