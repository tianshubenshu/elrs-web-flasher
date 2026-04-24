<script setup>
import {resetState, store} from './js/state';

import FirmwareSelect from './pages/FirmwareSelect.vue';
import MainHardwareSelect from './pages/MainHardwareSelect.vue';
import VRXHardwareSelect from "./pages/BackpackHardwareSelect.vue";

import TransmitterOptions from './pages/TransmitterOptions.vue';
import ReceiverOptions from './pages/ReceiverOptions.vue';
import BackpackOptions from "./pages/BackpackOptions.vue";

import Download from "./pages/Download.vue";
import SerialFlash from "./pages/SerialFlash.vue";
import STLinkFlash from "./pages/STLinkFlash.vue";

import ReloadPrompt from './components/ReloadPrompt.vue';

function stepPrev() {
  if (store.currentStep === 1) {
    resetState()
  } else {
    store.currentStep--
  }
}

function disableNext() {
  if (store.currentStep === 1) {
    return !store.target ? "next" : false
  } else if (store.currentStep === 2) {
    return !store.options.flashMethod ? "next" : false
  } else if (store.currentStep === 3) {
    return "next"
  }
  return false
}

// 处理查询参数
let urlParams = new URLSearchParams(window.location.search);
store.targetType = urlParams.get('type');
if (store.targetType === "tx" || store.targetType === "rx")
  store.firmware = 'firmware'
else if (store.targetType)
  store.firmware = 'backpack'

store.options.flashMethod = urlParams.get('method');


const friendsList = [
  {
    name: '田鼠本鼠',
    avatar: '/tianshu.png', // 换成真实的图片路径
    altText: '田鼠本鼠头像',
    desc: '本站维护者/穿越机飞手\nB站/抖音/小红书',
    link: 'https://space.bilibili.com/395998010'
  },
  {
    name: '昊翔模型',
    avatar: '/HXMX.png',
     altText: '昊翔模型头像',
    desc: '资深爱好者/航模商家\nB站/抖音',
    link: 'https://www.douyin.com/user/MS4wLjABAAAAuLitcVssvTiuq6bSH_5QNt6m4gKg8I9gTUIOvqfiOII?from_tab_name=main'
  }
];

</script>

<template>
  <VApp>
    <VLayout>
      <ReloadPrompt />
      
      <VAppBar height="330" flat color="transparent" class="text-white">
        <v-container class="fill-height d-flex align-start justify-space-between pt-16" style="max-width: 950px;">
          
          <div class="d-flex align-center mt-n3">
            <div class="logo-container mr-3">
              <svg fill="#fff" viewBox="0 0 512 512" height="130" width="130">
                <path d="M237.6 14.8c47.2-5 96.3 10.3 131.9 41.8 5.3 4.9 12.5 9.4 13.4 17.2 1.3 8.8-6.8 17.5-15.8 16.4-8-.4-12.3-8.1-18.1-12.4-21-17.9-47.2-29.6-74.6-33.1-42.8-6-88.1 8.3-119.2 38.5-3.5 3.7-7.8 7.3-13.2 7.1-7.4.4-14.2-5.8-14.9-13.2-.4-4.3 1.1-8.8 4.1-11.9 28.2-28.4 66.5-46.7 106.4-50.4zm0 49c39-5.7 80.5 8.2 107.6 36.9 4.9 5.7 4.3 15.2-1.4 20.1-4.7 4.6-12.7 5.3-18.1 1.5-2.9-2.1-5.3-4.7-8-7-14.9-12.9-34-21-53.6-22.7-26.2-2.7-53.4 6.1-73 23.6-3.6 3.1-6.8 7.2-11.7 8.2-7.7 2.1-16.4-3.6-17.8-11.4-1-4.7.5-9.9 4-13.3 19.3-19.5 45-32.3 72-35.9zm8 48c23.8-3.2 48.8 5.7 65.2 23.1 4.9 5.5 4.5 14.9-1 19.9-5.1 5.5-14.7 5.7-20.1.5-6.3-6.2-14-11-22.6-13.1-16-4.2-33.9.5-45.8 12.1-3.7 4.1-9.7 5.8-15 4.2-4.7-1.4-8.6-5.3-10-9.9-1.3-4.9-.3-10.4 3.3-14.1 12.1-12.6 28.6-20.8 46-22.7zm-2.7 61c6.3-4.1 14.6-4.9 21.6-2.3 8.6 3 15.1 11.2 16.1 20.3 1 7.8-2.1 15.7-7.7 21.1l4.3 28.1h-16.7l-3.3-21.5c-.6 0-1.7.1-2.3.1l-3.3 21.4h-16.7l4.3-28.1c-4.5-4.4-7.6-10.4-7.9-16.8-.6-8.9 4.1-17.7 11.6-22.3m9.4 13.6c-5.6 2.5-6.1 11.1-.9 14.2 5 3.7 13-.5 12.8-6.7.4-5.9-6.7-10.4-11.9-7.5zM86 232c5.5-5.6 13.1-9.2 21-9.6 11.6-.8 23.2 5.5 29 15.6 5.6 9.4 5.8 21.8.3 31.4-2.5 4.6-6.4 8.2-10.1 11.8l-23.5 23.5c-7.6 7.7-12 18.4-11.9 29.2l.1 42.9c.1 13 8 23.8 13.2 35.2l32.5-32.5c10-9.8 26.6-11.4 38.4-3.8 12 7.2 17.9 23 13.5 36.3-1.8 6-5.6 11-10.1 15.2l-46.5 46.5c-5.3 5.4-11 10.5-15.1 16.9-4.3 6.4-7 13.8-8.6 21.3H91.5c2.2-15.7 9.5-30.6 20.7-41.8l54.5-54.5c3.1-2.9 6-6.3 6.5-10.6 1.2-6.4-2.4-13.2-8.3-16-5.7-3-13.2-1.4-17.6 3.2l-24 24c-6.5 6.3-12.5 13.5-21 17.2-11.3 5.4-24.2 3.1-36.3 3.6v-16.5c7.9-.2 15.8.4 23.6-.4L80 400.7c-3.9-8-5.8-16.9-5.7-25.8v-42c.1-14.6 6-29 16.2-39.5l28.9-29.1c4.4-4.4 5.6-11.6 2.8-17.1-2.7-5.7-9.2-9.3-15.4-8.4-3.8.4-7.1 2.4-9.7 5.1l-39.4 39.5c-8.3 8.4-13.9 19.5-15.8 31.2l-7.4 47a109.61 109.61 0 0 0-1.5 18.1v111c0 7.1.6 14.1 2.2 21H18.4c-1.4-6.9-1.9-14-1.9-21V381.8c-.4-16.2 3.5-32 5.7-47.9 2.4-13 3-26.5 8.3-38.8 3.8-9.1 9.5-17.3 16.5-24.3L86 232zm286.5 14.8c2.7-12.2 13.2-22.1 25.6-24 9.9-1.7 20.4 1.6 27.5 8.7l40.5 40.5c11.2 11.3 18.4 26.3 20.6 42.1l7.7 49.1c1.5 9.5 1.1 19.2 1.2 28.9v84c-.1 12 .6 24.1-1.9 36h-16.8c2.8-11.5 2.1-23.3 2.2-35v-84c0-9.6.4-19.3-1.2-28.8l-8-50.3c-2.1-12.5-8.8-24.1-18-32.7l-38-38c-5.5-5.5-15.3-5.8-20.8-.2-5.9 5.2-6.4 15.2-1 20.9 6.4 6.7 13.1 13.1 19.6 19.7 5.9 6.1 12.5 11.8 17 19 6.2 9.5 9.3 21 9.1 32.3l-.1 44.6c-.5 14.9-9.1 27.6-15.2 40.7 7.8.7 15.7.2 23.6.4v16.5c-10-.4-20.2 1.1-30-1.4-7.1-1.8-13.6-5.8-18.8-10.9l-33-33c-5.5-5.5-15.1-5.7-20.7-.2-6.2 5.4-6.4 15.7-.6 21.5l57 57.1c11.2 11.2 18.5 26.2 20.7 41.8H404c-2.2-11.3-7.6-21.9-15.7-30.1l-57.4-57.5c-10-10.1-11.5-27.2-3.5-39 7.6-12 23.7-17.4 36.9-12.5 8.2 2.6 13.8 9.5 19.7 15.3l24 24c5.2-11.4 13.1-22.2 13.2-35.2v-42.9c.1-10.8-4.3-21.6-11.9-29.2l-29-29.1c-7.3-7.7-10.3-18.9-7.8-29.1zm-215.6.4h198.2v16.5H157c-.2-5.5-.1-11-.1-16.5zm-5.4 33.6c11.5-2.8 24.3 4 28.4 15.1 3.3 8 1.8 17.7-3.6 24.5-6.2 8.1-17.7 11.5-27.3 8.1-10.2-3.3-17.5-13.8-16.8-24.5.2-11 8.6-21 19.3-23.2m1.9 16.7c-5.7 2.4-6.3 11.1-1.1 14.3 4.7 3.5 12.2.1 12.8-5.8 1-6.1-6.2-11.4-11.7-8.5zm53-17.3h99.1v66.1h-99.1v-66.1m16.6 16.5v33h66v-33h-66zm127.5-16.1c7.4-1.5 15.5.8 21.1 5.8 7 6.1 10 16.4 7.3 25.3-2.6 9.3-11.1 16.7-20.8 17.7-8.4 1.1-17.2-2.5-22.4-9.2-5.4-6.7-6.9-16.3-3.7-24.3 3-7.7 10.2-13.8 18.5-15.3m1.6 16.7c-5.7 1.9-7.1 10.3-2.3 14 4.3 4 12.1 1.4 13.3-4.3 1.9-6.1-5.1-12.4-11-9.7zm-215.2 48.2c11.4-6.6 25.1-8.9 38.1-6.7 13.6 2.3 26.2 9.7 34.9 20.4 11 13.2 15.5 31.5 11.9 48.3-4.6 23-24.5 42-47.8 45.3l-2.5-16.3c11.2-1.8 21.5-8.2 27.8-17.6 6.6-9.5 8.9-21.9 6-33.1-2.9-11.9-11.5-22.3-22.6-27.5-11.6-5.5-25.8-5.1-37 1.1-11.2 6-19.3 17.4-21.2 30l-16.3-2.5c2.5-17.1 13.4-32.8 28.7-41.4zm175.5 3.9c13.5-10.2 31.6-13.9 48-9.8 22.3 5.2 40.2 24.8 43.5 47.4l-16.3 2.5c-1.9-12.1-9.4-23.1-19.9-29.3-10.3-6.2-23.3-7.5-34.6-3.4-10.6 3.7-19.5 12-24 22.2-5 11.2-4.6 24.7 1.2 35.6 5.9 11.5 17.5 19.9 30.4 21.8l-2.4 16.2c-17-2.4-32.5-13.2-41.2-28-6.6-11-9.2-24.3-7.4-37 1.9-14.9 10.4-29.1 22.7-38.2z"/>
                <path d="M247.8 395.8h16.5v16.5h-16.5c-.1-5.5-.1-11 0-16.5zm-49.6 66.1h115.6v33h66.1v16.2l-134.9.2c-28.7-.1-57.3 0-86 0-8.9-.2-17.9.4-26.8-.2v-16.2h66.1l-.1-33m16.5 16.5v16.5h82.6v-16.5h-82.6z"/>
              </svg>
        </div>
<div class="header-text d-flex flex-column justify-center">
    <div 
      style="
        font-size: 52px !important; 
        font-weight: 900; 
        line-height: 0.9; 
        margin-bottom: 8px;
        letter-spacing: -1px; /* 紧凑一点更有力量感 */
      "
    >
      ExpressLRS&trade;
    </div>
    <div
      style="
        font-size: 25px !important; 
        font-weight: 300; 
        letter-spacing: 12px !important; 
        opacity: 0.8;
        margin-left: 4px;
      ">WEB FLASHER</div>
  </div>
</div>

      <div class="my-brand-container d-flex flex-column align-end mt-n1">
        <div class="my-brand-container d-flex flex-column align-end">
          <div class="brand-logo mb-2" style="margin-right: -19px;"> 
              <img src="/logo.svg" alt="TIANSHU Logo" height="80" style="width: auto; display: block;" />
          </div>
          <div 
      style="
        font-size: 33px !important; 
        font-weight: 310; 
        letter-spacing: 27.8px;
        line-height: 0.6; 
        color: #ffffff; 
        text-align: right;
        margin-right: 41px;
        white-space: nowrap;
        text-shadow: 0 4px 15px rgba(0,0,0,0.4);
      ">中文镜像站
           </div>
        </div>
      </div>

          <div class="text-subtitle-2 position-absolute right-0 bottom-0 pa-4" style="opacity: 0.6;">
            本站由 @田鼠本鼠 维护
          </div>
        </v-container>
      </VAppBar>
      <VMain>
        <div class="section mt-n12"> <VFadeTransition mode="out-in">
            <VContainer max-width="1280px" v-if="!store.targetType" style="display: grid; gap: 40px;">
              <FirmwareSelect />
            </VContainer>
            
            <VContainer max-width="1024px" v-else>
              <div class="containerMain">
                <VStepper v-model="store.currentStep" :items="['硬件选择', '参数设置', '开始刷写']" hideActions>
                  <template v-slot:item.1>
                    <MainHardwareSelect v-if="store.firmware === 'firmware'" />
                    <VRXHardwareSelect vendor-label="发射机模块" v-if="store.targetType === 'txbp'" />
                    <VRXHardwareSelect vendor-label="高频头类型" v-if="store.targetType === 'vrx'" />
                    <VRXHardwareSelect vendor-label="天线追踪器类型" v-if="store.targetType === 'aat'" />
                    <VRXHardwareSelect vendor-label="计时器类型" v-if="store.targetType === 'timer'" />
                  </template>
                  
                  <template v-slot:item.2>
                    <TransmitterOptions v-if="store.targetType === 'tx'" />
                    <ReceiverOptions v-else-if="store.targetType === 'rx'" />
                    <BackpackOptions v-else />
                  </template>
                  
                  <template v-slot:item.3>
                    <Download v-if="store.options.flashMethod === 'download'" />
                    <Download v-else-if="store.options.flashMethod === 'wifi'" />
                    <STLinkFlash v-else-if="store.options.flashMethod === 'stlink'" />
                    <SerialFlash v-else />
                  </template>
                  
                  <VStepperActions 
                    :disabled="disableNext()" 
                    prev-text="上一步" 
                    next-text="下一步" 
                    @click:prev="stepPrev" 
                    @click:next="store.currentStep++"
                  />
                </VStepper>
              </div>
            </VContainer>
          </VFadeTransition>
<v-container max-width="1280px" class="mt-n9 pb-7">

  <div class="containerMain pt-0 px-10 pb-4" style="padding-top: 0 !important;">
    
    <div class="text-center mb-2">
      <h2 class="text-h5 font-weight-black mb-0" style="letter-spacing: 1px; color: #333;">
        本页面资源来自 expresslrs.org
      </h2>
      
      <div class="mt-0 mb-1">
        <a href="http://expresslrs.github.io/web-flasher" 
           target="_blank" 
           style="color: #9e9e9e; text-decoration: none; font-size: 0.85rem;">
           http://expresslrs.github.io/web-flasher
        </a>
      </div>

      <div class="text-caption text-grey-darken-1">特别鸣谢镜像站技术支持</div>
    </div>

    <v-row justify="center" class="mt-3">
      <v-col v-for="(friend, index) in friendsList" :key="index" cols="12" md="3" class="pa-4">
        <v-card 
          variant="flat" 
          class="friend-card text-center d-flex flex-column align-center pt-5 px-8 pb-8" 
          :href="friend.link" 
          target="_blank"
          rel="noopener noreferrer"
          style="height: 250px; border: 1px solid #eee !important;" 
        >
          <v-avatar size="80" class="mb-5 elevation-2">
            <v-img :src="friend.avatar" :alt="friend.name"></v-img>
          </v-avatar>

          <div class="text-h6 font-weight-bold mb-3" style="color: #222; font-size: 18px !important;">
  {{ friend.name }}
</div>

          <div class="text-grey-darken-2" style="font-size: 13px !important; line-height: 1.5; min-height: 8em; white-space: pre-line;">
  {{ friend.desc }}
</div>
        </v-card>
      </v-col>
    </v-row>
  </div>
</v-container>
        </div>
      </VMain>
      
    </VLayout>
  </VApp>
</template>

<style>
.header {
  background-color: red;
}

.v-app-bar {
  background: linear-gradient(45deg, #9dc66b 5%, #4fa49a 30%, #4361c2) !important;
}

.v-toolbar__content {
  justify-content: center;
}

.v-stepper-window {
  padding: 24px;
  margin: 0 !important;
}

.header-main {
  display: flex;
  flex-direction: column;
  font-size: 40px;
  line-height: normal;
  margin-bottom: 4rem;
}

.logoContainer {
  margin-bottom: 4.25rem;
  padding: 20px;
}

.header-main h1 {
  font-size: clamp(3rem, 5vw, 4rem);
  color: #fff;
  font-weight: 600;
}

.header-main h2 {
  font-size: clamp(1.5rem, 2.9vw, 2.5rem);
  color: #fff;
  letter-spacing: 1.075rem;
  font-weight: 200;
}

@media (max-width: 640px) {
  .v-toolbar__content {
    justify-content: center;
    flex-direction: column;
  }

  .logoContainer {
    margin-bottom: -1rem;
  }
}

.friend-card {
  /* 基础变换：加入微小的缩放，让动画更灵动 */
  transition: all 0.4s cubic-bezier(0.165, 0.84, 0.44, 1);
  
  /* 圆角：既然你说不够圆，那就直接上 20px */
  border-radius: 19px !important; 
  
  /* 背景：用纯白，并配合一个淡淡的边框，在静止时也能一眼看出来 */
  background-color: #f8f9fa !important;
  border: 1px solid rgba(0, 0, 0, 0.15) !important;
  
  /* 关键：静止时也给一个若有若无的阴影，增加立体感 */
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.07) !important;
  
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.friend-card:hover {
  /* 悬停：位移加缩放，视觉冲击力更强 */
  transform: translateY(-2px) scale(1.002); 
  
  /* 阴影：参考图中的那种深邃感，扩散范围加大 */
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.22) !important;
  
  /* 边框：颜色稍微亮一点，引导视觉 */
  border-color: #42A5F5 !important;
}

/* 别忘了这个！杀掉 Vuetify 自带的变灰层，保持卡片清爽 */
.friend-card .v-card__overlay {
  display: none !important;
}
</style>
