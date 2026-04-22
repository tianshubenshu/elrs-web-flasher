<script setup>
import {ref, watch, watchPostEffect} from 'vue';
import {store} from '../js/state';
import {compareSemanticVersions} from '../js/version';

let firmware = ref(null);
let flashBranch = ref(false);
let hardware = ref(null);
let versions = ref([]);
let vendors = ref([]);
let radios = ref([]);
let targets = ref([]);
let luaUrl = ref(null);
let hasUrlParams = ref(false);

function setTargetFromParams() {
  let urlParams = new URLSearchParams(window.location.search);
  let target = urlParams.get('target');
  if (target) {
    hasUrlParams.value = true;
    store.target = {
      vendor: target.split('.')[0],
      radio: target.split('.')[1],
      target: target.split('.')[2],
      config: {}
    }
  }
}

watchPostEffect(() => {
  fetch(`./assets/${store.firmware}/index.json`).then(r => r.json()).then(r => {
    firmware.value = r
  })
})

function updateVersions() {
  if (firmware.value) {
    hardware.value = null
    store.version = null
    versions.value = [] // 确保清空
    if (flashBranch.value) {
      Object.entries(firmware.value.branches).forEach(([key, value]) => {
        versions.value.push({title: key, value: value}) // 修正：加上了 .value
        if (!store.version) store.version = value
      })
      Object.entries(firmware.value.tags).forEach(([key, value]) => {
        if (key.indexOf('-') !== -1) versions.value.push({title: key, value: value})
      })
      versions.value = versions.value.sort((a, b) => a.title.localeCompare(b.title))
    } else {
      let first = true;
      Object.keys(firmware.value.tags).sort(compareSemanticVersions).reverse().forEach((key) => {
        if (key.indexOf('-') === -1 || first) {
          versions.value.push({title: key, value: firmware.value.tags[key]})
          if (!store.version && key.indexOf('-') === -1) store.version = firmware.value.tags[key]
          first = false
        }
      })
    }
  }
}

watch(firmware, updateVersions)
watch(flashBranch, updateVersions)

watch([() => store.version, versions], () => {
  const item = versions.value.find(x => x.value === store.version)
  store.versionLabel = item ? item.title : null
}, { immediate: true })

watchPostEffect(() => {
  if (store.version) {
    store.folder = `./assets/${store.firmware}`
    fetch(`./assets/${store.firmware}/hardware/targets.json`).then(r => r.json()).then(r => {
      hardware.value = r
      store.vendor = null
      vendors.value = []
      for (const [k, v] of Object.entries(hardware.value)) {
        let hasTargets = false;
        Object.keys(v).forEach(type => hasTargets |= type.startsWith(store.targetType))
        if (hasTargets && v.name) vendors.value.push({title: v.name, value: k})
      }
      vendors.value.sort((a, b) => a.title.localeCompare(b.title))
    }).catch((_ignore) => {
    })
  }
})

const radioTitles = {
  'tx_2400': '2.4GHz 发射机/高频头',
  'tx_900': '900MHz 发射机/高频头',
  'tx_dual': '双频 2.4GHz/900MHz 发射机/高频头',
  'rx_2400': '2.4GHz 接收机',
  'rx_900': '900MHz 接收机',
  'rx_dual': '双频 2.4GHz/900MHz 接收机',
}

watchPostEffect(() => {
  radios.value = []
  let keepTarget = false
  if (store.vendor && hardware.value) {
    Object.keys(hardware.value[store.vendor]).forEach(k => {
      if (k.startsWith(store.targetType)) radios.value.push({title: radioTitles[k] || k, value: k})
      if (store.target && store.target.vendor === store.vendor && store.target.radio === k) keepTarget = true
    })
    if (radios.value.length === 1) {
      store.radio = radios.value[0].value
      keepTarget = true
    }
  }
  if (!keepTarget) store.radio = null
})

watchPostEffect(() => {
  targets.value = []
  let keepTarget = false
  if (store.version && hardware.value) {
    setTargetFromParams()
    const foundVersion = versions.value.find(x => x.value === store.version)
    const version = foundVersion ? foundVersion.title : '0.0.0'
    
    for (const [vk, v] of Object.entries(hardware.value)) {
      if (vk === store.vendor || store.vendor === null) {
        for (const [rk, r] of Object.entries(v)) {
          if (rk.startsWith(store.targetType) && (rk === store.radio || store.radio === null)) {
            for (const [ck, c] of Object.entries(r)) {
              if (flashBranch.value || compareSemanticVersions(version, c.min_version) >= 0) {
                targets.value.push({title: c.product_name, value: {vendor: vk, radio: rk, target: ck, config: c}})
                if (store.target && store.target.vendor === vk && store.target.radio === rk && store.target.target === ck) {
                  store.target.config = c
                  keepTarget = true
                }
              }
            }
          }
        }
      }
    }
  }
  targets.value.sort((a, b) => a.title.localeCompare(b.title))
  if (!keepTarget) store.target = null
})

watch([() => store.version, () => store.firmware], () => {
  let file = 'elrs.lua'
  versions.value.forEach(item => {
    if (item.value === store.version && item.title < '4.0.0') {
      file = 'elrsV3.lua'
    }
  })
  luaUrl.value = store.version ? `./assets/${store.firmware}/${store.version}/lua/${file}` : null
})

watch(() => store.target, (v, _oldValue) => {
  if (v) {
    store.vendor = v.vendor
    store.radio = v.radio
  }
})

function flashType() {
  return flashBranch.value ? '测试分支 (Branches)' : '正式版 (Releases)'
}
</script>

<template>
  <VRow justify="end">
    <VSwitch v-model="flashBranch" :label="flashType()" color="secondary" class="mr-4"/>
  </VRow>

  <VContainer max-width="600px">
    <VCardTitle>硬件选择</VCardTitle>
    <VCardText>请选择您要刷写的特定厂家硬件。如果列表中没有您的硬件，则说明该硬件目前暂不支持。</VCardText>
    <br>
    <VSelect :items="versions" v-model="store.version" density="compact" label="固件版本" variant="outlined"/>
    <VSelect :items="vendors" v-model="store.vendor" density="compact" label="厂家/品牌"
             :disabled="!store.version || hasUrlParams" variant="outlined"/>
    <VSelect :items="radios" v-model="store.radio" density="compact" label="无线电频段"
             :disabled="!store.vendor || hasUrlParams" variant="outlined"/>
    <VAutocomplete :items="targets" v-model="store.target" density="compact" label="产品名称/型号"
             :disabled="!store.version || hasUrlParams" variant="outlined"/>
    
    <VBtn :disabled="!luaUrl" :href="luaUrl" download color="primary" block class="mt-4">
      下载 ELRS Lua 脚本
    </VBtn>
  </VContainer>
</template>