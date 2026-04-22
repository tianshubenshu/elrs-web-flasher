<script setup>
import {VSelect, VTextField} from "vuetify/components";

let melodyType = defineModel('melodyType')
let melodyTune = defineModel('melodyTune')

// 汉化后的蜂鸣器类型列表
const melodyTypes = [
  {value: 0, title: '静音 (无蜂鸣声)'},
  {value: 1, title: '仅响一声'},
  {value: 2, title: '无旋律 (仅提示音)'},
  {value: 3, title: '默认旋律'},
  {value: 4, title: '自定义旋律'}
]
</script>

<template>
  <VSelect v-model="melodyType" label="蜂鸣器模式" :items="melodyTypes" variant="outlined"/>
  
  <VTextField v-model="melodyTune" 
              label="自定义旋律代码" 
              placeholder="例如: B5 10 B5 10"
              v-if="melodyType===4" 
              variant="outlined"/>
</template>