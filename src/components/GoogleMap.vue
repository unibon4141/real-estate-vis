<script setup>
import { onMounted, ref } from 'vue'
import { Loader } from "@googlemaps/js-api-loader"

const loader = new Loader({
  apiKey: import.meta.env.VITE_API_KEY,
  version: "weekly",
  libraries: ["places"]
})
function convertToInteger(str) {
  // カンマと空白を取り除く
  let cleanedStr = str.replace(/,/g, '').replace(/\s/g, '')

  let multiplier = 1

  // 億の処理
  if (cleanedStr.includes('億')) {
    cleanedStr = cleanedStr.replace('億', '')
    multiplier *= 100000000
  }

  // 万の処理
  if (cleanedStr.includes('万')) {
    cleanedStr = cleanedStr.replace('万', '')
    multiplier *= 10000
  }

  // 数値に変換して倍率をかける
  const num = parseFloat(cleanedStr) * multiplier
  return num
}

function valueToColor(value, min, max) {
  // 入力値が範囲内にあることを確認
  if (value < min) value = min
  if (value > max) value = max

  // 値を0から1の範囲に正規化
  let normalizedValue = (value - min) / (max - min)

  // RGBの赤から青のグラデーションを計算
  let r = Math.round(255 * normalizedValue)
  let g = 0; // 緑は常に0
  let b = Math.round(255 * (1 - normalizedValue))

  // 16進数のカラーコードに変換
  let color = `#${r.toString(16).padStart(2, '0')}${g.toString(16).padStart(2, '0')}${b.toString(16).padStart(2, '0')}`

  return color
}


const drawMaker = async () => {
  const baseUrl = import.meta.env.VITE_BASE_URL
  const url = baseUrl + import.meta.env.VITE_TEST_REQUEST_PATH
  const data = await fetch(url)
  let ls = []
  const res = await data.json()
  res.features.forEach((item) => {
  ls.push({
    lat:item.geometry.coordinates[1],
    lng:item.geometry.coordinates[0]
  })

})

  const { AdvancedMarkerElement, PinElement } = await google.maps.importLibrary("marker")

  const markers = ls.map((position, i) => {
    const label = labels[i % labels.length]
    const rawPrice = res.features[i].properties.u_transaction_price_total_ja
    const price = rawPrice.length > 0 ? convertToInteger(rawPrice) : 0
    const scale = 1
    const pinGlyph = new PinElement({
      glyph: label,
      glyphColor: "#0000ff",
      background: valueToColor(price, 0,2000000000),
      scale,
    })
    const marker = new AdvancedMarkerElement({
      position,
      content: pinGlyph.element,
      title:rawPrice,
      map:gmap
    })
  })

}
const labels = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
let gmap

onMounted(  ()=>{
  const initMap =  () => {
    loader.load().then( async () => {
      const { Map } = await google.maps.importLibrary("maps");
      const centerPosition = { lat: 35.643560897913034, lng:139.67117539078941 }

      gmap = document.getElementById('gmap')
      gmap = new Map(gmap, {
        center: centerPosition,
        zoom: 15,
        mapId: "TEST_MAP_ID",
      })

    })
  }
  initMap()

  drawMaker()
})

</script>

<template>
  <div id="gmap" style="height:500px;width:800px;" ></div>
</template>

<style  scoped>

</style>