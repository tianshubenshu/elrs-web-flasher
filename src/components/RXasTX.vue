<script setup>
import {ref} from "vue";
import {store} from "../js/state.js";

let enabled = defineModel('enabled')
let type = defineModel('type')
type.value = "0"

// 汉化后的模式选择列表
let items = ref([
  {title: "接收机作为内置高频头 (全双工 Full-duplex)", value: "0"},
  {title: "接收机作为外置高频头 (半双工 Half-duplex)", value: "1"}
])
</script>

<template>
  <VCheckbox v-model="enabled" 
             :label="'将接收机作为发射机刷写' + (store.target.config.platform.startsWith('esp32') ? '' : ' (仅限内置全双工模块)')"/>
  
  <VSelect v-model="type" 
           :items="items" 
           label="刷写模式"
           v-if="store.target.config.platform.startsWith('esp32') && enabled"
           variant="outlined"
           density="compact"/>
</template>