<script setup>
import {hasFeature, store} from "../js/state.js";
import FanRuntime from "./FanRuntime.vue";
</script>

<template>
  <VTextField v-model="store.options.rx.uartBaud" label='UART 串口波特率' variant="outlined"/>

  <VCheckbox v-model="store.options.rx.lockOnFirstConnect" label='首次连接锁定 (Lock on first connection)'/>

  <VCheckbox v-model="store.options.rx.r9mmMiniSBUS" 
             label='将 SBUS 针脚用作 UART 串口' 
             v-if="hasFeature('sbus-uart')"/>

  <FanRuntime v-model="store.options.rx.fanMinRuntime"/>
</template>