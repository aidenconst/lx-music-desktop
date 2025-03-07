<template>
  <div ref="dom_menu" :class="$style.menu">
    <ul :class="$style.list" role="toolbar">
      <li v-for="item in menus" :key="item.to" :class="$style.navItem" role="presentation">
        <router-link :class="[$style.link, {[$style.active]: $route.meta.name == item.name}]" role="tab" :aria-selected="$route.meta.name == item.name" :to="item.to" :aria-label="item.tips">
          <!-- <svg version="1.1" xmlns="http://www.w3.org/2000/svg" xlink="http://www.w3.org/1999/xlink" :viewBox="item.iconSize" :height="item.size" :width="item.size" space="preserve"> -->
            {{ item.tips }}
            <use :xlink:href="item.icon" />
          <!-- </svg> -->
        </router-link>
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import { appSetting } from '@renderer/store/setting'
import { useI18n } from '@root/lang'
import { ref, computed } from '@common/utils/vueTools'
import { useIconSize } from '@renderer/utils/compositions/useIconSize'

export default {
  name: 'NavBar',
  setup() {
    const t = useI18n()
    const dom_menu = ref<HTMLElement>()
    const iconSize = useIconSize(dom_menu, 0.32)

    const menus = computed(() => {
      const size = iconSize.value
      return [
        // {
        //   to: '/search',
        //   tips: t('search'),
        //   icon: '#icon-search-2',
        //   iconSize: '0 0 425.2 425.2',
        //   size,
        //   name: 'Search',
        //   enable: true,
        // },
        {
          to: '/songList/list',
          tips: t('song_list'),
          icon: '#icon-album',
          iconSize: '0 0 425.2 425.2',
          size,
          name: 'SongList',
          enable: true,
        },
        {
          to: '/leaderboard',
          tips: t('leaderboard'),
          icon: '#icon-leaderboard',
          iconSize: '0 0 425.22 425.2',
          size,
          name: 'Leaderboard',
          enable: true,
        },
        {
          to: '/list',
          tips: t('my_list'),
          icon: '#icon-love',
          iconSize: '0 0 444.87 391.18',
          size,
          name: 'List',
          enable: true,
        },
        {
          to: '/download',
          tips: t('download'),
          icon: '#icon-download-2',
          iconSize: '0 0 425.2 425.2',
          size,
          enable: appSetting['download.enable'],
          name: 'Download',
        },
        // {
        //   to: '/setting',
        //   tips: t('setting'),
        //   icon: '#icon-setting',
        //   iconSize: '0 0 493.23 436.47',
        //   size,
        //   enable: true,
        //   name: 'Setting',
        // },
      ].filter(m => m.enable)
    })
    return {
      appSetting,
      menus,
      dom_menu,
    }
  },
}
</script>

<style lang="less" module>
@import '@renderer/assets/styles/layout.less';

.menu {
    gap: 1vh;
    justify-content: center;
    flex-grow: 1;
    display: flex;
    justify-content: center;
    height:100%;
    width:64%;
    align-items: center;
    font-weight: bold;
}
.list {
  display: flex;
    letter-spacing: 0.25rem;
    text-decoration: none;
    // color: #333;
    -webkit-app-region: no-drag;
    font-size:inherit;
    font-weight: 700;
    font-size:1.2em;
    // border-radius: 0.375rem;
    transition: 0.2s;
    text-align: center;
    gap:10px;
    -webkit-user-drag: none;
    align-content: center;
    flex-wrap: nowrap;
    flex-direction: row;
    justify-content: center;
    align-items: center;
  .navItem{
    padding:8px 16px;
    & a{
      text-decoration:none;
    }
    &:before {
      content: '';
      display: block;
      width: 100%;
  }
  }
}
.link {
  &.active {
    // border-left-color: @color-theme-active;
    // background-color: var(--color-primary-light-300-alpha-700);
    color: var(--color-theme);
    text-decoration:none;
    &:before {
      transform: translateX(0);
    }

    &:hover {
      color: var(--color-primary-alpha-200);
    }
  }


  &:hover {
    color: var(--color-nav-font);

    &:not(.active) {
      opacity: .9;
      color: var(--color-primary-alpha-200);
    }
  }
  &:active:not(.active) {
    opacity: .8;
    color: var(--color-primary-alpha-200);
  }
}

// .icon {
//   // margin-bottom: 5px;
//   &> svg {
//     width: 32%;
//   }
// }

</style>
