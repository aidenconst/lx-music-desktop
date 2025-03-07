<template>
  <div :class="$style.player">
    <div :class="$style.progress">
      <common-progress-bar v-if="!isShowPlayerDetail" :class-name="$style.progressBar" :progress="progress" :handle-transition-end="handleTransitionEnd" :is-active-transition="isActiveTransition" />
    </div>
    <div :class="$style.picContent" :aria-label="$t('player__pic_tip')" @contextmenu="handleToMusicLocation" @click="showPlayerDetail">
      <img v-if="musicInfo.pic" :src="musicInfo.pic" decoding="async" @error="imgError">
      <div v-else :class="$style.emptyPic">L<span>X</span></div>
    </div>
    <div :class="$style.infoContent">
      <div :class="$style.title" :aria-label="title + $t('copy_tip')" @click="handleCopy(title)">
        {{ title }}
      </div>
      <div :class="$style.status">{{ statusText }}</div>
    </div>
    <!-- <play-progress /> -->
    <div :class="$style.playBtnContent">
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
    <control-btns />
    <div :class="$style.timeContent">
      <span>{{ nowPlayTimeStr }}</span>
      <span style="margin: 0 1px;">/</span>
      <span>{{ maxPlayTimeStr }}</span>
    </div>
  </div>
</template>

<script>
import { computed } from '@common/utils/vueTools'
import { useRouter } from '@common/utils/vueRouter'
import { clipboardWriteText } from '@common/utils/electron'
import ControlBtns from './ControlBtns.vue'
// import PlayProgress from './PlayProgress'
import usePlayProgress from '@renderer/utils/compositions/usePlayProgress'
// import { lyric } from '@renderer/core/share/lyric'
import {
  statusText,
  musicInfo,
  isShowPlayerDetail,
  isPlay,
  playInfo,
  playMusicInfo,
} from '@renderer/store/player/state'
import {
  setMusicInfo,
  setShowPlayerDetail,
} from '@renderer/store/player/action'
import { appSetting } from '@renderer/store/setting'
import { togglePlay, playNext, playPrev } from '@renderer/core/player'
import { LIST_IDS } from '@common/constants'

export default {
  name: 'CorePlayBar',
  components: {
    ControlBtns,
    // PlayProgress,
  },
  setup() {
    const router = useRouter()

    const {
      nowPlayTimeStr,
      maxPlayTimeStr,
      progress,
      isActiveTransition,
      handleTransitionEnd,
    } = usePlayProgress()

    const showPlayerDetail = () => {
      if (!playMusicInfo.musicInfo) return
      setShowPlayerDetail(true)
    }
    const handleCopy = (text) => {
      clipboardWriteText(text)
    }

    const imgError = () => {
      // console.log(e)
      setMusicInfo({ pic: null })
    }

    const handleToMusicLocation = () => {
      const listId = playMusicInfo.listId
      if (!listId || listId == LIST_IDS.DOWNLOAD || !playMusicInfo.musicInfo) return
      if (playInfo.playIndex == -1) return
      void router.push({
        path: '/list',
        query: {
          id: listId,
          scrollIndex: playInfo.playIndex,
        },
      })
    }

    const title = computed(() => {
      return musicInfo.name
        ? appSetting['download.fileName'].replace('歌名', musicInfo.name).replace('歌手', musicInfo.singer)
        : ''
    })

    // onBeforeUnmount(() => {
    // window.eventHub.emit(eventPlayerNames.setTogglePlay)
    // })

    return {
      musicInfo,
      nowPlayTimeStr,
      maxPlayTimeStr,
      progress,
      isActiveTransition,
      handleTransitionEnd,
      handleCopy,
      imgError,
      statusText,
      title,
      showPlayerDetail,
      isPlay,
      togglePlay,
      playNext,
      playPrev,
      handleToMusicLocation,
      isShowPlayerDetail,
    }
  },
}
</script>


<style lang="less" module>
@import '@renderer/assets/styles/layout.less';

.player {
  position: relative;
  height: @height-player;
  // border-top: 1px solid var(--color-primary-alpha-900);
  box-sizing: border-box;
  display: flex;
  flex-flow: row nowrap;
  align-items: center;
  contain: strict;
  padding: 10px 30px 8px 30px;
  z-index: 2;
  box-shadow: 0px -2px 10px var(--color-primary-alpha-800);
  // box-shadow: 0px 0px 4px rgba(0, 0, 0, 0.1);
  * {
    box-sizing: border-box;
  }

  &:before {
    .mixin-after;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-color: var(--color-main-background);
    // opacity: .1;
    background: rgba(255, 255, 255, 0.1); /* 白色半透明遮罩 */
    -webkit-backdrop-filter: blur(10px);
    backdrop-filter: blur(10px);
    z-index: -1;
  }
}
.progress {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  padding-bottom: 0px;
  // background-color: var(--color-primary);
  height: 8px;
  .progressBar {
    height: 2px;
    border-radius: 0;
  }
}

.picContent {
  height: 100%;
  aspect-ratio: 1 / 1;
  width:7%;
  // color: var(--color-primary);
  // transition: @transition-normal;
  // transition-property: color;
  flex: none;
  opacity: 1;
  transition: opacity @transition-fast;
  // transition-property: opacity;
  display: flex;
  justify-content: center;
  // align-items: center;
  cursor: pointer;

  &:hover {
    opacity: .8;
  }

  // svg {
  //   fill: currentColor;
  // }
  img {
    box-shadow: 0 0 2px rgba(0, 0, 0, 0.3);
    max-width: 100%;
    max-height: 100%;
    transition: @transition-normal;
    transition-property: border-color;
    // border-radius: 50%;
    border-radius: @radius-border;
    // border: 2px solid @color-theme_2-background_1;
  }

  .emptyPic {
    background-color: var(--color-primary-light-900-alpha-200);
    border-radius: @radius-border;
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    color: var(--color-primary-light-400-alpha-200);
    user-select: none;
    font-size: 20px;
    font-family: Consolas, "Courier New", monospace;

    span {
      padding-left: 3px;
    }
  }
}

.infoContent {
  padding-left: 10px;
  // flex: auto;
  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
  align-items: flex-start;
  font-size: 13px;
  color: var(--color-font);
  min-width: 0;
  line-height: 1.5;
  width:22%;
}

.title {
  max-width: 100%;
  font-size: 18px;
  color: var(--color-800);
  .mixin-ellipsis-1;
}
.status {
  flex: auto;
  padding-top: 3px;
  height: 23px;
  .mixin-ellipsis-1;
  max-width: 100%;
  color: var(--color-400);
}

.timeContent {
  display:flex;
  flex: none;
  color: var(--color-550);
  font-size: 15px;
  padding-left: 10px;
  width: 10%;
  justify-content: flex-end;
}

.playBtnContent {
  height: 100%;
  flex: auto;
  display: flex;
  flex-flow: row nowrap;
  align-items: center;
  padding-left: 10px;
  padding-right: 15px;
  gap: 18px;
  width:40%;
  justify-content: center;
}

.playBtn {
  flex: none;
  // height: 70%;
  // margin-top: -2px;
  transition: @transition-fast;
  transition-property: color, opacity;
  color: var(--color-button-font);
  opacity: 1;
  cursor: pointer;
  opacity: 0.6;
  svg {
    fill: currentColor;
    filter: drop-shadow(0 0 1px rgba(0, 0, 0, 0.2));
  }
  &:hover {
    opacity: 0.9;
  }
  &:active {
    opacity: 0.7;
  }
}

</style>
