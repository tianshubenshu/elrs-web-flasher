<script setup>
import {store} from '../js/state';
import HoverCard from '../components/HoverCard.vue';

const emit = defineEmits(['onClick']);

// Set firmware and targetType in state store, then update page to pages selection page
function setFirmware(firmware, targetType) {
  store.firmware = firmware;
  store.targetType = targetType;
  emit('onClick');
}
</script>

<template>
  <div class="containerMain">
    <div class="containerHeader">
      <VCardTitle>主要遥控器固件</VCardTitle>
      <VCardSubtitle>无论是发射端还是接收端，我们都能满足你的需求</VCardSubtitle>
    </div>
    <VRow class="tile-row">
      <VCol md="6">
        <HoverCard min-height="100%" @click="setFirmware('firmware', 'tx')"
                    image="transmitter_6275858.png" hover-image="transmitter_6276574.png"
                    title="发射机 (TX)"
                    text="更新（刷写）您的外置高频头模块，JR 标准接口 (Micro) 或 Nano 接口模块；或是遥控器内置的发射模块。"/>
      </VCol>
      <VCol md="6">
        <HoverCard min-height="100%" @click="setFirmware('firmware', 'rx')"
                    image="reciever_6276002.png" hover-image="reciever_6276814.png"
                    title="接收机 (RX)"
                    text="无论是串行连接（CRSF协议）还是传统的 PWM 接收机，都可以在这里更新固件。"/>
      </VCol>
    </VRow>
  </div>
  <div class="containerMain">
    <div class="containerHeader">
      <VCardTitle>背包 (Backpack) 固件</VCardTitle>
      <VCardSubtitle>无线联动一切设备，尽在掌握！</VCardSubtitle>
    </div>
    <VRow class="tile-row">
      <VCol md="3">
        <HoverCard min-height="100%" @click="setFirmware('backpack', 'txbp')"
                    image="backpack_723137.png" hover-image="backpack_723278.png"
                    title="发射机背包"
                    text="内置于大多数发射机中，用于与其他背包通信，或通过 WiFi 连接到运行 Mission Planner 的电脑以支持 MAVLink 协议。"/>
      </VCol>
      <VCol md="3">
        <HoverCard min-height="100%" @click="setFirmware('backpack', 'vrx')"
                    image="vr-glasses_8736938.png" hover-image="vr-glasses_8737003.png"
                    title="图传接收机 (VRX)"
                    text="内置背包的 VRx（如 HDZero 眼镜、SkyZone 模块）或 DIY 接收机方案。让您的眼镜始终锁定在正确频道。"/>
      </VCol>
      <VCol md="3">
        <HoverCard min-height="100%" @click="setFirmware('backpack', 'aat')"
                    image="satellite_2637312.png" hover-image="satellite_2637314.png"
                    title="天线追踪器 (AAT)"
                    text="正在进行远程飞行并需要天线始终精准对准飞机方向？这就是为您准备的背包固件。"/>
      </VCol>
      <VCol md="3">
        <HoverCard min-height="100%" @click="setFirmware('backpack', 'timer')"
                    image="stopwatch_4354897.png" hover-image="stopwatch_4355918.png"
                    title="比赛计时器"
                    text="连接到 RotorHazard 赛道计时系统，并在 OSD 中实时显示圈速和当前排名，助你掌控比赛。"/>
      </VCol>
    </VRow>
  </div>
</template>

<style scoped>
.tile-row {
  margin: -12px;
}

.tile-row :deep(.v-col) {
  padding: 12px;
}

.v-card-title {
  padding: 0;
  font-size: 1.5rem;
  font-weight: 600;
  color: #242527;
}

.v-card-subtitle {
  padding: 0;
  font-size: 1rem;
  color: #242527;
}
</style>