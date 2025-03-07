<template>
  <div v-show="!isFullscreen" ref="dom_btns" :class="$style.controlBtn">
    <button type="button" :class="[$style.btn, $style.close]" :aria-label="$t('close')" ignore-tip :title="$t('close')" @click="closeWindow">
      <svg :class="$style.controlBtniIcon" version="1.1" xmlns="http://www.w3.org/2000/svg" xlink="http://www.w3.org/1999/xlink" width="100%" viewBox="0 0 24 24" space="preserve">
        <use xlink:href="#icon-window-close" />
      </svg>
    </button>
    <button type="button" :class="[$style.btn, $style.min]" :aria-label="$t('min')" ignore-tip :title="$t('min')" @click="minWindow">
      <svg :class="$style.controlBtniIcon" version="1.1" xmlns="http://www.w3.org/2000/svg" xlink="http://www.w3.org/1999/xlink" width="100%" viewBox="0 0 24 24" space="preserve">
        <use xlink:href="#icon-window-minimize" />
      </svg>
    </button>
    <router-link :class="$style.logo" to="/setting">
      <svg t="1741087064161" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="12093" width="50" height="50">
        <path :fill="'var(--color-primary-alpha-900)'" d="M42.667 516.693A469.333 469.333 0 1 0 512 52.053a469.333 469.333 0 0 0-469.333 464.64z" p-id="12094" data-spm-anchor-id="a313x.search_index.0.i30.17f83a81h2zJQq" class="selected"></path>
        <path :fill="'var(--color-primary)'" d="M654.933 12.373a217.173 217.173 0 0 1-105.386 49.494 409.6 409.6 0 0 0-128 39.68 184.747 184.747 0 0 0-42.667 29.866c-20.053 17.494-34.987 49.494-30.293 59.734s50.346 72.106 107.946 154.026S569.6 504.32 581.973 521.387a269.227 269.227 0 0 1 20.054 32.426c0 2.56-10.24 0-20.054-2.56A256 256 0 0 0 421.547 576a260.267 260.267 0 0 0-97.707 96.853 170.667 170.667 0 0 0-7.68 131.84 183.893 183.893 0 0 0 90.453 94.294 146.347 146.347 0 0 0 82.774 20.053 221.867 221.867 0 0 0 75.093-5.12 216.32 216.32 0 0 0 165.547-183.893c2.56-47.36-2.56-62.294-65.28-170.667-97.707-170.667-185.6-322.987-185.6-325.547l40.106-5.12a165.973 165.973 0 0 0 145.494-85.333c12.373-22.187 12.373-29.867 12.373-77.227A221.44 221.44 0 0 0 672 6.4c-2.133-8.96-4.267-6.4-17.067 5.973z" p-id="12095"></path>
      </svg>
    </router-link>
  </div>
</template>

<script setup>
import { minWindow, closeWindow } from '@renderer/utils/ipc'
import { onMounted, onBeforeUnmount, ref, useCssModule } from '@common/utils/vueTools'
// import { getRandom } from '../../utils'
import { isFullscreen } from '@renderer/store'

const dom_btns = ref()

const cssModule = useCssModule()

const handle_focus = () => {
  if (!dom_btns.value) return
  dom_btns.value.classList.remove(cssModule.hover)
}
const handle_mouseenter = () => {
  dom_btns.value.classList.add(cssModule.hover)
}
const handle_mouseleave = () => {
  dom_btns.value.classList.remove(cssModule.hover)
}


onMounted(() => {
  window.app_event.on('focus', handle_focus)
  dom_btns.value.addEventListener('mouseenter', handle_mouseenter)
  dom_btns.value.addEventListener('mouseleave', handle_mouseleave)
})
onBeforeUnmount(() => {
  window.app_event.off('focus', handle_focus)
  dom_btns.value.removeEventListener('mouseenter', handle_mouseenter)
  dom_btns.value.removeEventListener('mouseleave', handle_mouseleave)
})
</script>

<style lang="less" module>
@import '@renderer/assets/styles/layout.less';

@control-btn-width: @height-toolbar * .26;
@control-btn-height: 6%;
.controlBtn {
  -webkit-app-region: no-drag;
  display: flex;
  gap: 10px;
  width:18%;
  transition: opacity @transition-normal;
  height: 100%;
  // padding-left: 20px;
    // &.hover {
    //   opacity: 1;
    //   .controlBtniIcon {
    //     opacity: 1;
    //   }
    // }
}
.controlBtn > button:nth-child(1){
  margin-left: 26px;
}
.controlBtniIcon:hover{
  opacity: 1;
}
.logo {
    display: flex;
    align-items: center;
    justify-content: flex-end;
    align-content: center;
    flex-direction: row-reverse;
    flex-wrap: nowrap;
    height: 100%;
}
.logo svg{
  width: 50px;
  height: 50px;
}
.btn {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  border: none;
  padding: 0;
  margin: 0;
  opacity: .5;
  cursor: pointer;
  margin-top:8px;
  transition: all 0.2s ease;
  background-color: transparent;
  background-size: 10px;
  background-repeat: no-repeat;
  background-position: center;
  &.hover {
    opacity: 1;
    .controlBtniIcon {
      opacity: 1;
    }
  }
  &.min {
    background-color: var(--color-btn-min);
  }
  // &.max {
  //   background-color: var(--color-btn-max);
  // }
  &.close {
    background-color: var(--color-btn-close);
  }
}

.controlBtniIcon {
  opacity: 0;
  transition: opacity 0.2s ease-in-out;
}


</style>
