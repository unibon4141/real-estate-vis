<script setup>
const download =  async () => {
  const baseUrl = 'http://localhost:9000'
  const url  = baseUrl+'/proxy/?path=XIT001&qNames=year&qNames=city&qVals=2020&qVals=13101'

  const options = {
    method : "GET",
    headers : {
    }
  }
  const data = await fetch(url, options)

  const res = await data.json()
  console.log(res)
  console.log(res.data[0].Area)
}

</script>
<template>
<div>Main</div>
<div @click="download">Clicked here !</div>
</template>
<style>

</style>