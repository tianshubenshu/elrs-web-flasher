<script setup>
import {hasFeature, store} from "../js/state.js";
import FanRuntime from "./FanRuntime.vue";
import MelodyInput from "./MelodyInput.vue";
</script>

<template>
  <VNumberInput v-model="store.options.tx.telemetryInterval" 
                label='遥测数据上报间隔' 
                suffix="毫秒 (ms)"
                :step="10" :min="100" :max="1000"/>

  <VCheckbox v-model="store.options.tx.uartInverted" 
             label="UART 串口信号取反"
             v-if="store.target?.config?.platform==='stm32'"/>

  <FanRuntime v-model="store.options.tx.fanMinRuntime"/>

  <VCheckbox v-model="store.options.tx.higherPower" 
             label='解锁超高发射功率 (请注意散热!)'
             v-if="hasFeature('unlock-higher-power')"/>

  <MelodyInput v-model:melody-type="store.options.tx.melodyType"
               v-model:melody-tune="store.options.tx.melodyTune"
               v-if="hasFeature('buzzer')"/>
</template>