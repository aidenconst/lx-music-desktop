<template>
  <div :class="$style.container">
    <div v-show="!props.listInfo.noItemLabel" ref="dom_list_ref" :class="$style.listContent" class="scroll">
      <ul>
        <li v-for="item in props.listInfo.list" :key="item.id" :class="$style.item" @click="toDetail(item)">
          <div :class="$style.cover">
            <div :class="$style.image">
              <img :class="$style.img" loading="lazy" decoding="async" :src="item.img">
            </div>
          </div>
          <div :class="$style.desc">
            <h4>{{ item.name }}</h4>
            <div>
              <p :class="$style.author">{{ item.author }}</p>
              <!-- <p v-if="item.time" :class="$style.time">{{ item.time }}</p> -->
              <div :class="$style.covermask"></div>
              <div :class="$style.songlist_info">
                <span v-if="item.total != null"><svg-icon name="music" />{{ item.total }}</span>
                <span v-if="item.play_count != null"><svg-icon name="headphones" />{{ item.play_count }}</span>
                <span v-if="visibleSource">{{ item.source }}</span>
              </div>
              <div :class="$style.songlist_info"></div>
            </div>
          </div>
        </li>
        <li v-for="(i, index) in 6" :key="index" :class="$style.item" style="margin-bottom: 0;height: 0;" />
      </ul>
      <div :class="$style.pagination">
        <material-pagination :count="props.listInfo.total" :limit="props.listInfo.limit" :page="props.listInfo.page" @btn-click="togglePage" />
      </div>
    </div>
    <transition enter-active-class="animated fadeIn" leave-active-class="animated fadeOut">
      <div v-show="props.listInfo.noItemLabel" :class="$style.noitem">
        <p v-text="props.listInfo.noItemLabel" />
      </div>
    </transition>
  </div>
</template>

<script setup lang="ts">
import { ref } from '@common/utils/vueTools'
import type { ListInfo, ListInfoItem } from '@renderer/store/songList/state'
import { useRoute, useRouter } from '@common/utils/vueRouter'


const props = withDefaults(defineProps<{
  listInfo: ListInfo
  visibleSource?: boolean
}>(), {
  visibleSource: false,
})

const router = useRouter()
const route = useRoute()

const dom_list_ref = ref<HTMLElement | null>(null)

const emit = defineEmits(['toggle-page'])


const togglePage = (page: number) => {
  emit('toggle-page', page)
}

const toDetail = (info: ListInfoItem) => {
  void router.push({
    path: '/songList/detail',
    query: {
      source: info.source,
      id: info.id,
      picUrl: info.img,
      fromName: route.name as string,
    },
  })
}

defineExpose({
  scrollTo(top: number) {
    dom_list_ref.value?.scrollTo({
      top,
      // behavior: 'smooth',
    })
  },
  getScrollTop() {
    return dom_list_ref.value?.scrollTop ?? 0
  },
})


</script>


<style lang="less" module>
@import '@renderer/assets/styles/layout.less';
.container {
  overflow: hidden;
  height: 100%;
  display: flex;
  flex-flow: column nowrap;
  position: relative;
  padding-bottom: 100px;
}

.listContent {
  position: absolute;
  left: 0;
  top: 10px;
  width: 100%;
  height: 100%;
  display: flex;
  flex-flow: column nowrap;
  font-size: 14px;
  box-sizing: border-box;
  padding: 10px 15px 90px 15px;
  overflow-y: scroll; /* 允许垂直滚动 */
  overflow-x: hidden; /* 隐藏水平滚动条 */
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none; /* Internet Explorer 和 Edge */
  ul {
    width: 100%;
    display: grid;
    grid-template-columns: repeat(5, minmax(0px, 1fr));
    gap: 20px;
  }
}
.item {
    position: relative;
    height: auto;
    border-radius: 16px;
    z-index: 0;
    transition: background-color 0.3s, transform 0.3s;
    cursor: pointer;
    transition: 0.3s ease-in-out; /* 添加过渡效果 */
    &:hover{
      opacity: .9;
      background-color:var(--color-primary-dark-100-alpha-900);
      box-shadow:0 0 12px var(--color-primary-dark-100-alpha-700);
      .image{
        transform: scale(1.1); /* 放大到1.5倍大小 */
      }
    }
  // &:hover {
  //   // opacity: .7;
  //   // transform: translateY(-5px);  /* 上移5px */
  //   box-shadow: 7px 12px 15px -3px var(--color-primary-dark-100-alpha-900);
  // }
}
.cover{
  position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    aspect-ratio: 1 / 1;
    border-radius: 16px;
    overflow: hidden;
    box-shadow: 0px 0px 4px 2px rgba(0, 0, 0, 0.1);
    transition: border-radius 0.3s, box-shadow 0.3s;
}

.image {
  transition: filter 0.3s, transform 0.3s;
  position: relative;
    width: 100%;
    height: 100%;
}

.img {
  width: 100%;
  height: 100%;
  transition: opacity 0.35s ease-in-out;
}
.desc {
  display: flex;
    flex-direction: column;
    padding: 12px;
  h4 {
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 2; /* 限制显示两行 */
    overflow: hidden;
    text-overflow: ellipsis; /* 超出部分显示省略号 */
    line-height: 1.3; /* 设置行高，此处为字体大小的1.5倍 */
    height: 2.6em; /* 两行的高度（1.5 * 2 = 3em） */
  }
}
.description{
  position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    padding: 40px 60px 12px 12px;
    background: linear-gradient(rgba(0, 0, 0, 0), rgba(0, 0, 0, 0.6));
    transform: translateY(100%);
    transition: transform 0.3s;
}
.covermask{
  position: absolute;
    top: 0;
    left: 0;
    height: 30%;
    width: 100%;
    border-radius: 16px;
    background: linear-gradient(rgba(0, 0, 0, 0.3), rgba(0, 0, 0, 0));
}
.description{
  position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    padding: 40px 60px 12px 12px;
    background: linear-gradient(rgba(0, 0, 0, 0), rgba(0, 0, 0, 0.6));
    transform: translateY(100%);
    transition: transform 0.3s;
}
.songlist_info {
  position: absolute;
    display: flex;
    align-items: center;
    top: 10px;
    right: 12px;
    color: #fff;
    font-weight: bold;
    z-index: 2;
    font-size:14px;
}
.author {
  margin-top: 6px;
  font-size: 14px;
  .mixin-ellipsis-1;
  text-align: justify;
  line-height: 1.3;
  // text-indent: 24px;
  color: var(--color-font-label);
}
.time {
  position: absolute;
    display: flex;
    align-items: center;
    top: 10px;
    left: 12px;
    font-size:12px;
    color: #fff;
    font-weight: bold;
    z-index: 2;
  // margin-top: 3px;
  // font-size: 12px;
  // .mixin-ellipsis-1;
  // text-align: justify;
  // line-height: 1.3;
  // // text-indent: 24px;
  // color: var(--color-font-label);
}
.pagination {
  text-align: center;
  padding: 15px 0;
  div{
    ul{
      grid-template-columns: repeat(10, minmax(0px, 0.8fr));
    }
  }
  // left: 50%;
  // transform: translateX(-50%);
}
.noitem {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
  align-items: center;
  // background-color: var(--color-000);

  p {
    font-size: 24px;
    color: var(--color-font-label);
  }
}

</style>
