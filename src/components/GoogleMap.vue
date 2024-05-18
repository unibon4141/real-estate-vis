<script setup>
import { onMounted } from 'vue'
import { Loader } from "@googlemaps/js-api-loader"
import { MarkerClusterer } from "@googlemaps/markerclusterer";

// 動作確認用のマーカーの位置情報
const locations = [
  { lat: 35.64400016558132, lng: 147.154312 },
  { lat: 35.64370917424021, lng: 139.67053974165745 },
  { lat: -33.727111, lng: 150.371124 },
  { lat: -33.848588, lng: 151.209834 },
  { lat: -33.851702, lng: 151.216968 },
  { lat: -34.671264, lng: 150.863657 },
  { lat: -35.304724, lng: 148.662905 },
  { lat: -36.817685, lng: 175.699196 },
  { lat: -36.828611, lng: 175.790222 },
  { lat: -37.75, lng: 145.116667 },
  { lat: -37.759859, lng: 145.128708 },
  { lat: -37.765015, lng: 145.133858 },
  { lat: -37.770104, lng: 145.143299 },
  { lat: -37.7737, lng: 145.145187 },
  { lat: -37.774785, lng: 145.137978 },
  { lat: -37.819616, lng: 144.968119 },
  { lat: -38.330766, lng: 144.695692 },
  { lat: -39.927193, lng: 175.053218 },
  { lat: -41.330162, lng: 174.865694 },
  { lat: -42.734358, lng: 147.439506 },
  { lat: -42.734358, lng: 147.501315 },
  { lat: -42.735258, lng: 147.438 },
  { lat: -43.999792, lng: 170.463352 },
]
const loader = new Loader({
  apiKey: import.meta.env.VITE_API_KEY,
  version: "weekly",
  libraries: ["places"]
})
let gmap
onMounted(  ()=>{
  const initMap =  () => {
    loader.load().then( async () => {
      const { Map } = await google.maps.importLibrary("maps");
      const { AdvancedMarkerElement, PinElement } = await google.maps.importLibrary("marker")
      const centerPosition = { lat: 35.643560897913034, lng:139.67117539078941 }
      const labels = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
      gmap = document.getElementById('gmap')
      gmap = new Map(gmap, {
        center: centerPosition,
        zoom: 15,
        mapId: "TEST_MAP_ID",
      })

      const markers = locations.map((position, i) => {
        const label = labels[i % labels.length]
        const pinGlyph = new PinElement({
          glyph: label,
          glyphColor: "white",
          scale:0.5*i,
        })
        const marker = new AdvancedMarkerElement({
          position,
          content: pinGlyph.element,
          title: 'testTitle',
        })
        return marker
      })

      new MarkerClusterer({ markers, map: gmap })
    })
  }
  initMap()

})

// 不動産APIを使うときに参考にするコード
// onMounted(async () => {
//   const baseUrl = ''
//   const url  = baseUrl+''
//   const options = {
//     method : "GET",
//     headers : {
//     }
//   }
//   const data = await fetch(url, options)
//
//   const res = await data.json()
//   // レスポンスから１つめの緯度と経度を取得
//   // res.features[0].geometry.coordinates
//   }
// )
</script>

<template>
  <div id="gmap" style="height:500px;width:800px;" ></div>
</template>

<style  scoped>

</style>