
```
<template>
  <div id="imagedata">
  <!-- 使用shp读取shp2geojson.js-master.zip放在E盘中-->

    <!-- 地图 -->
    <div class="id-map">
      <div id="id-lmap" style="width: 100%;height: 100%;background-color:#fff"></div>
      <div id="id-lmap-position">层级:{{olZoom.toFixed(0)}}</div>
      <div id="id-lmap-detail">
        <p>标记内容：{{tag}}</p>
        <p>备注:{{remark}}</p>
      </div>
      <div id="id-lmap-upload">
        <Upload multiple type="drag" action="//jsonplaceholder.typicode.com/posts/">
          <div style="padding: 20px 0">
            <Icon type="ios-cloud-upload" size="52" style="color: #3399ff"></Icon>
            <p>Click or drag files here to upload</p>
          </div>
        </Upload>
        <Button @click="getgeojson">getgeojson</Button>
      </div>
    </div>

  </div>

</template>
<script>
import { mapGetters, mapMutations } from "vuex";
import axios from "axios";

// import ysjCheck from "../data/ysjCheck.vue";
export default {
  name: "imageSeData",
  components: {
    // ysjCheck
  },
  data() {
    return {
      tag: "",
      remark: "",
      olZoom: 4,
      GeoJSON: "",
      map: null,
      tileGrid: ""
    };
  },
  mounted() {
    // this.getDataList(); //请求数据列表
    // this.getgeojson();
    this.initOlMap();
  },
  computed: {
    ...mapGetters({
      GET_USER_TOKEN: "GET_USER_TOKEN"
    })
  },
  watch: {},
  methods: {
    // getgeojson() {
    //   // axios.get("../../../../static/lib/test20180711.json").then(res => {
    //   //   var result = res.data;
    //   //   this.GeoJSON = result;
    //   //   this.initOlMap();
    //   // });
    //   //---------------获取shp然后读取成geojson
    //   var shapefile = require("shapefile");
    //   var features = [];
    //   var that = this;
    //   shapefile
    //     .open("../../../../static/lib/test20180711.shp")
    //     .then(source =>
    //       source.read().then(function log(result) {
    //         debugger;
    //         if (result.done) return;
    //         // console.log(result.value);
    //         // return source.read().then(log);
    //         // console.log(source.read().then(log));
    //         features.push(result.value);
    //         that.GeoJSON = {
    //           type: "FeatureCollection",
    //           features: features
    //         };
    //         console.log(that.GeoJSON);
    //         debugger;
    //         that.addLayer();
    //       })
    //     )
    //     .catch(error => console.error(error.stack));
    //   //-----------------
    // },
    getgeojson() {
      debugger;
      // axios.get("../../../../static/lib/test20180711.json").then(res => {
      //   var result = res.data;
      //   this.GeoJSON = result;
      //   this.initOlMap();
      // });
      //---------------获取shp然后读取成geojson
      // var toJSON = require("shp2json");
      // toJSON(process.stdin).pipe(process.stdout);
      // process.stdin.resume();

      // // or

      // toJSON.fromShpFile("./data/from_files/shape.shp").pipe(process.stdout);
      //-----------------
      // -----------
      var that = this;
      loadshp(
        {
          url: "../../../../static/lib/110108001000shp.zip", // path or your upload file
          // encoding: "big5", // default utf-8
          encoding: "GB2312",
          EPSG: 3857 // default 4326
        },
        function(geojson) {
          console.log(geojson);
          debugger;
          that.GeoJSON = geojson;
          // geojson returned
        }
      );
      if (that.GeoJSON != "") {
        that.addLayer();
      }

      // -----------
    },
    addLayer() {
      debugger;
      var Vectorobj = this.GeoJSON;
      var vectorSource = new ol.source.Vector({
        features: new ol.format.GeoJSON().readFeatures(Vectorobj)
        //   url: "../../../../static/lib/test20180711.json",//geojson地址
        // "https://openlayers.org/en/v4.1.1/examples/data/geojson/countries.geojson",
        //   format: new ol.format.GeoJSON()
      });
      // var vectorlayer = new ol.layer.Vector({
      //   //这是一个能选择的地图源
      //   source: vectorSource
      // });
      var vectorLayer = new ol.layer.Vector({
        source: vectorSource
      });
      map.addLayer(vectorLayer);
      // console.log(Vectorobj);
      // map.getView().fit(ol.proj.transformExtent(Vectorobj.bbox), map.getSize());
      map
        .getView()
        .fit(
          ol.proj.transformExtent(Vectorobj.bbox, "EPSG:3857", "EPSG:3857"),
          map.getSize()
        );
    },
    initOlMap() {
      debugger;
      console.log(this.GeoJSON);

      var that = this;
      var mousePositionControl = new ol.control.MousePosition({
        coordinateFormat: ol.coordinate.createStringXY(4),
        // projection: this.curRow.prj == "c" ? "EPSG:4326" : "EPSG:3857",
        className: "custom-mouse-position",
        target: document.getElementById("id-lmap-position"),
        undefinedHTML: "&nbsp;"
      });
      // var Vectorobj = this.GeoJSON;
      // var vectorlayer = new ol.layer.Vector({
      //   //这是一个能选择的地图源
      //   source: new ol.source.Vector({
      //     features: new ol.format.GeoJSON().readFeatures(Vectorobj)
      //     // url: "../../../../static/lib/test20180711.json"
      //     // "https://openlayers.org/en/v4.1.1/examples/data/geojson/countries.geojson",
      //     //   format: new ol.format.GeoJSON()
      //   })
      // });
      //--------加载天地图影像
      this.wmts_img = new ol.layer.Tile({
        source: new ol.source.WMTS({
          url: CONFIG.TILE_HOSTS + "/img_c/wmts",
          layer: "img",
          format: "tiles",
          matrixSet: "c",
          style: "default",
          tileGrid: that.tileGrid
        })
      });
      //-------------
      map = new ol.Map({
        // layers: [vectorlayer],
        // layers: [this.wmts_img],
        target: document.getElementById("id-lmap"),
        controls: ol.control
          .defaults({
            attribution: false,
            zoomOptions: {
              zoomInTipLabel: "放大",
              zoomOutTipLabel: "缩小"
            }
          })
          .extend([mousePositionControl]),
        //----------天地图影像的view
        // view: new ol.View({
        //   projection: "EPSG:4326",
        //   center: ol.proj.transform([127.43, 45.01], "EPSG:4326", "EPSG:4326"), //初始默认中心点
        //   zoom: 0 //初始默认缩放级数
        // })
        //------------
        view: new ol.View({
          center: [0, 0],
          zoom: 4 //初始默认缩放级数
        })
      });
      // map.addLayer(this.wmts_img);//加载天地图

      // this.wmts_img.setZIndex(-1);
      // this.map = map; //加载天地图
      map.getView().on("change:resolution", function(evt) {
        that.olZoom = map.getView().getZoom();
      });

      // // console.log(Vectorobj);
      // map.getView().fit(
      //   // ol.proj.transformExtent([
      //   //   Vectorobj.features[0].geometry.coordinates[0][0][0],
      //   //   Vectorobj.features[0].geometry.coordinates[0][0][1],
      //   //   Vectorobj.features[0].geometry.coordinates[0][1][0],
      //   //   Vectorobj.features[0].geometry.coordinates[0][1][1]
      //   // ]),
      //   ol.proj.transformExtent(Vectorobj.bbox),
      //   map.getSize()
      // );
      var select = null; // ref to currently selected interaction

      // select interaction working on "singleclick"
      var selectSingleClick = new ol.interaction.Select();

      // select interaction working on "click"
      //   var selectClick = new ol.interaction.Select({
      //     condition: click
      //   });

      //   var selectElement = document.getElementById('type');

      // var value = selectElement.value;
      // if (value == "click") {
      select = selectSingleClick;
      // } else {
      //   select = null;
      // }
      if (select !== null) {
        map.addInteraction(select);
        select.on("select", function(e) {
          debugger;
          that.tag = e.selected[0].N.AREA_CODE;
          that.remark = e.selected[0].N.NAME;
        });
      }

      /**
       * onchange callback on the select element.
       */
      //   selectElement.onchange = changeInteraction;
      //   changeInteraction();
    }
  }
};
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#id-lmap-position {
  position: absolute;
  padding: 3px;
  font-size: 12px;
  bottom: 5px;
  right: 5px;
  border-radius: 4px;

  z-index: 500;
  background: rgba(255, 255, 255, 0.6);
}
#id-lmap-detail {
  /* width: 200px; */
  height: 100px;
  top: 100px;
  position: absolute;
  border-radius: 6px;
  background-color: #dee3e9;
}
#id-lmap-upload {
  height: 100px;
  top: 400px;
  position: absolute;
  border-radius: 6px;
  background-color: #dee3e9;
}
</style>
```
