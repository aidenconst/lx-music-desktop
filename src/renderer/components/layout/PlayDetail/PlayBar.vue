<template>
  <div :class="$style.footer">
    <div :class="$style.footerLeft">
      <control-btns />
      <div :class="$style.progressContainer">
        <div :class="$style.progressContent">
          <common-progress-bar
            :class-name="$style.progress"
            :progress="progress"
            :handle-transition-end="handleTransitionEnd"
            :is-active-transition="isActiveTransition"
          />
        </div>
      </div>
      <div :class="$style.timeLabel"><span :class="$style.status" style="margin-right: 15px">{{ status }}</span><span>{{ nowPlayTimeStr }}</span><span style="margin: 0 5px;">/</span><span>{{ maxPlayTimeStr }}</span></div>
    </div>
    <div :class="$style.playControl">
      <div :class="$style.playBtn" :aria-label="$t('player__prev')" @click="playPrev()">
        <svg viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" width="50" height="50">
          <path d="M356.77 605.22L701.8 849.53c69.8 49.42 166.29-0.49 166.29-86.01V261.25c0-85.52-96.49-135.43-166.29-86.01L356.77 419.55c-64.02 45.34-64.02 140.34 0 185.67zM210.42 154.36c30.38 0 55 24.62 55 55v606.07c0 30.38-24.62 55-55 55s-55-24.62-55-55V209.36c0-30.38 24.62-55 55-55z" p-id="1151"></path>
        </svg>
      </div>
      <div :class="$style.playBtn" :aria-label="isPlay ? $t('player__pause') : $t('player__play')" @click="togglePlay">
        <svg v-if="isPlay" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" width="60" height="60">
          <path d="M332.26 853.89c-49.71 0-90-40.29-90-90v-503c0-49.71 40.29-90 90-90s90 40.29 90 90v503c0 49.7-40.3 90-90 90zM691.26 853.89c-49.71 0-90-40.29-90-90v-503c0-49.71 40.29-90 90-90s90 40.29 90 90v503c0 49.7-40.3 90-90 90z" p-id="933"></path>
        </svg>
        <svg v-else viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" width="60" height="60">
          <path d="M783.74 401.86L372.23 155.28c-85.88-51.46-195.08 10.41-195.08 110.53v493.16c0 100.12 109.2 161.99 195.08 110.53l411.51-246.58c83.5-50.04 83.5-171.03 0-221.06z" p-id="12091"></path>
        </svg>
      </div>
      <div :class="$style.playBtn" :aria-label="$t('player__next')" @click="playNext()">
        <svg viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" width="50" height="50">
          <path d="M665.47 417.65l-345.03-244.3c-69.8-49.42-166.29 0.49-166.29 86.01v502.27c0 85.52 96.49 135.43 166.29 86.01l345.03-244.31c64.02-45.34 64.02-140.34 0-185.68zM811.82 868.52c-30.38 0-55-24.62-55-55V207.46c0-30.38 24.62-55 55-55s55 24.62 55 55v606.07c0 30.37-24.62 54.99-55 54.99z" p-id="1369"></path>
        </svg>
      </div>
    </div>
  </div>
</template>

<script setup>
import { playNext, playPrev, togglePlay } from '@renderer/core/player'
import { status, isPlay } from '@renderer/store/player/state'
import usePlayProgress from '@renderer/utils/compositions/usePlayProgress'

import ControlBtns from './components/ControlBtns.vue'

const {
  nowPlayTimeStr,
  maxPlayTimeStr,
  progress,
  isActiveTransition,
  handleTransitionEnd,
} = usePlayProgress()

</script>

<style lang="less" module>
@import '@renderer/assets/styles/layout.less';

.footer {
  flex: 0 0 100px;
  overflow: hidden;
  display: flex;
  align-items: center;
}
.footerLeft {
  flex: auto;
  display: flex;
  flex-flow: column nowrap;
  padding: 13px 13px 13px 30px;
  overflow: hidden;
}

.progressContainer {
  width: 100%;
  position: relative;
  padding: 3px 0;
}

.progressContent {
  position: relative;
  height: 16px;
  padding: 5px 0;
  width: 100%;
}
.progress {
  height: 100%;
}

.barTransition {
  transition-property: transform;
  transition-timing-function: ease-out;
  transition-duration: 0.2s;
}
.timeLabel {
  width: 100%;
  height: 18px;
  display: flex;
  span {
    font-size: 13px;
  }
}
.status {
  flex: auto;
}

.playControl {
  flex: none;
  // height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 13px 20px;
  align-content: center;
  flex-wrap: nowrap;
  flex-direction: row;
  color: var(--color-button-font);
}
.playBtn {
  height: 40%;
  padding: 5px;
  cursor: pointer;
  flex: none;
  // transition: @transition-normal;
  // transition-property: color;
  color: var(--color-button-font);
  transition: opacity 0.2s ease;
  opacity: 1;
  cursor: pointer;

  +.playBtn {
    margin-left: 10px;
  }
  svg {
    fill: currentColor;
    filter: drop-shadow(0 0 1px rgba(0, 0, 0, 0.2));
  }
  &:hover {
    opacity: 0.8;
  }
  &:active {
    opacity: 0.6;
  }
}

</style>
