<template>
    <div class="header">
        <div id="mapSearch" class="map">
        <!--<vl-map class="map" :load-tiles-while-animating="true" :load-tiles-while-interacting="true"
             data-projection="EPSG:4326" style="height: 400px">
            <vl-view :zoom.sync="map.zoom" :center="topicPointCoordinates" :rotation.sync="map.rotation"></vl-view>
            <vl-feature v-if="wikidocumentaries.geo.location != ''" id="topicPosition">
                <vl-geom-point :coordinates="topicPointCoordinates"></vl-geom-point>
                <vl-style-box>
                    <vl-style-icon class="topic-icon" src="/static/wikifont/svgs/mod/uniE851 - mapPin - red.svg" :scale="1.4" :anchor="[0.5, 1]"></vl-style-icon>
                </vl-style-box>
            </vl-feature>
            <vl-layer-tile id="osm">
                <vl-source-osm></vl-source-osm>
            </vl-layer-tile>
        </vl-map>-->
        </div>
        <div class="header-contents">
            <div class="title">
                <h1><span>{{ wikidocumentaries.title }}</span></h1>
            </div>
        </div>
    </div>
</template>

<script>
export default {
  name: 'MapPageHeader',
  props: {
  },
  computed: {
      wikidocumentaries () {
          return this.$store.state.wikidocumentaries;
      },
  },
  data () {
    return {
        }
    },
    mounted: function () {
       this.createMap();
    },
    methods: {
        createMap() {
            var ol = this.$ol;

            var view = null;

            if (this.topicPointCoordinates() != null) {
                view = new ol.View({
                    center: ol.proj.fromLonLat(this.topicPointCoordinates()),
                    zoom: 14
                })
            }
            else {
                view = new ol.View({
                    center: ol.proj.fromLonLat([0, 0]),
                    zoom: 1
                })
            }

            var map = new ol.Map({
                target: 'mapSearch',
                layers: [
                    new ol.layer.Tile({
                        source: new this.$ol.source.OSM()
                    })
                ],
                view: view
            });

            this.$store.commit('setHistoricalMapSearchPageMap', map);

            if (this.wikidocumentaries.geo.location != null) {
                this.topicFeature = new ol.Feature({
                    geometry: new ol.geom.Point(ol.proj.fromLonLat(this.topicPointCoordinates())),
                    
                });
                var iconStyle = new ol.style.Style({
                    image: new ol.style.Icon({
                        anchor: [0.5, 1],
                        anchorXUnits: 'fraction',
                        anchorYUnits: 'fraction',
                        src: "/static/wikifont/svgs/mod/uniE851 - mapPin - red.svg",
                        scale: 1.4
                    })
                });
                this.topicFeature.setStyle(iconStyle);

                var vectorSource = new ol.source.Vector({
                    features: [this.topicFeature]
                });
                var vectorLayer = new ol.layer.Vector({
                    source: vectorSource
                });

                map.addLayer(vectorLayer);
            }
        },
        topicPointCoordinates () {
            var coords = null;
            if (this.wikidocumentaries.geo.location != null) {
                //console.log(this.wikidocumentaries.geo.location)
                var coordPart = this.wikidocumentaries.geo.location.split('(')[1].split(')')[0];
                //console.log(coordPart);
                coords = coordPart.split(' ').map(Number);
                //console.log(coords);
            }

            return coords;
        }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.header {
    position:relative;
}
.map {
    width: 100%;
    height: 400px;
    -webkit-filter: grayscale(90%);
    filter: grayscale(90%);
}
.title {
    position:absolute;
    bottom: 0px;
    left: 10px;
    width: calc(100% - 10px); 
}
h1 {
    font-weight: bold;
    font-size: 32pt;
    text-transform: uppercase;
    color: #ffffff;
}
h1 span { 
   /*letter-spacing: -1px;  */
   background: rgb(0, 0, 0); /* fallback color */
   background: rgba(0, 0, 0, 0.2);
   padding: 10px; 
}

</style>
