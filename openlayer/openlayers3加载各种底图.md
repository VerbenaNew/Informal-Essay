不管是加载在线还是离线的底图，无非是按照一定的规则，正确定义图层（layer）,然后将图层添加到map对象中，这里我map对象使用的坐标系为WGS84 4326，openlayers版本为3.17。

1.在线天地图交通图


var tian_di_tu_road_layer = new ol.layer.Tile({  

    title: "天地图路网",  
    source: new ol.source.XYZ({  
        url: "http://t4.tianditu.com/DataServer?T=vec_w&x={x}&y={y}&l={z}"  
    })  
});  
  
var tian_di_tu_annotation = new ol.layer.Tile({  

    title: "天地图文字标注",  
    source: new ol.source.XYZ({  
        url: 'http://t3.tianditu.com/DataServer?T=cva_w&x={x}&y={y}&l={z}'  
    })  
    
});  

2.离线天地图
var tiandituBaseLayer = new ol.layer.Tile({  

    title:"天地图基础图层",  
    source: new ol.source.XYZ({  
        tileUrlFunction:function(tileCoord){  // 参数tileCoord为瓦片坐标  
            var z = tileCoord[0];  
            var x = tileCoord[1];  
            var y = -tileCoord[2]-1;  
            return 'http://localhost:8080/tiantai/TDTVec/L'+z+'/'+y+'/'+x+'.png'  
        },  
        projection:'EPSG:4326'  
    })  
    
});  
  
// 添加一个使用离线瓦片地图的层  
var tiandituMarkLayer = new ol.layer.Tile({  

    title:"天地图标注图层",  
    source: new ol.source.XYZ({  
        tileUrlFunction:function(tileCoord){  // 参数tileCoord为瓦片坐标，遍历整个坐标系统  
            var z = tileCoord[0];  
            var x = tileCoord[1];  
            var y = -tileCoord[2]-1;//不是经纬度,是瓦片坐标  
            return 'http://localhost:8080/tiantai/TDTVecAnno/L'+z+'/'+y+'/'+x+'.png';  
        },  
        projection:'EPSG:4326'  
    })  
    
});  
3.在线天地图影像


var tiandituSatelliteLayer = new ol.layer.Tile({  

       source: new ol.source.XYZ({  
           url:'http://t3.tianditu.cn/DataServer?T=img_w&X={x}&Y={y}&L={z}'//天地图影像  
       }),  
       projection: 'EPSG:3857' 
       
   })  
     
var tiandituSatelliteLabel = new ol.layer.Tile({  

       source: new ol.source.XYZ({  
           url:'http://t3.tianditu.cn/DataServer?T=cia_w&X={x}&Y={y}&L={z}'//天地图影像 标注  
       }),  
       projection: 'EPSG:3857'  
       
   })  
4.在线高德地图

var gaodeMapLayer = new ol.layer.Tile({  

        source: new ol.source.XYZ({  
            url:'http://webrd03.is.autonavi.com/appmaptile?x={x}&y={y}&z={z}&lang=zh_cn&size=1&scale=1&style=8'//高德地图在线  
        }),  
        projection: 'EPSG:3857'  
    })  
5.3857坐标系下的arcgis切片


var tileLayer = new ol.layer.Tile({  

    source: new ol.source.XYZ ({  
        tileUrlFunction: function(tileCoord) {  
            var x = 'C'+zeroPad(tileCoord[1],8,16);  
            var y = 'R'+zeroPad(-tileCoord[2]-1,8,16);  
            var z = 'L'+zeroPad(tileCoord[0],2,10);  
            return 'tiles/wt/'+z+'/'+y+'/'+x+'.png';  
        },  
        projection: 'EPSG:3857'  
    })  
});   

     function zeroPad(num,len,radix){  
    var str = num.toString(radix || 10);  
    while(str.length<len){  
        str = "0"+ str;  
    }  
    return str;  
    
}  
6.Open Street Map 地图层

默认：

var openStreetMapLayer = new ol.layer.Tile({  

     source: new ol.source.OSM()  
 });  
地形：

var osmTerrainlayer = new ol.layer.Tile({  

        source : new ol.source.OSM(  
                url:'http://tile-{a-c}.openstreetmap.fr/hot/{z}/{x}/{y}.png'//osm地形  
        )  
})  
自行车地图：


var osmBicycleLayer = new ol.layer.Tile({  

        source: new ol.source.OSM({   
            url:'http://{a-c}.tile.thunderforest.com/cycle/{z}/{x}/{y}.png'//osm自行车地图  
        })   
    })   

7.在线Bing地图


var bingMapLayer = new ol.layer.Tile({  

        source: new ol.source.BingMaps({  
            key: 'AkjzA7OhS4MIBjutL21bkAop7dc41HSE0CNTR5c6HJy8JKc7U9U9RveWJrylD3XJ',  
      imagerySet: 'Road'  
        })  
    });  
8.在线Stamen地图

var stamenLayer = new ol.layer.Tile({  

        source: new ol.source.Stamen({  
            layer: 'watercolor'  
        })  
    });  

9.在线MapQuest地图

var mapQuestLayer = new ol.layer.Tile({  

        source: new ol.source.MapQuest({  
            layer: 'osm'  
        })  
    });  

10.加载geoserver的WMS服务

var TileWMSLayer = new ol.layer.Tile({  

        source: new ol.source.TileWMS({  
            url: "http://192.168.88.21:8081/geowebcach/service/wms",  
            params: {'LAYERS': 'tiantai','FORMAT':'image/png','SRS':'EPSG:0'},  
            tileGrid: new ol.tilegrid.TileGrid({  
                //resolution和conf.xml中保持一致  
                resolutions: [0.00068664344191344954,0.00034332053122622185,0.00017165907588260801,8.5829537941304004e-005,4.2913579240149092e-005,2.1455599889571629e-005,1.07266102142829e-005,5.3633051071414502e-006,2.6816525535707251e-006],  
                tileSize: [256, 256],  
                origin:[-400,400]  
            })  
        }),  
        projection: 'EPSG:4326'  
    })  

11.ArcGIS Server发布的服务

var arcgisTileLayer = new ol.layer.Tile({  

        source: new ol.source.TileArcGISRest({  
            url: 'http://localhost:8399/arcgis/rest/services/test/MapServer'  
        })  
    })  
好像3.17版本才支持，如果服务启用缓存，访问的就是arcgis缓存切片

12.在线谷歌地图

谷歌交通地图：

var googleMapLayer = new ol.layer.Tile({  

        source: new ol.source.XYZ({  
            url:'http://www.google.cn/maps/vt/pb=!1m4!1m3!1i{z}!2i{x}!3i{y}!2m3!1e0!2sm!3i345013117!3m8!2szh-CN!3scn!5e1105!12m4!1e68!2m2!1sset!2sRoadmap!4e0'  
        })  
    });  
谷歌地形地图：


var googleTerrainLayer = new ol.layer.Tile({  

       source: new ol.source.XYZ({  
           url:'http://mt3.google.cn/vt/lyrs=t@131,r@216000000&hl=zh-CN&gl=CN&src=app&x={x}&y={y}&z={z}&s=Gal'//谷歌地形地图  
       }),  
       projection: 'EPSG:3857'  
   })  

谷歌卫星地图（混合）：

var googleSatelliteLayer = new ol.layer.Tile({  

        source: new ol.source.XYZ({  
            url:'http://mt2.google.cn/vt/lyrs=y&hl=zh-CN&gl=CN&src=app&x={x}&y={y}&z={z}&s=G'//谷歌卫星地图 混合  
        }),  
        projection: 'EPSG:3857'  
    })  