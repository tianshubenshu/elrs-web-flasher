<script setup>
import {store} from "../js/state.js";

let model = defineModel()
</script>

<template>
  <VNumberInput v-model="model" 
                :label="'WiFi 自动开启等待时间' + (model === null ? ' (已禁用)' : '')"
                suffix="秒"
                :step="10" :min="0" :max="120"
                v-if="store.target?.config?.platform!=='stm32'"
                variant="outlined"/>
</template>