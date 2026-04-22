<script setup>
import {ref} from "vue";

let ssid = defineModel('ssid')
let password = defineModel('password')

let showPassword = ref(false)
</script>

<template>
  <VTextField v-model="ssid" 
              name="wifi-ssid" 
              label="WiFi 名称 (SSID)" 
              variant="outlined"/>

  <VTextField v-model="password" 
              name="wifi-password" 
              label="WiFi 密码"
              :append-inner-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
              :type="showPassword ? 'text' : 'password'"
              @click:append-inner="showPassword = !showPassword"
              variant="outlined"/>
</template>