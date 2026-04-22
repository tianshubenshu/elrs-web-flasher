<script setup>
import {VSelect} from "vuetify/components";

let model = defineModel()
const props = defineProps({methods: Array})

const flashMethods = [
  {value: 'download', title: '下载到本地'},
  {value: 'uart', title: '串口刷写（UART）'},
  {value: 'betaflight', title: '通过Betaflight端口刷写'},
  {value: 'etx', title: '通过EdgeTX端口刷写'},
  {value: 'passthru', title: '透传模式'},
  {value: 'wifi', title: '通过WiFi刷写'},
  {value: 'stlink', title: 'STLink'},
]

function getFlashMethods() {
  return flashMethods.filter(v => v.value === 'download' || (props.methods && props.methods.includes(v.value)))
}
</script>

<template>
  <VSelect v-model="model" label="刷写模式" :items="getFlashMethods()"/>
</template>
