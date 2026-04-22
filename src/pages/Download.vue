<script setup>
import {computed, ref, watchEffect} from "vue";
import * as zip from "@zip.js/zip.js";
import FileSaver from "file-saver";
import pako from 'pako';
import {store} from "../js/state.js";
import {generateFirmware} from "../js/firmware.js";
import {getDownloadFilename} from "../js/downloadFilename.js";

watchEffect(buildFirmware)

let zipped = ref(false)

const downloadFilename = computed(() => {
  if (!store.target?.config) return 'firmware.bin.gz'
  if (store.target.config.platform === 'esp8285') return getDownloadFilename('.bin.gz')
  if (zipped.value) return getDownloadFilename('.zip')
  return getDownloadFilename('.bin')
})

const files = {
  firmwareFiles: [],
  config: null,
  firmwareUrl: '',
  options: {}
}

async function buildFirmware() {
  if (store.currentStep === 3) {
    const [binary, {config, firmwareUrl, options}] = await generateFirmware()

    files.firmwareFiles = binary
    files.firmwareUrl = firmwareUrl
    files.config = config
    files.options = options

    if (store.target.config.upload_methods.includes('zip') ||
        (store.targetType === 'vrx' && (store.vendor === 'hdzero-goggle' || store.vendor === 'hdzero-boxpro'))) { // or HDZero Goggles
      zipped.value = true
    }
  }
}

async function downloadFirmware() {
  if (store.target.config.platform === 'esp8285') {
    const bin = pako.gzip(files.firmwareFiles[files.firmwareFiles.length - 1].data)
    const data = new Blob([bin], {type: 'application/octet-stream'})
    FileSaver.saveAs(data, getDownloadFilename('.bin.gz'))
  } else if (zipped.value) {
    // create zip file
    const zipper = new zip.ZipWriter(new zip.BlobWriter("application/zip"), {bufferedWrite: true})
    await zipper.add('bootloader.bin', new Blob([files.firmwareFiles[0].data.buffer], {type: 'application/octet-stream'}).stream())
    await zipper.add('partitions.bin', new Blob([files.firmwareFiles[1].data.buffer], {type: 'application/octet-stream'}).stream())
    await zipper.add('boot_app0.bin', new Blob([files.firmwareFiles[2].data.buffer], {type: 'application/octet-stream'}).stream())
    await zipper.add('firmware.bin', new Blob([files.firmwareFiles[3].data.buffer], {type: 'application/octet-stream'}).stream())
    FileSaver.saveAs(await zipper.close(), getDownloadFilename('.zip'))
  } else {
    const bin = files.firmwareFiles[files.firmwareFiles.length - 1].data.buffer
    const data = new Blob([bin], {type: 'application/octet-stream'})
    FileSaver.saveAs(data, getDownloadFilename('.bin'))
  }
}
</script>

<template>
  <VContainer max-width="600px">
    <VCardTitle>下载固件文件</VCardTitle>
    <VCardText>
      固件已根据您的硬件 <b>{{ store.target?.config?.product_name }}</b> 及指定选项配置完成。
      <br/>
      若要刷写该固件，请将设备进入 <b>WiFi 模式</b>，在浏览器中连接到设备页面，然后在 <b>Update (更新)</b> 选项卡中上传 <b>{{ downloadFilename }}</b> 文件。
    </VCardText>
    
    <VCardText v-if="store.target.config.platform === 'esp8285'">
      固件文件 <b>{{ downloadFilename }}</b> 应当直接上传刷写。<b>切勿</b> 解压或打开该文件，否则刷写时 <i>肯定</i> 会报错。
    </VCardText>
    
    <VCardText v-else-if="zipped">
      固件文件包含在 <b>{{ downloadFilename }}</b> 压缩包内，在上传到设备刷写之前，您需要先 <b>解压</b> 该文件。
    </VCardText>
    <br>
    <VBtn color="primary" @click="downloadFirmware()">下载固件</VBtn>
  </VContainer>
</template>