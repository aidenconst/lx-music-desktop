<template>
  <div :class="[$style.aside, { [$style.fullscreen]: isFullscreen }]">
    <ControlBtns v-if="appSetting['common.controlBtnPosition'] == 'left'" /><!--窗口控制按钮-->
    <div v-else :class="$style.logo">L X</div>
    <NavBar /><!--菜单按钮-->
    <div style="width:20%;height:100%;">
      <layout-toolbar id="toolbar" />
    </div>
  </div>
</template>

<script setup>
import { isFullscreen } from '@renderer/store'
import { appSetting } from '@renderer/store/setting'

import ControlBtns from './ControlBtns.vue'
import NavBar from './NavBar.vue'

</script>


<style lang="less" module>
@import '@renderer/assets/styles/layout.less';

.aside {
  // box-shadow: 0 0 5px rgba(0, 0, 0, .3);
  // transition: @transition-normal;
  // transition-property: background-color;
  // // background-color: @color-theme-sidebar;
  // // background-color: @color-aside-background;
  // // border-right: 2px solid var(--color-primary);
  // -webkit-app-region: drag;
  // -webkit-user-select: none;
  // display: flex;
  // flex-flow: column nowrap;
  -webkit-app-region: drag;
  height: 22px;
  padding: 0;
  display: flex;
  justify-content: flex-end;
  align-items: center;
  z-index: 10;
  // position: fixed;
  width: 100%;
  &.fullscreen {
    -webkit-app-region: no-drag;
    .logo {
      display: none;
    }
  }
}

.logo {
  box-sizing: border-box;
  padding: 0 13%;
  height: 50px;
  color: var(--color-nav-font);
  opacity: .8;
  flex: none;
  text-align: center;
  line-height: 50px;
  font-weight: bold;
  // -webkit-app-region: no-drag;
}

</style>
