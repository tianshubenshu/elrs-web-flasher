<script setup>
import {hasFeature} from "../js/state.js";

let model = defineModel()
</script>

<template>
  <VNumberInput v-model="model" 
                label='风扇最小运行时间' 
                suffix="秒"
                :step="10" 
                :min="0" 
                :max="3600"
                v-if="hasFeature('fan')"/>
</template>